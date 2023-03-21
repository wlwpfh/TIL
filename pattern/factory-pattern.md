## * Factory Pattern  
: 객체를 사용하는 코드에서 객체 생성 부분을 떼어내 추상화한 패턴이자 상속 관계에 있는 두 클래스에서 상위 클래스가 중요하다.    
: 뼈대를 결정하고 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정하는 패턴이다.  
<br/>

### * 예제
```java
    abstract class Coffee{
        public abstract int getPrice();

        @Override
        public String toString(){
            return "this coffee is"+ this.getPrice();
        }
    }
```

``` java
    class CoffeeFactory{
        public static Coffee getCoffee(String type, int price){
            if("Latte".equalsIgnoreCase(type))
                return new Latte(price);
            if("Americano".equalsIgnoreCase(type))
                return new Americano(price);
            return new DefaultCoffee();
        }
    }
```

```java
    public DefaultCoffee extends Coffee{
        private int price;

        public DefaultCoffee(){
            this.price=-1;
        }
        

        @Override
        public int getPrice(){
            return this.price;
        }
    }
```

```java
    public Latte extends Coffee{
        private int price;

        public Latte(int price){
            this.price=price;
        }
        

        @Override
        public int getPrice(){
            return this.price;
        }
    }
```

```java
    public Americano extends Coffee{
        private int price;

        public Americano(int price){
            this.price=price;
        }
        

        @Override
        public int getPrice(){
            return this.price;
        }
    }
```

```java
    Coffee latte = CoffeeFactory.getCoffee("Latte", 4000);
    Coffee americano=CoffeeFactory.getCoffee("Americano", 2000);

    System.out.println("latte": + latte);
    System.out.println("americano": + americano); 

    /*
    latte: this coffee is 4000
    americano: this coffee is 2000
    */
```
: `Coffee class`를 `CoffeeFactory`라는 뼈대를 통해 생성하여 `Latte`와 `Americano`에서 구체적인 내용을 갖게 된다. 