# 🍒 Chapter 2

## Immutable 패턴

- 상태가 절대 변하지 않는다.
- 필드 값을 변경할 수도 없고 필드 내용을 변경하는 메소드도 가지고 있지 않은 클래스
    
    → 복수 쓰레드 사이에 read-read라는 상황밖에 발생하지 않는다. 
    
- synchronized를 사용하지 않아도 된다 → 안전성, 생존성 유지하면서 수행능력 높일 수 있다.
- 예시 클래스 ) String, BigInteger, Decimal, Pattern, 기본형 랩형 클래스 (Integer,Short,Char,,,)
- 적용 경우
    - 인스턴스 생성 후 상태가 변하지 않는 것이 필요할 때
        
        → final으로 선언, setter 메소드가 존재하지 않아야 한다.
        
        → final으로 선언, setter 메소드가 존재하지 않아도 immutable이 아닐 수 있다. 필드 값 자체는 변하지 않더라도 필드가 참조하고 있는 곳의 인스턴스가 변할 수 있다. 
        
- String vs StringBuilder vs StringBuffer
    - String : Immutable한 클래스
        - 문자열 수정 불가
    - StringBuffer : mutable한 클래스
        - 문자열을 자유롭게 수정가능 → StringBuffer 클래스는 String 클래스와 달리 내부상태를 변경하는 메소드를 가지고 있다.
        - 수정될때 synchronize가 적절히 사용
    
    ### 🍎 추가 공부
    <img width="681" alt="스크린샷 2023-04-14 오후 5 11 05" src="https://user-images.githubusercontent.com/68679529/231985114-fd783ffa-ff17-45e4-b817-67565773ffc4.png">
    

### ArrayList 클래스 : 스레드 세이프가 아닌 컬렉션

스레드 세이프를 위해선?

1) synchronizedList 메소드를 사용하여 동기화 : 스레드 세이프 확보됨.

2) CopyOnWriteArrayList

- 컬렉션에 대하여 쓰기 조작을 하면 내부에 확보된 배열을 통째로 복사한다. → 시간 소요
- 읽어가는 도중에 요소가 변경되지 않는다.
- 쓰기가 별로 없고 읽기가 빈번하게 일어나는 경우 사용