# gc

![img.png](gc.png)

eden 영역 : 처음 객체가 생성되어 할당되는 공간

survivor 영역 : 마이너 gc가 일어날 때, eden 혹은 다른 survivor 영역에서 이동됨. 각 객체는 age를 갖고 있음.,

old generation: survivor영역에서 일정 임계값(age)을 넘으면, old generation으로 이동된다. (이걸 promoted라고 함)
임계값은 `MaxTenuringThreshold`로 설정할 수 있다. g1gc와 패러럴 GC는 기본이 15이다.


minor gc : eden 영역이 꽉 찬 경우 발생
major gc(full gc) : old generation 영역이 부족한 경우 발생

Stop the world

mark and sweep (compact)

young generation : 새로운 객체들이 할당되는 영역