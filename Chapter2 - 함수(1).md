#Chapter2 - 함수(1)
## 들어가면서
* 어떤 프로그램이든 가장 기본적인 단위가 함수다. 이 장에서는 함수를 잘 만드는 법(깔끔하고, 명확하게(?))에 대해서 공부해보자.
---

* 우선 아래 예제부터 살펴보자. 길이가 길 뿐만 아니라 중복된 코드에, 괴상한 문자열에, 낯설고 모호한 자료 유형과 API가 많다.
```java
public static String testableHtml(pageData pageData, boolean includeSuiteSetup) throws Exception{
  WikiPage wikiPage = pageData.getWikiPage();
  StringBuffer buffer = new StringBuffer();
  if (pageData.hasAttribute("Test")) {
    if (includeSuiteSetup){
      WikiPage suiteSetup = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME, wikiPage);
      if (suiteSetup != null) {
        WikiPagePath pagePath = suiteSetup.getPageCrawler().getFullPath(suiteSetup);
        String pagePathName = PathParser.render(pagePath);
        buffer.append("!include -setup .")
              .append(pagePathName) 
              .append("\n");    
      }
    }
    WikiPage setup = PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
    if (setup != null){
      WikiPagePath setupPath = wikiPage.getPageCrawler().getFullPath(setup);
      String setupPathName = PathParser.render(setupPath);
      buffer.append("!include -setup .")
            .append(setupPathName);
            .append("\n");
    }
  }
  buffer.append(pageData.getContent());
  if (pageData.hasAttribute("Test")){
    WikiPage teardown = PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
    if (teardown != null){
      WikiPagePath tearDownPath = wikiPage.getPageCrawler().getFullPath(teardown);
      String tearDownPathName = PathParser.render(tearDownPath);
      buffer.append("\n"
            .append("!incldue -teardown .")
            .append(tearDownPathName)
            .append("\n");
    }
    if (includeSuiteSetup){
      WikiPage suiteTeardown = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage);
      if (suiteTeardown != null){
        WikiPagePath pagePath = suiteTeardown.getPageCrawler().getFullPath(suiteTeardown);
        buffer.append("!include -teardown .")
              .append(pagePathName)
              .append("\n");
      }
    }
  }
  pageData.setContent(buffer.toString());
  return pageData.getHtml();
}
```

* 위 코드는 추상화 수준도 너무 다양하고, 코드도 너무 길다. 그리고 두 겹으로 중첩된 if문은 이상한 플래그를 확인하고, 이상한 문자열과 함수를 호출한다.
* 아마 FitNesse에 익숙하지 않은 이상 코드를 100% 이해하기는 어렵지만, 그래도 이 함수가 설정(setup) 페이지와 해제(teardown)페이지를 테스트 페이지에 넣은 후 해당 테스트 페이지를 HTML로 렌더링한다는 사실을 대충 짐작 정도는 해볼 수 있다. 그렇다면 위와 같이 장황하고 이해가 잘 되지 않는 코드보다 아래 코드로 써 보는 것은 어떨까? 아래 코드가 훨씬 읽기 쉽고 이해하기 편할 것이다.

```java
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception{
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage){
        WikiPage testPage = pageData.getWikiPage();
        StringBuffer newPageContent = new StringBuffer();
        includeSetupPages(testPage,  newPageContent, isSuite);
        newPageContent.append(pageData.getContent());
        includeTeardownPages(testPage, newPageContent, isSuite);
        pageData.setContent(newPageContent.toString());

    }
    return pageData.getHtml();
}
```

### 작게 만들어라!
* 함수를 만드는 첫째 규칙은 '작게' 만드는 것이다. 그리고 함수를 만드는 두 번째 규칙은 '더 작게' 만드는 것이다. 그만큼 함수의 길이는 짧고 명료할수록 좋다.
* 사실 위의 함수도 더 작게 만드는 것이 좋다.
```java
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception{
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage(pageData)){
        includeSetupAndTeardownPages(pageData, isSuite);
    }
    return pageData.getHtml();
}
```
* _다시 말해 if문 / else 문 / while 문 등에 들어가는 블록은 한 줄이어야 한다는 의미다. 대개 거기서 함수를 호출한다. 그러면 바깥을 감싸는 함수가 작아질 뿐만 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다._
---

### 한 가지만 해라!
> 함수는 한 가지를 해야 한다. 그 한 가지를 잘해야 한다. 그 한가지만을 해야 한다. 
* 책에서는 추상화 수준을 가지고 함수가 한 가지 일을 하는지에 대해 판단하는 기준이 있다. 그러나, 나는 이 부분에 대해서는 완벽히 이해가 되지는 않아서 그 다음에 나오는 내용으로 함수가 한 가지 일을 하는지 신경쓰려고 한다.
    * _단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 호출할 수 있는가?_ 
