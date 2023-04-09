## 중첩 클래스의 접근 제한 
### 1. 바깥 필드에와 메소드에서 사용 제한
  
: 멤버 클래스가 인스턴스 또는 정적으로 선언됨에 따라 바깥 클래스의 필드와 메소드에 사용 제한이 생긴다.
```java
    class A{
        
        // 인스턴스 필드
        B field1=new B(); (O)
        C field2=new C(); (O)

        // 인스턴스 메소드
        void method1(){
            B var=new B(); (O)
            C var2=new C(); (O)
        }

        // 정적 필드 초기화
        static B field3=new B(); (X)
        static C field4=new C(); (O)

        // 정적 메소드
        static void method2(){
            B var1=new B(); (X)
            C var2=new C(); (O)
        }

        // 인스턴스 멤버 클래스
        class B{}

        //정적 멤버 클래스
        static class C{}
    }

```

<br/>

### 2. 멤버 클래스에서 사용 제한   
: 멤버 클래스가 인스턴스 또는 정적으로 선언됨에 따라 멤버 클래스 내부에서 바깥 클래스의 필드와 메소드를 접근할 때에도 제한이 생긴다.  
: B 안에서는 바깥 클래스의 모든 필드와 메소드에 접근할 수 있다.  
: 하지만 C 안에서는 바깥 클래스의 정적 필드와 정적 메소드에만 접근할 수 있으며 인스턴스 필드와 메소드에는 접근할 수 없다.  

```java
    class A{
        int field1;
        void method1(){ }

        static int field2;
        static void method2(){}

        class B{
            void method(){
                field1=10;
                method1();

                field2=10;
                method2();
            }
        }
    }
```
모든 필드와 메소드에 접근할 수 있다.  

<br/>

```java
    class A{
        int field1;
        void method1(){ }

        static int field2;
        static void method2(){}

        static class B{
            void method(){
                field1=10; (X)
                method1(); (X)

                field2=10; (O)
                method2(); (O)
            }
        }
    }
```
예시와 같이 인스턴스 필드와 메소드는 접근할 수 없다.

<br/>

### 3. 로컬 클래스에서 사용 제한
: 내부에서는 바깥 클래스의 필드나 메소드를 제한 없이 사용할 수 있다.  
: 로컬 클래스에서 사용된 매개 변수와 로컬 변수는 모두 `final`의 특징을 갖는다.  
: `final` 키워드가 있다면 로컬 클래스의 메소드 내부에 지역 변수로 복사되지만 `final` 키워드가 없다면 로컬 클래스의 필드로 복사된다.  

```java
    class Outter{
        int localVariable=1;

        class Inner{
            public void method(){
                int result=arg+localVariable;
            }
        }
    }
```

<br/>

### 4. 중첩 클래스에서 바깥 클래스 참조 얻기
: 클래스에서 `this`는 객체 자신의 참조이다.  
: `this`를 사용하면 중첩 클래스의 객체 참조가 된다.  

```java
    class Outter{
        String field="outter-field";

        void method(){
            System.out.println("outter-method");
        }

        class Nested{
            String field="nested-field";

            void method(){
                System.out.println("nested-method");
            }
        }

        void print(){
            System.out.println(this.field);
            this.method(); // 중첩 객체 참조 

            System.out.println(Outter.this.field);
            Outter.this.method(); //바깥 객체 참조 
        }
    }
```