# g1gc

garbage first gc라는 용어다.

기존 GC 구조와 달리 힙을 여러 리전으로 나눠, 'young, old, humongous, eden, survivor' 영역으로 관리한다.


## 영역

- Young 
  - 새로운 객체가 할당되는 영역. 
  - Eden 영역
    - 최초 생성된 객체가 할당된다.
  - Survivor 영역
    - 
- Old
  - Young에서 오래 살아남은 객체가 할당된다.
- Humongous
  - 매우 큰 객체를 저장하기 위한 영역


## GC