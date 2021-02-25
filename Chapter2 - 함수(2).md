#Chapter2 - 함수(2)

## 지난 내용
* 함수를 작게 만들어라!
* 함수는 한 가지만 해라!
---


### Switch문 
* switch문은 작게 만들기가 어렵다. case 분기가 두 개만 되도 코드가 길어지며, '한 가지' 작업만 하는 switch문도 만들기 어렵다. 본질적으로 switch문은 N가지 일을 처리할 수밖에 없다.
* 따라서 각 switch문을 저차원 클래스에 숨기고, 절대로 반복하지 않는 방법은 있다. by *다형성(polymorphism)*
* 아래는 직원 유형에 따라 다른 값을 계산해 반환하는 함수이다. 일단 이 함수의 문제점은 길고, 새 직원을 추가하면 더 길어지게 된다. 그리고 한 가지 작업만 수행하지도 않는다. 하지만 가장 심각한 문제는 위 함수와 구조가 동일한 함수가 무한정 존재한다는 사실이다.
```java
public Money calculatePay(Employee e) throws InvalidEmployeeType{
  switch(e.type){
    case COMMISSIONED:
      return calculateCommissionedPay(e);
    case HOURLY:
      return calculateHourlyPay(e);
    case COMMISSIONED:
      return calculateSalariedPay(e);
    default:
      throw new InvalidEmployee(e.type);
  }
}
```
* 이 문제를 해결한 코드가 아래 코드이다. 아래 코드는 switch문을 추상 팩토리(Abstract Factory)에 숨긴다. 팩토리는 switch문을 사용해 적절한 Employee 파생 클래스의 인스턴스를 생성한다.
* calculatePay, isPayday, deliverPay 등과 같은 함수는 Employee 인터페이스를 거쳐 호출된다. 그러면 다형성으로 인해 실제 파생 클래스의 함수가 실행된다.
```java
public abstract class Employee{
  public abstract boolean isPayday();
  public abstract Money calculatePay();
  public abstract void deliverPay(Money pay);
}
---
public interface EmployeeFactory{
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
---
public class EmployeeFactoryImpl implements EmployeeFactory{
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType{
    switch (r.type){
      case COMMISSIONED:
        return new CommissionedEmployee(r);
      case HOURLY:
        return new HourlyEmployee(r);
      case SALARIED:
        return new SalariedEmployee(r);
      default:
        return new InvalidEmployeeType(r.type);
    }
  }
}
```

  
