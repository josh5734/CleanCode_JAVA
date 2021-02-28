#Chapter3 - 주석

## 들어가면서
* 프로그래밍 언어 자체가 표현력이 풍부하다면, 사실 상 주석을 사용할 필요가 없다. 하지만 우리가 코드로 의도를 표현하지 못할 때 어쩔 수 없이 주석을 사용한다.
* 그러므로 주석이 필요한 상황에 처하면 곰곰이 생각을 해야 한다. _과연 주석 대신에 코드로 의도를 표현할 방법은 없을까?_


### 코드로 의도를 표현하라!
* 확실히 코드만으로 의도를 설명하기 어려운 경우가 존재한다. 하지만 분명히 코드만으로도 의도를 설명할 수 있는 경우가 많다. 아래 예시를 보면 명확히 그 차이를 알 수 있다.
```java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
----
if (employee.isEligibleForFullBenefits())
```

### 좋은 주석 - 주석이 항상 안 좋은 것이 아니라, 꼭 필요할 때가 있고 쓰는 게 더 좋을 때가 있다.
1. 법적인 주석
```
// CopyRight (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
// GNU General Public License 버전 2 이상을 따르는 조건으로 배포한다.
```
2. 정보를 제공하는 주석
```java
// kk:mm:ss EEE, MMM dd, yyyy형식이다.
Pattern timeMatcher = Patteern.compile("\\d*:\\d*:\\d*\\w*, \\w* \\d*, \\d*");
```
3. 의미를 명료하게 밝히는 주석 - 잘못된 의미를 써 놓으면 망한다.
```java
public void testCompareTo() throws Exception{
    WikiPagePath a = PathParser.parse("PageA");
    WikiPagePath ab = PathParser.parse("PageA.PageB");
    WikiPagePath b = PathParser.parse("PageB");
    WikiPagePath aa = PathParser.parse("PageA.PageA");
    WikiPagePath bb = PathParser.parse("PageB.PageB");
    WikiPagePath ba = PathParser.parse("PageB.PageA");

    assertTrue(a.compareTo(a) == 0);     // a == a
    assertTrue(a.compareTo(b) != 0);     // a != b
    assertTrue(ab.compareTo(ab) == 0);   // ab == ab
    assertTrue(a.compareTo(b) == -1);    // a < b
    assertTrue(aa.compareTo(ab) == -1);  // aa == ab
    assertTrue(ba.compareTo(bb) == -1);  // ba == bb
    assertTrue(b.compareTo(a) == 1);     // b > a
    assertTrue(ab.compareTo(aa) == 1);   // ab > aa
    assertTrue(bb.compareTo(ba) == 1);   // bb > ba
}
```

4. TODO 주석
*  TODO 주석은 프로그래머가 필요하다 여기지만 당장 구현하기 어려운 업무를 기술한다. 더 이상 필요없는 기능을 삭제하라는 알림, 누군가에게 문제를 봐달라는 요청, 더 좋은 이름을 떠올려달라는 부탁 등에 유용하다.

## 예제
* 아래 모듈을 보며 우리는 흔히 '주석을 잘 달았다'고 생각해왔을 것이다. 나 역시도 비슷하게 주석을 작성했고, 친절한 주석이라고 생각했었다.
```java
/**
 * 이 클래스는 사용자가 지정한 최대 값까지 소수를 생성한다. 사용된 알고리즘은 에라토스테네스의 체이다.
 *  <p>
 *  알고리즘은 상당히 단순하다. 2에서 시작하는 정수 배열을 대상으로
 *  2의 배수를 모두 제거한다. 다음으로 남은 정수를 찾아 이 정수의 배수를 모두 지운다.
 *  최대 값의 제곱근이 될 때까지 이를 반복한다.
 *  </p>
 * @author Alphonse
 * @version 13 Feb 2002 atp
*/
import java.util.*;

public class GeneratePrimes{
    /**
     * @param maxValue : 소수를 찾아낼 최대 값
     */
    public static int[] generatePrimes(int maxValue){
        if (maxValue >= 2) // 유일하게 유효한 경우
        {
            // 선언
            int s = maxValue + 1l // 배열크기
            boolean[] f = new boolean[s];
            int i;

            // 배열을 참으로 초기화
            for(i = 0; i < s; i++)
                f[i] = true;
            //소수가 아닌 알려진 숫자를 제거
            f[0] = f[1] = false;

            // 체
            int j;
            for (i = 2; i < Math.sqrt(s) +1 ;i++){
                if(f[i]) // i가 남아 있는 숫자라면 이 숫자의 배수를 구한다.
                {
                    for (j = 2*i; j < s; j+=i)
                        f[j] = false; // 배수는 소수가 아니다.
                }
            }

            // 소수 개수는?
            int count = 0;
            for(i = 0; i < s; i++){
                if(f[i])
                    count++; // 카운트 증가
            }

            int[] primes = new int[count];

            // 소수를 결과 배열로 이동한다.
            for(i = 0, j = 0; i < s; i++){
                if(f[i]) // 소수일 경우에
                    primes[j++] = i;
            }
            return primes; // 소수를 반환한다.
        }
        else // maxValue < 2
            return new int[0];
    }
}
```

* 아래는 위 코드를 리팩토링한 결과이다. 주석 양이 상달히 줄었다.
```java

/**
 * 이 클래스는 사용자가 지정한 최대 값까지 소수를 생성한다. 
 * 알고리즘은 에라토스테네스의 체이다.
 *  2에서 시작하는 정수 배열을 대상으로 작업한다.
 *  처음으로 남아 있는 정수를 찾아 배수를 모두 제거한다.
 *  배열에 더 이상 배수가 없을 때까지 반복한다.
*/
public class PrimeGenerator
{
    private static boolean[] crossedOut;
    private static int[] result;

    public static int[] generatePrimes(int maxValue)
    {
        if (maxValue < 2)
            return new int[0];
        else {
            uncrossIntegersUpTo(maxValue);
            crossOutMultiples();
            putUncrossedIntegersIntoResult();
            return result;
        }
    }

    private static void uncrossIntegersUpTo(int maxValue)
    {
        crossedOut = new boolean[maxValue + 1]
        for(int i = 2; i < crossedOut.length; i++)
            crossedOut[i] = false;            
    }

    private static void crossOutMultiples()
    {
        int limit = determineIterationLimit();
        for (int i = 2; i <=limit; i++)
            if(notCrossed())
                crossOutMultiples();
    }

    private static int determineIterationLimit()
    {
        // 배열에 있는 모든 배수는 배열 크기의 제곱근보다 작은 소수의 인수다.
        // 따라서 이 제곱근보다 더 큰 숫자의 배수는 제거할 필요가 없다.
        double iterationLimit = Math.sqrt(crossedOut.length);
        return (int) iterationLimit;
    }

    private static void crossOutMultiplesOf(int i)
    {
        for(int multiple = 2 * i; multiple < crossedOut.length; multiple += i)
            crossedOut[multiple] = true;
    }

    private static boolean notCrossed(int i)
    {
        return crossedOut[i] == false;
    }

    private static void putUncrossedIntegersIntoResult()
    {
        result = new int[numberOfUncrossedIntegers()];
        for (int j = 0, i = 2; i < crossedOut.length; i++)
            if (notCrossed(i))
                result[j++] = i;
    }
    private static int numberOfUncrossedIntegers() {
        int count = 0;
        for (int i = 2; i < crossedOut.length; i++)
            if (notCrossed(i))
                count++;

        return count;
    }
}
```

### 마치면서
* 이번 글에서는 좋은 주석이란 어떤 주석인지에 대해 살펴보고, 그 예시를 직접 관찰해보았다.
* 좋은 주석을 남기기 위해서는 이전에 공부했던 함수에 좋은 이름을 짓고, 의미있는 이름을 지어야 했다. 그리고 각 기능을 함수 단위로 쪼개는 것이 주석을 대신하는 방법이 된다는 것을 느꼈다.
