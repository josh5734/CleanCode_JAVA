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
---

### 서술적인 이름을 사용하라!
> 코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드다.
* 이름은 길어도 괜찮다. 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
* 서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.
---

### 함수 인수
* 함수에서 이상적인 인수 개수는 0개이다. 3개는 가능한 피하는 편이 좋고, 4개 이상은 특별한 이유가 필요하다.
---

### 명령과 조회를 분리하라.
* 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다. 둘 다 하면 안 된다. 예를 들어 다음 함수를 살펴보자.
```java
public boolean set(String attribute, String value);
```
* 이 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true를 반환하고 실패하면 false를 반환한다. 그래서 다음과 같이 괴상한 코드를 유발할 수 있다.
```java
if set("username", "unclebob")) ...
```
* 독자 입장에서 코드를 읽어보자. 무슨 뜻일까?
*   - "username"이 "unclebob"으로 설정되어 있는지 확인하는 코드인가?
*   -  "username"을 "unclebob"으로 설정하는 코드인가? 
* 함수를 호출하는 코드만 봐서는 의미가 모호하다. 함수를 구현한 개발자는 "set"을 동사로 의도했지만, if문에 넣고 보면 형용사로 느껴진다. set이라는 함수 이름을 setAndCheckIfExists라고 바꾸는 방법도 있지만  if문에 넣고 보면 여전히 어색하다. 진짜 해결책은 명령과 조회를 분리해 혼란을 애초에 뿌리뽑는 것이다.
```java
if(attributeExists("username")){
  setAttribute("username", "unclebob");
  ...
}
```
---
### 오류 코드보다 예외를 사용해라!
* 명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다. 자칫하면 if문에서 명령을 표현식으로 사용하기 쉬운 탓이다.
```java
if (deletePage(page) == E_OK)
```
* 위 코드는 동사/형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다. 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힌다.
```java
if (deletePage(page) == E_OK){
    if(registry.deleteReference(page.name) == E_OK){
        if(configKeys.deleteKey(page.name.makekey()) == E_OK){
            logger.log("page deleted!");
        }else{
            logger.log("configKey not deleted");
        }
    }else{
        logger.log("deleteReference from registry failed");
    }
}else{
    logger.log("delete failed");
    return E_ERROR;
}
```
* **반면 오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.**
```java
try{
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e){
  logger.log(e.getMessage());
}
```

#### Try/Catch 블록 뽑아내기
* try/catch 블록은 추하다. 코드 구조에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤섞는다. 그러므로 try/catch 블록을 별도 함수로 뽑아내는 편이 좋다.
```java
public void delete(Page page){
  try{
    deletePageAndAllReferences(page);
  }
  catch(Exception e){
    logError(e);
  }
}

private void deletePageAndAllReferences(Page page) throws Exception{
  deletePage(page);
  registry.deleteReference(page.name);
  ConfigKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e){
  logger.log(e.getMessage());
}
```
* 위에서 delete 함수는 모든 오류를 처리한다. 그래서 코드를 이해하기 쉽다. 한 번 훑어보고 넘어갈 정도로 충분하다. 실제로 페이지를 제거하는 함수는 deletePageAndAllReferences다. deletePageAndAllReferences 함수는 예외를 처리하지 않는다. 이렇게 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.


## 마치면서
_함수를 짤 때 처음에는 길고  복잡하다. 들여쓰기 단계도 많고 중복된 루프도 많다. 인수 목록도 아주 길다. 이름은 즉흥적이고 코드는 중복된다. 하지만 그 다음에 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거해야 한다. 메서드를 줄이고 순서를 바꾸기도 한다. 때로는 전체 클래스를 쪼개기도 한다._
* 함수를 깨끗하게 만드는 것은 쉽지 않다. 나도 항상 한 가지 일을 하는 함수를 만든다고는 하지만 그 안에서 여러가지 명령과 조회를 하고 있고, 조회를 하고 다른 값을 리턴해버리기도 한다. try/catch보다는 반복되는 조건문을 이용해 상황을 처리하기만 했었는데, 이번 장을 보면서 깨끗한 함수를 만드는 것이 얼마나 중요한지 조금이나마 알게 되었다.
* 책에 있는 내용을 100% 다 정리하지는 않았다. 지금 상황에서 이해하기 어려운 부분이나 적용해보기 어려울 것 같은 부분은 제외했다. 이런 부분은 나중에 기회가 되면 다시 봐야겠다.
