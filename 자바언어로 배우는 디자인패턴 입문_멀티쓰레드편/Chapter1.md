# 🍒 Chapter 1

- synchronized 메소드를 호출하는 데에는 보통의 메소드를 호출 할 때보다 시간이 더 걸린다
    
    → 수행 능력이 떨어짐.
    
- 자바 컬렉션 클래스의 대부분은 쓰레드 세이프한 상태가 아니다.
    
    → 안정성을 지킬 필요가 없는 경우에 고속으로 동작할 수 있도록 하기 위한 것
    
- synchronized 메소드나 synchronized블록은 return을 하든지 예외가 통보되더라도 확실하게 락을 해제해준다.

## Single Threaded Execution 패턴

: unsafeMethod를 동시에 단 한 개의 쓰레드밖에 액세스 하지 못하도록 가드한다 → synchronized 이용

- SharedResource 역할의 인스턴스에 복수의 쓰레드가 액세스할 가능성이 있을 때 사용.
- SharedResource의 상태가 변화하는 것을 막기 위해서 사용
- 안정성 확보 ( 안정성 = 객체가 망가지는 것 , 설계자의 의도와는 다른 상태가 되는 것 )
- 데드락의 위험성 존재
- 해당 클래스를 상속할 경우 서브 클래스에 의해 안정성이 무너질 가능성이 존재
- 수행능력은 떨어진다 ( 수행 능력 = 처리를 고속, 대략으로 할 수 있는 것 )
    - 락을 취득하는데 시간이 걸린다.
    - 쓰레드의 충돌로 대기하게 된다.
        
        크리티컬 섹션에서 처리를 실행하고 있는 동안에는 크리티컬 섹션에 들어가려고 했던 다른 쓰레드는 블록한다. (쓰레드의 충돌)
        
- long과 double를 쓰레드 사이에 공유하는 경우, synchronized 안에 넣든지 volatile 선언한다.

### 보강 chapter

- 계수 세마포어 : ‘ 최대 N개의 쓰레드’까지 실행할 수 있도록 설정
    - 생성자 : 리소스의 수 지정
    - acquire 메소드 : 리소스를 확보하는 메소드 → 빈자리 발생시 쓰레드가 들어올 수 있게 함
    - release 메소드 : 리소스를 해제하는 메소드

### 연습문제

thread 메소드

- Thread.sleep() : 주어진 시간동안 일시 정지
- Thread.yield() : 다른 스레드에게 실행 양보
    
    yield() 메소드를 호출한 스레드는 실행 대기 상태로 돌아가고, 동일한 우선순위 또는 높은 우선순위를 갖는 다른 스레드가 실행 기회를 가질 수 있도록 한다. 
    
- Thread.join() : 해당 쓰레드가 계산 작업을 모두 마칠 때까지 일시 정지에 있다가 최종 계산된 결과값을 산출하고 종료하면 결과값을 받아 출력하도록 한다. (쓰레드의 종료 체크)

### Hashtable vs ConcurrentHashMap ( 추가 공부 )

- Hashtable
    - 모든 메소드를 Single Threaded Execution으로 한다.
    - 동기화를 위해 synchronized 키워드를 이용해서 메소드 전체에 락을 검
- ConcurrnetHashMap
    - 내부의 데이터 구조를 분할함으로써 구조적으로 간섭하지 않는 쓰레드 사이에 배타제어가 일어나지 않게 한다.
        
        각각의 bucket별로 동기화를 진행하기에 다른 bucket에 속해 있을 경우, 별도의 lock 없이 운용한다. 
        
        각 Table bucket을 독립적으로 잠그는 방식
        
        ex) put 메소드 
        
        - 빈 Hash Bucket에 노드를 사용할 경우 : lock 사용하지 않고, compare and swap을 사용
        
        <aside>
        💡 💡 여러 쓰레드가 접근하면? : CAS 알고리즘으로 다시 한번 확인한다. 
        즉, bucket 값이 비어있을 경우 volatile 변수에 접근(가시성 보장)하여 기대값(null)과 일치하는지 한 번 더 확인하여 일치할 경우에만 노드를 생성한다(원자성 보장). 이와 같이 동시성을 처리함으로 Thread-safety를 보장한다
        
        CAS 알고리즘 : CAS 알고리즘은 현재 스레드가 가지고 있는 기존값과 메모리가 가지고 있는 값을 비교해 같은 경우 변경할 값을 메모리에 반영하고 true를 반환한다. 
        
        참고)
        [https://yeoonjae.tistory.com/entry/Java-ConcurrentHashMap에-대해-알아보자작성중](https://yeoonjae.tistory.com/entry/Java-ConcurrentHashMap%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90%EC%9E%91%EC%84%B1%EC%A4%91)
        [https://applefarm.tistory.com/100](https://applefarm.tistory.com/100)
        
        </aside>
        
        - 이미 Bucket에 노드가 존재할 경우 : `synchronized` 을 이용해 하나의 thread만 접근하도록 제어한다. 서로 다른 thread가 같은 hash bucket에 접근할 때만 해당 block이 lock된다.
            
            ![Untitled](https://user-images.githubusercontent.com/68679529/231982558-a8c07559-a7cb-4523-9c40-3e599c91c5e4.png)
            
        
    - Hashtable 과는 다르게, 주요 method 에 `synchronized`  키워드가 선언되어 있진 않다.
        
        ex) get메소드
        
        ConcurrentHashMap에서의 get을 살펴보면, `synchroized` keyword를 발견할 수 없다
        
    참고)

    [https://pplenty.tistory.com/17](https://pplenty.tistory.com/17)
    
    [https://velog.io/@alsgus92/ConcurrentHashMap의-Thread-safe-원리](https://velog.io/@alsgus92/ConcurrentHashMap%EC%9D%98-Thread-safe-%EC%9B%90%EB%A6%AC)