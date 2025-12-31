# Blocking I/O VS Non-Blocking I/O

프로그래밍에서의 입출력(I/O) 작업은 다양한 접근 방식을 통해 처리됩니다.

Block vs. Non-Block, 그리고 Sync vs. Async의 다양한 I/O 처리 방식을 알아보고, 각각의 특징과 사용 사례에 대해 알아보겠습니다.

둘의 차이는 다른 주체가 작업할 때 물리적으로 자신의 제어권이 있는지가 기준입니다. (물리적인 실행 특성)

## Blocking IO

<img width="800px" src="./img/blocking.png">

1. I/O 작업을 처리하기 위해 User Level에 있던 Application이 시스템 함수를 호출한다(→ system call).
   - 이 때 context-switching 이 발생한다.
2. Kernel Level에서 해당 I/O 작업 진행, I/O가 끝날 때까지 유저 프로세스 대기
   - 끝나기 전에는 함수가 반환되지 않기 때문에 커널이 작업을 완료하기전까지 유저 프로세스는 작업을 중단한 채 대기해야 한다. I/O 작업이 CPU 자원을 거의 쓰지 않기 때문에 Blocking 방법은 CPU 자원 낭비가 심하다.
  - 애플리케이션 관점에서 보면 아무런 동작도 안하는 것처럼 보이지만 실제로는 커널에서 I/O 작업을 수행하느라 block되어 있는 것이다. 바로 이 부분이 blocking I/O의 문제점이며 개선 포인트다
  - 이러한 blocking 방식의 비효율성을 극복하고자 만들어 진 것이 non-blocking 방식이다.
3. 작업 완료 후 데이터를 반환하게 되면 그 때가 되어서야 Application단의 스레드에 걸렸던 block이 풀린다.


## Non-Blocking IO

<img width="600px" src="./img/non_blocking.png">

1. 유저 프로세스가 I/O를 처리하기 위해 커널에 함수를 호출하면(→ system call )
2. 커널에서 함수의 진행 상황과 상관없이 바로 결과를 반환한다.
   - 이 때 반환되는 결과는 반환하는 순간에 가져올 수 있는 데이터에 해당한다. 처음에는 가져올 수 있는 데이터가 없겠지만 시간이 지나면서 가져올 수 있는 데이터가 생겨날 것이다. 서버는 클라이언트가 요청한 사이즈에 맞는 데이터를 반환하기 위해 데이터를 축적한다.
3. 클라이언트가 따로 반환되는 값이 원하는 사이즈가 되었는 지 계속해서 확인해준다. (→ polling )
   - 반환되는 데이터가 준비가 되었는지(원하는 값이 되었는지) 확인하는 과정에서 수많은 클라이언트의 요청이 동시 다발적으로 일어날 경우, CPU에게 적지 않은 부담이 될 수 있는 I/O Model 이다.
4. 데이터의 축적이 끝났을 때, 반환되어 클라이언트에서 요청한 사이즈의 데이터를 받아올 수 있게 된다.

Blocking 과 Non-Blocking 의 차이는 호출되는 IO함수가 바로 리턴하여 제어권을 어플리케이션에 넘기느냐 아니면 바로 리턴하지 않아 커널 쪽에서 제어권을 가지고 있느냐의 차이이다.
ㄴ Blocking 은 바로 리턴하지 않고 IO 작업이 완료될때까지 제어권을 커널 쪽에 넘긴 상태이다. (제어권을 넘겨서 block)
ㄴ Non-Blocking 은 바로 리턴하여 어플리케이션한테 다시 제어권이 돌아온 상태이다. (제어권을 돌려받아서 non block)

이 Blocking IO 와 Non-Blocking IO 가 동기(synchronous) , 비동기(asynchronous) 와 비슷하다고 하여 똑같다고 생각할 수 있으나 엄연히 다른 개념이다.

## 동기 (synchronous) VS 비동기 (asynchronous)

### 동기 (synchronous)

프로세스의 작업을 직렬화하여 수행하는 방식이다.
즉, 한 작업이 완전히 수행될 때까지 다른 작업들은 대기 상태이고, 완전히 완료가 되었다면 이어서 다른 작업이 수행되는 형식이다.
요청을 보내고 반드시 응답을 받아야 다음 작업을 수행하는 형식

### 비동기 (asynchronous)

프로세스의 작업을 병렬화하여 수행하는 방식이다.
즉, 여러 작업을 동시에 수행하는 상태이다.
요청을 보내고 반드시 응답을 받을 필요없이 다음 작업을 동시에 수행하는 형식

<img src="./img/sync.png">

## Synchronous blocking I/O

- Blocking ⇒ 제어권이 없으므로, I/O 작업이 끝날 때까지 스레드에서 다른 작업을 하지 못합니다.
- Synchronous ⇒ 다른 작업으로부터 결과 반환 시 곧바로 처리합니다.

<img src="./img/SynchronousBlocking.png">

## Synchronous non-Blocking I/O

- Non-Blocking ⇒ 제어권은 잃지 않기 때문에 I/O 작업이 끝나기를 기다리지 않고, 스레드에서 다른 작업을 대기 없이 계속 할 수 있습니다.
- Synchronous ⇒ 결과를 처리하기 위해 관심을 갖기 때문에, 결과가 나왔는지 일정한 시간 간격으로 물어보고, 결과를 받으면 곧바로 업무를 처리합니다.

<img src="./img/SynchronousNon-Blocking.png">

### 구현 방식: polling, interrupt

Non-Blocking I/O이기 때문에 I/O 결과가 곧바로 반환되지는 않습니다. 보통 이렇게 요청한 쪽에서 주기적으로 I/O 작업이 완료되었는지 계속 확인해야 하는데, 이런 방식을 polling이라고 합니다. polling은 주기적으로 특정 상태나 이벤트를 확인하는 방법을 나타내며, 이를 통해 I/O 작업의 완료 여부를 주기적으로 체크합니다. 또는, I/O가 완료된 경우 상태가 변경되었을 때 interrupt를 사용하여 알림을 받는 방식으로도 구현할 수 있습니다.

### 단점

또 위 방법에서 보시면, 커널이 I/O 작업으로부터 Read response를 받는 시점과 애플리케이션이 data movement를 받는 시점 사이에 time gap이 있는 것을 확인할 수 있습니다. 이 time gap을 통해 해당 방식이 I/O의 지연을 유발한다는 것을 확인할 수 있습니다. ⇒ 비효율적입니다!

## read/write O_NONBLOCK

일반적으로 Non-blocking과 Synchronous은 서로 상충되는 특성을 가지고 있어 함께 사용되기 어렵지만, 상황에 따라 의미가 조금 달라질 수 있습니다.

참고로, 위 표에 적힌 read/write O_NONBLOCK 에 대해 조금만 설명을 하자면, 이 옵션은 파일 디스크립터를 Non-blocking 모드로 설정하는 옵션입니다. 예를 들어, C 언어의 "open" 함수를 사용하여 파일을 열 때 "O_NONBLOCK" 플래그를 사용할 수 있습니다.

``` java
int fd = open("example.txt", O_RDONLY | O_NONBLOCK);
```

이렇게 하면 파일 디스크립터 fd가 Non-blocking 모드로 열리게 됩니다. 따라서 이 파일 디스크립터를 사용하여 read나 write 작업을 수행할 때, I/O 작업이 완료되지 않았더라도 대기하지 않고 즉시 반환됩니다. 이는 Non-blocking 특성을 갖는 것으로 볼 수 있습니다.

## Asynchronous blocking I/O

- Blocking ⇒ 제어권은 없으므로, I/O가 끝나기만을 기다리면서 다른 작업을 할 수 없습니다.
- Asynchronous ⇒ 결과에 대한 처리는 할 수도 안할 수도 있습니다.

I/O 작업 결과에 대한 처리를 곧바로 할 것도 아닌데 뭐하러 계속 I/O 작업이 끝나기를 기다릴까요? 굉장히 궁합이 안맞고 비효율적인 것 같은데요!

실제로도 해당 방법은 자주 사용하지는 않고, 프로그래머의 실수로 많이 일어난다고 합니다.

<img src="./img/AsynchronousBlocking.png">

### I/O multiplexing

- 하나의 스레드가 여러 개의 입출력 소켓을 감시하면서, 어떤 소켓에서 I/O 작업이 완료되었는지 감시하는 기술
  - 다중 클라이언트와 효율적으로 상호작용할 수 있도록 도와줍니다.
  - 여러 소켓이 동시에 이벤트가 발생할 때까지 블로킹되지 않고 대기
- 여러 소켓에 대한 I/O 이벤트를 단일 스레드에서 처리할 수 있습니다.
- select 또는 poll과 같은 함수를 사용하여 여러 소켓에서 I/O 이벤트를 감시하고, 이벤트가 발생한 소켓들에 대해서만 작업을 수행합니다.
- 다수의 클라이언트 연결을 단일 스레드로 효과적으로 다룰 수 있어서, 많은 서버 프로그램에서 사용되는 기술

## Asynchronous non-Blocking I/O (AIO)

- Non-Blocking ⇒ I/O 작업이 일하는 동안 스레드는 기다리지 않고 다른 작업을 합니다.
- Asynchronous ⇒ 결과에 대한 처리는 할 수도 안할 수도 있습니다.

이 방식에서 일반적으로 I/O 작업 요청은 즉시 반환되어 I/O 작업이 성공적으로 시작되었음을 알려주고, 프로그램은 I/O 작업이 완료될 동안 다른 처리를 수행할 수 있습니다. I/O 결과가 도착하면 시그널이나 스레드 기반의 콜백을 생성하여 I/O 결과를 처리할 수 있습니다.

<img src="./img/AsynchronousNon-Blocking.png">
