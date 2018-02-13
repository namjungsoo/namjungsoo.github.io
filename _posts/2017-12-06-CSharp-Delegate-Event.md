---
layout: post
title: C# Delegate/Event 정리
---
**Delegate/Event**  
C#은 Java의 interface callback을 쉽게 하기 위한 방법으로 delegate/event를 제공한다. 

**Delegate (위임, 대리자)**  
Delegate는 C의 함수포인터와 같은 방식으로 함수를 저장했다가 파라미터로 전달 또는 호출할수 있는 객체이다.

```cs
using System;
 
namespace Delegate
{
    class MainClass
    {
        public delegate void Message(string msg);
 
        public void Hello(string msg)
        {
            Console.WriteLine("Hello "+msg);   
        }
 
        public void Run()
        {
            Message message = new Message(Hello);
            message("Delegate!");
        }
 
        public static void Main(string[] args)
        {
            MainClass mainClass = new MainClass();
            mainClass.Run();
        }
    }
}
```

**C++의 함수 포인터와의 차이점**  
클래스를 사용하는 C++에는 Pointer to member function이 있는데, 이는 한 클래스의 멤버 함수에 대한 포인터로서 '객체'에 대한 컨텍스트를 가지고 있다는 점에서 C#의 delegate와 비슷하다. 

단, C#의 delegate는 메서드 Prototype이 같다면 어느 클래스의 메서드도 쉽게 할당할 수 있는데 반해, C++의 Pointer to member는 함수 포인터 선언시 특정 클래스를 지정해주기 때문에 한 클래스에 대해서만 사용할 수 있다.  

두번째로 C의 함수 포인터는 하나의 함수 포인터를 갖는데 반해, C# delegate는 하나 이상의 메서드 레퍼런스들을 가질 수 있어서 Multicast가 가능하다.

또한 C의 함수포인터는 Type Safety를 완전히 보장하지 않는 반면, C#의 delegate는 엄격하게 Type Safety를 보장한다. 

**Event**  
특별한 형태의 delegate 즉 C# event를 사용할 수 있다. C# event는 할당연산자(=)를 사용할 수 없으며, 오직 **이벤트 추가(+= 연산자, Subscribe) 혹은 기존 이벤트 삭제 (-= 연산자, Unsubscribe) 만**을 할 수 있다. 또한 delegate 와는 달리 **해당 클래스 외부에서는 직접 호출할 수 없다.** 

```cs
using System;
 
namespace Delegate
{
    class MessageClass
    {
        public delegate void Message(string msg);
        public Message myMessage;
        public event Message myEvent;
 
        public void Hello(string msg)
        {
            Console.WriteLine("Hello " + msg);
        }
 
        public void Run()
        {
            Message message = new Message(Hello);
            message("Delegate!");
        }
 
        public void RunEvent()
        {
            myEvent("RunEvent!");
        }
    }
 
    class MainClass
    {
        public static void Main(string[] args)
        {
            MessageClass mainClass = new MessageClass();
            mainClass.myMessage = mainClass.Hello;
            mainClass.myMessage("External!");
 
            mainClass.myEvent += mainClass.Hello;
 
            // event는 클래스 외부에서 실행할수 없음
            //mainClass.myEvent("Event");
            mainClass.RunEvent();
        }
    }
}
```