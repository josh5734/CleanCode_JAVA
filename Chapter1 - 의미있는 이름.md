# Chapter1 - 의미 있는 이름

## 들어가면서
* 우리는 변수에도 이름을 붙이고, 함수에도 이름을 붙이고, 인수와 클래스와 패키지에도 이름을 붙인다. 소스 파일에도 이름을 붙이고, 소스 파일이 담긴 디렉토리에도 이름을 붙인다.
이렇듯 우리는 여기저기에서 이름을 사용하고, 따라서 이름을 잘 지으면 여러모로 편하다. 이 장에서는 이름을 잘 짓는 간단한 규칙을 몇 가지 공부해보자.   
   
   
   
   
### 의도를 분명히 밝혀라.
* 의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다. 다음 코드는 무엇을 할까?
```java
public List<int[]> getThem(){  
  List<int[]> list1 = new ArrayList<int[]>();  
   for(int[] x : theList)  
    if(x[0] == 4)  
      list1.add(x);  
  return list1;  
}
```
* 코드가 하는 일을 짐작하기 어렵다. 왜일까? 복잡한 문장이 없음에도 독자는 코드의 '함축성'으로 인해 고통받게 되는 것이다.
  - theList에 무엇이 들어있는가?
  - theList에서 0번째 값이 왜 중요한가?
  - 값 4는 무슨 의미인가?
  - 함수가 반환하는 리스트 list1을 어떻게 사용하는가?
  
* 위 코드 샘플엔 이와 같은 정보가 드러나지 않는다. 하지만 정보 제공은 충분히 가능했었다. 우리가 만약 지뢰찾기 게임을 만든다고 가정해보자. 그러면 theList가 게임판이라는 사실을 안다.
theList를 gameBoard로 바꾸고, 게임판에서 각 칸은 단순 배열로 표현해보자. 배열에서 0번째 값은 칸 상태를 뜻한다. 값 4는 깃발이 꽂힌 상태를 가리킨다. 각 개념에 이름만 붙여도 코드가 상당히 나아진다.
```java
public List<int[]> getFlaggedCells(){
  List<int[]> flaggedCells = new ArrayList<int[]>();
    for(int[] cell : gameBoard){
      if( cell[STATUS_VALUE] == FLAGGED)
        flaggedCells.add(cell);
  return flaggedCells;
}
```

* 위에서 보듯이, 코드의 단순성은 변하지 않았다. 연산자 수와 상수 수는 앞의 예와 독같고, 들여쓰기 단계또 똑같다. 그런데도 코드는 더욱 명확해졌다.
한걸음 더 나아가, int 배열을 사용하는 대신, 칸을 간단한 클래스로 만들어도 좋다. isFlagged라는 좀 더 명시적인 함수를 사용해 FLAGGED라는 상수를 감춰도 좋다!
```java
public List<int[]> getFlaggedCells(){
  List<Cell> flaggedCells = new ArrayList<Cell>();
    for(Cell cell : gameBoard){
      if(cell.isFlagged())
        flaggedCells.add(cell);
  return flaggedCells;
}
```

* 단순히 이름만 고쳐도 함수가 하는 일을 이해하기 훨씬 쉬워진다. 이래서 좋은 이름을 써야 한다.
---

### 그릇된 정보를 피하라.
* 사실 이 부분에 대해서는 우리가 이미 많이 경험적으로 고통을 받은 적이 많을 것이다. 아래와 같은 예를 보면 무슨 말을 하고자 하는지 바로 이해가 될 것이다.
```
int a = 1;
int a = l;

int b = o1;
int b = 01;
```
---

### 의미 있게 구분하라.
* 이름을 의미 있게 구분하라는 말 역시, 위에서 언급했던 것처럼 '의미있는 이름'을 쓰라는 말과 비슷하다. 연속적인 숫자를 덧붙인 이름(a1, a2 ...)은 의도적인 이름과 정반대다.
이런 이름은 그릇된 정보를 제공하는 이름도 아니며, 아무런 정보를 제공하지 못하는 이름일 뿐이다. 저자 의도가 전혀 드러나지 않는다!
```java
public sttic void copyChars(char a1[], char a2[]){
  for(int i = 0 ; i < a1.length; i++){
    a2[i] = a1[i];
  }
}
```

* 함수 인수 이름으로 source와 destination을 사용한다면 코드 읽기가 훨씬 더 쉬워지지 않을까?
또한 아래와 같은 이름짓기도 혼란을 초래할 수 있다.
```
getActiveAccount()
getActiveAccounts()
getActiveAccountInfo()

moneyAccount
money

customerInfo
customer 
...
```
* 그 외에도 변수 이름을 a, b, i, j, k 이런식으로 자신의 기억력을 자랑하듯 짓지 말자.
그리고 되도록 발음하기 편한 단어로 이름을 짓는 것이 좋다는 것도 기억해두자.



