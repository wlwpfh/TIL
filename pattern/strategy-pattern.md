## * 전략 패턴
: 객체의 행위를 바꾸고 싶은 경우, 직접 수정하지 않고 전략이라 부르는 `캡슐화한 알고리즘`을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴이다.    
: 상속을 통해 다양한 방식을 구현할 수 있다.  
<br/>

### * 예제

```java
    public interface PaymentStrategy {
        void pay(int amount);
}
```
`PaymentStrategy`를 통해 결제에 대한 뼈대를 생성한다. 

```java
    public class KakaoCardPayment implements PaymentStrategy{
        @Override
        public int pay(int amount) {
            System.out.println(amount+"원 결제 중 - KAKAOCARD");
        }
}
```

```java
    public class LunaCardPayment implements PaymentStrategy{
        @Override
        public int pay(int amount) {
            System.out.println(amount+"원 결제 중 - LUNACARD");
        }
    }
```


```java
    @Getter
    @Setter
    public class Item{
        private String name;
        private int price;

        public Item(String name, int price){
            this.name=name;
            this.price=price;
        }
    }
```
`Item`객체를 통해 상품의 이름과 가격을 설정합니다.  

```java
    public class Cart{
        List<Item> items;

        public Cart(){
            this.items=new ArrayList<Item>();
        }

        public void addItem(Item item){
            items.add(item);
        }

        public int calculatePrice(){
            int totalPrice=0;
            for(Item item:items)
                totalPrice+=item.getPrice();
        }
        return totalPrice;

        public int pay(PaymentStrategy payment){
            int amount=calculatePrice();
            payment.pay(amount);
        }
    }
```

```java
    class Test{
        Cart cart= new Cart();
        Item item1=new Item("아이템1", 1000);
        Item item2=new Item("아이템2", 2000);

        cart.add(item1);
        cart.add(item2);

        cart.pay(new KakaoCardPayment());
        cart.pay(new LunaCardPayment());
        
        /*
        3000원 결제 중 - KAKAOCARD
        3000원 결제 중 - LUNACARD 
        */
    }
```


<br/>

### * passport
: Node.js에서 인증 모듈을 구현할 때 사용되는 `passport`라이브러리가 전략 패턴으로 활용되어 있다. 