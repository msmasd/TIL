Garbage Collector

System.gc()를 사용하면 가비지컬렉터가 실행된다.

가비지 컬렉터가 실행되면 gc를 실행하는 쓰레드 외 모든 쓰레드는 정지된다(stop-the-world)

가비지 컬렉터의 전제
1. 대부분의 객체는 금방 접근 불가능 상태가 된다(unreachable)
2. 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

이 전재를 위해 hotstop VM에서는 물리적으로 공간을 나누었다. Young, Old로
Young(Young generation) 영역: 새롭게 생성한 객체 대부분이 이 영역에 위치.
매우많은 새로운 객체들이 여기 있다가 금방 사라진다. 이 영역에서 객체가 사라질때 minor GC가 발생한다고 한다.

Old(Old generation) 영역: 접근 불가능 상태가 되지않아 Young영역에서 살아남은 객체가 이 영역으로 복사된다. 대부분 Young영역보다 크게 할당하며, 크기가 큰 만큼 Young영역보다 GC는 적게 발생한다. 이 영역에서 객체가 사라질때 major GC(혹은 full GC)라고 한다.

Permanent Generation 영역(Pern 영역) 은 method Area라고 한다. 객체나 억류(intern)된 문자열이 여기있고 여기서도 GC가 발생하는데 major GC카운트에 포함된다.

Young 영역은 Eden과 두개의 survivor영역으로 이루어진다. 
* 처음 객체가 생성되면 eden영역으로 쌓이고
* 한번 gc가 일어나도 살아있는 객체는 하나의 survivor영역으로 옮겨진다.
* 한 survivor영역이 다 차면 다른 survivor영역으로 옮겨진다.
* 이런 과정을 통하다가 Old영역으로 옮겨진다.

Old 영역 GC
1. Serial GC
2. Parallel GC
3. Parallel Old GC
4. Concurrent Mark & Sweep GC
5. G1(Garbage First) GC

참고내용 https://d2.naver.com/helloworld/1329
