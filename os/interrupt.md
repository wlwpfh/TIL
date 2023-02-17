## * Interrupt
: hardware나 software에서 문제나 예외사항이 생겼을 때 interrupt라는 것을 발생시켜 알린다.

### * interrupt handling
: OS는 register, PC를 저장하므로 CPU의 상태를 저장한다.

### * interrupt가 발생하였을 때의 방법
1. polling: 하나씩 던져 그에 맞는 것을 수행한다.
2. vectored interrupt system: interrupt번호로 와서 해당 interrupt service routine을 실행한다.

해당 처리가 완료되면 다시 원래의 프로그램을 되돌려 정상적으로 수행하게 된다.