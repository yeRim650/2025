**Python 애플리케이션 ELK 로깅 가이드라인**

---

## 목차

1. [개요](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B0%9C%EC%9A%94)
2. [공통 준비사항](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B3%B5%ED%86%B5-%EC%A4%80%EB%B9%84%EC%82%AC%ED%95%AD)
3. [방법 A: `python-json-logger` + `SocketHandler`](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EB%B0%A9%EB%B2%95-a-python-json-logger--sockethandler)
4. [방법 B: `python-logstash` 라이브러리](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EB%B0%A9%EB%B2%95-b-python-logstash-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC)
5. [테스트 & 검증 체크리스트](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%ED%85%8C%EC%8A%A4%ED%8A%B8--%EA%B2%80%EC%A6%9D-%EC%B2%B4%ED%81%AC%EB%A6%AC%EC%8A%A4%ED%8A%B8)
6. [결론 & 선택 팁](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B2%B0%EB%A1%A0--%EC%84%A0%ED%83%9D-%ED%8C%81)

---

## 개요

이 가이드라인은 Python 애플리케이션에서 ELK 스택(Logstash → Elasticsearch → Kibana)으로 **구조화된 JSON 로그**를 전송하는 두 가지 방법을 정리한 문서입니다.

- **방법 A**: 표준 로깅 포맷터(`python-json-logger`) + `SocketHandler`
- **방법 B**: Logstash 전용 핸들러(`python-logstash`)

각 방법별 설치, 설정, 실행 예제와, POC 환경(도커 컴포즈)을 포함합니다.

---

## 공통 준비사항

1. **ELK Docker Compose** (`docker-compose.yml`)
    
    ```yaml
    version: '3.8'
    services:
      elasticsearch:
        image: docker.elastic.co/elasticsearch/elasticsearch:8.6.3
        environment:
          - discovery.type=single-node
          - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        ports:
          - "9200:9200"
      logstash:
        image: docker.elastic.co/logstash/logstash:8.6.3
        ports:
          - "5000:5000"
        volumes:
          - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
        depends_on:
          - elasticsearch
      kibana:
        image: docker.elastic.co/kibana/kibana:8.6.3
        environment:
          - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
        ports:
          - "5601:5601"
        depends_on:
          - elasticsearch
    
    ```
    
    ```bash
    # ELK 스택 기동
    docker-compose up -d
    
    ```
    
2. **Logstash 파이프라인** (`logstash.conf`)
    
    ```
    input {
      tcp { port => 5000 codec => json }
    }
    filter {
      # (선택) timestamp 변환, 필드 정제 등
    }
    output {
      elasticsearch {
        hosts => ["elasticsearch:9200"]
        index => "python-app-logs-%{+YYYY.MM.dd}"
      }
      stdout { codec => rubydebug }  # 개발 시 콘솔 디버그용
    }
    
    ```
    

---

## 방법 A: `python-json-logger` + `SocketHandler`

1. **의존성 설치**
    
    ```bash
    pip install python-json-logger
    
    ```
    
2. **로깅 설정 스크립트** (`app_a.py`)
    
    ```python
    import logging
    import logging.config
    from pythonjsonlogger import jsonlogger
    
    LOGSTASH_HOST = 'localhost'
    LOGSTASH_PORT = 5000
    
    LOGGING = {
        'version': 1,
        'disable_existing_loggers': False,
        'formatters': {
            'json': {
                '()': 'pythonjsonlogger.jsonlogger.JsonFormatter',
                'fmt': '%(asctime)s %(levelname)s %(name)s %(message)s %(pathname)s %(lineno)d'
            },
        },
        'handlers': {
            'logstash': {
                'class': 'logging.handlers.SocketHandler',
                'host': LOGSTASH_HOST,
                'port': LOGSTASH_PORT,
                'formatter': 'json'
            },
            'console': {
                'class': 'logging.StreamHandler',
                'formatter': 'json'
            },
        },
        'root': {
            'level': 'INFO',
            'handlers': ['logstash', 'console']
        },
    }
    
    logging.config.dictConfig(LOGGING)
    logger = logging.getLogger(__name__)
    
    if __name__ == '__main__':
        logger.info('Method A: JSON logger 시작', extra={'service': 'python-json-logger'})
        try:
            1 / 0
        except ZeroDivisionError:
            logger.exception('에러 발생!')
    
    ```
    
3. **실행 & 확인**
    
    ```bash
    python app_a.py
    
    ```
    
    - Logstash 로그(`docker logs logstash`)와 Kibana Discover에서 JSON 필드를 확인하세요.

---

## 방법 B: `python-logstash` 라이브러리

1. **의존성 설치**
    
    ```bash
    pip install python-logstash
    
    ```
    
2. **로깅 설정 스크립트** (`app_b.py`)
    
    ```python
    import logging
    import logstash
    
    LOGSTASH_HOST = 'localhost'
    LOGSTASH_PORT = 5000
    
    logger = logging.getLogger('python-logstash-logger')
    logger.setLevel(logging.INFO)
    handler = logstash.TCPLogstashHandler(LOGSTASH_HOST, LOGSTASH_PORT, version=1)
    logger.addHandler(handler)
    
    if __name__ == '__main__':
        logger.info('Method B: logstash-logger 시작', extra={'service': 'python-logstash'})
        try:
            {}['key']
        except Exception:
            logger.exception('B 방식에서 예외!')
    
    ```
    
3. **실행 & 확인**
    
    ```bash
    python app_b.py
    
    ```
    
    - Logstash 및 Kibana에서 로그 수신 여부를 검증하세요.

---

## 테스트 & 검증 체크리스트

- [ ]  `docker-compose ps` 로 Elasticsearch, Logstash, Kibana 모두 정상 기동 확인
- [ ]  `app_a.py`, `app_b.py` 각각 실행 시 Logstash가 JSON 수신 오류 없이 처리
- [ ]  Kibana Discover에 `python-app-logs-*` 인덱스 패턴 생성 후 필드별 검색•필터링
- [ ]  필요 시 Grafana 연동, 대시보드 생성 테스트

---

## 결론 & 선택 팁

- **포맷 완전 제어**가 필요하거나, **다양한 핸들러 조합**을 쓸 계획이면 → **방법 A**
- **최소 설정**으로 즉시 ELK 파이프라인과 호환되는 JSON/GELF 로그를 보내고 싶다면 → **방법 B**

두 방식을 직접 비교해 보시고,

- “어떤 필드를 얼마나 자주 커스터마이즈해야 하나?”
- “파이프라인(grok/filter) 설정 부담을 얼마나 줄이고 싶나?”