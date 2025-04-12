## Hooks의 개념과 useState, useEffect
### Hooks
* state 관련 함수
* Lifecycle 관련 함수
* 최적화 관련 함수
* use로 시작
#### useState
* state를 사용하기 위한 Hook
#### useEffect
* Side effect를 수행하기 위한 Hook
* 다른 컴포넌트에 영향을 미칠 수 있으며, 렌더링 중에는 작업이 완료될 수 없기 때문 리액트의 함수 컴포넌트에서 Side effect를 실행할 수 있게 해주는 Hook
* 검포넌트가 마운트 된 이후,
* 의존성 배열이 있는 변수들 중 하나라도 값이 변경될 때 실행
* return - 컴퐅넌트가 마운트 해제되기 전데 실행
#### useMemo
* Memoized value를 리턴하는 Hook
#### useCallback
* 값이 아닌 함수를 반환
#### useRef
* Reference를 사용하기 위한 Hook
* 특정 컴포넌트에 접근할 수 있는 객체
* 내부의 데이터 변경시 반환이 필요시 Callback ref
### Hook의 규칙과 Custom Hook 만들기
* Hook은 컴포넌트가 렌더링될 때마다 매번 같은 순서로 호출되어야 한다.
* 리액트 함수 컴포넌트에서만 Hook을 호출해야 한다.
#### Custom Hoot
* 반복되는 로직을 Hook으로
* Custom Hook의 이름은 꼭 use로 시작
* 컴포넌트 내부에 있는 모든 state와 effects는 전부 분리