---
layout: post
title: "효과적인 이름짓기"
date: 2014-03-01 18:57:13 +0900
comments: true
categories: [coding standard, naming convention]
author: 장재휴
---

지금 아내의 뱃속에 15주 된 아기가 자라고 있다. 태명은 "행복"이다.  
아내와 난 뱃속의 아기에게 "우리 복이~~" 이렇게 부르고 있다. 이름처럼 참 행복하다. ^^  
요즘은 우리 아기가 평생동안 불리게 될 이름을 생각해 보고 있다.  
부르기 쉬우면서도 흔하지 않은, 그러면서도 뭔가 자신만의 느낌(?)을 담고 있는 그러한 이름. 참 쉽지가 않다.  
이름에 따라서 그 사람을 보는 시각이 달라지기 때문에 더욱 고민하게 된다.  

프로그래밍을 할 때도 다르지 않다.

<!-- more -->

아래 글은 꽤 오래전 페이스북에 짤막하게 남긴 글이다.  

> 난 개발하면서 이름 지을 때 가장 많은 고민을 한다.   
> 변수 이름, 메쏘드 이름, 클래스 이름, 모듈 이름….  
> 이름을 잘 지어야 그것의 역할이 명확해지고 정체성도 분명해진다.  
> 정체성이 분명한 객체들끼리 모여서 자기 역할에 집중하며 서로 상호작용할때 제대로 된 OOP가 구현된다.  
> 이름을 제대로 지어놓지 않으면 처음 생각과는 다르게 덕지덕지 무언가가 붙게되고, 결국 형체도 없는 괴물이 되어간다.  
> 난 내 이름(정체성)에 맞게 살아가고 있나? 

개발자의 길을 걸어온지 이제 만 8년이 다 되어가는데, 이 고민은 아마 코딩을 손에서 놓을때까지 계속되지 않을까?

그런 의미에서 오늘은 "효과적인 이름짓기"라는 제목으로 포스팅을 해 보려고 한다. 오랫동안 책장에 꽂혀있던 [Code Complete 2](http://www.yes24.com/24/goods/1480040?scode=032&OzSrank=1) 책을 꺼내 "네이밍" 관련 부분에 대해 다시 찾아보았고 그 내용을 정리해보았다. "네이밍"에 관련돼 꽤 많은 내용들이 있었지만, 그 중에서도 꼭 기억했으면 하는 부분들에 대해서만 이야기를 해 보겠다.  
  
---

# 좋은 이름에 대한 고려사항

## 네이밍시 가장 중요한 고려사항

먼저 아래 두 코드를 비교해 보자.

```
// 나쁜 변수 명을 사용한 예제
x = x - xx;
xxx = fido + SalesTax( fido );
x = x + LateFee( x1, x ) + xxx;
x = x + Interest( x1, x );
```

```
// 좋은 변수 명을 사용한 예제
balace = balace - lastPayment;
monthlyTotal = newPurchases + SalesTax( newPurchases );
balace = balace + LateFee( customerID, balace );
balace = balace + Interest( customerID, balace );
```

회사에서 일을 하다보면 종종 다른 사람들이 작성한 코드를 보게 되는데, 놀랍게도 첫번째 예제와 같은 코드를 자주 만나게 된다. 이러한 코드를 이해하려면 앞뒤전후의 코드들을 샅샅히 살펴봐야 하고, 실제 동작하는 것을 디버깅까지 해봐야 이 코드가 무엇을 의도하는지 알 수 있다. 이 얼마나 시간 낭비, 에너지 낭비인가? 오늘도 수많은 개발자들이 x1, xx, xxx가 도대체 무엇인지 알아내기 위해, 모든 에너지와 시간을 쏟고나서 힘없이 퇴근하는 하루하루를 보내고 있을 것이다.  

변수의 이름을 짓는데 있어서 가장 중요한 고려사항은,  
`변수 이름이 변수가 표현하고 있는 것을 완벽하고 정확하게 설명해야 한다`는 것이다.  
현재의 이자율을 표현하고 싶으면 r이나 x보다 rate나 interestRate가 더 좋은 이름이다.  
`이름은 가능한 구체적이어야 한다. 모호하거나 하나 이상의 목적으로 사용될 수 있는 일반적인 이름은 보통 나쁜 이름이다.`  
(x, temp, i와 같은 이름은 적절한 정보를 제공해 주지 않는다.)

## 최적의 이름 길이

다음의 예를 보자.  

- numberOfPeopleOnTheUsOlympicTeam
- numberOfSeatsInTheStadium
- maximunNumberOfPointsInMordernOlympics

당연히 첫번째 변수의 의미는 '미국 올림픽 대표팀에 있는 선수의 수'이고, 두번째 변수의 의미는 '운동장의 좌석 수'이다. 세번째는 '올림픽에서 각 나라의 대표팀에서 획득한 최고 점수'를 나타내고 있다. 이러한 이름들은 판독하기가 쉽다. 실제로 이 변수 이름은 쉽게 읽을 수 있기 때문에 판독할 필요가 전혀 없다. 하지만 너무 길어서 실용적이지 않다.

일반적으로 `변수 이름의 길이가 평균적으로 10~16일 때 프로그램을 디버깅하기 위해서 들이는 노력을 최소화 할 수 있고, 변수의 평균 길이가 8~20인 프로그램은 디버깅하기가 쉽다`. 모든 변수의 이름을 10~16의 길이로 작성하기 위해 애쓸 필요는 없겠지만, 코드에 짧은 이름의 변수, 또는 굉장히 긴 이름의 변수를 보게 된다면, 그 이름이 적절한지 확인해 보아야 한다.

- 너무 긴 이름  
numberOfPeopleOnTheUsOlympicTeam  
numberOfSeatsInTheStadium  
maximunNumberOfPointsInMordernOlympics 
- 너무 짧은 이름  
n, np, ntm  
n, ns, nsisd  
m, mp, max, points    
- 적당한 이름  
numTeamMembers, teamMemberCount  
numSeatsInStadium, seatCount  
teamPointsMax, pointsRecord 

그렇다면, 짧은 변수 이름이 항상 나쁜가? 항상 그렇지는 않다. i와 같이 짧은 이름의 변수가 있다면, 이 변수는 제한된 범위를 갖는 연산에서 사용되는 임시 값일 것이다. i라는 이름의 변수가 등장한다면 이 코드는 이렇게 얘기하고 있다. *"이 변수는 일반적인 루프 카운터이거나 배열 인덱스이며, 몇 줄의 코드 외부에서는 전혀 중요하지 않다."*  
하지만 짧은 이름은 문제를 야기할 수 있기 때문에, 주의 깊은 프로그래머들은 방어적인 프로그래밍 정책으로 그것들을 피한다.  

## 변수 이름에서의 계산값 한정자
 
많은 프로그램들은 계산된 값(총계, 평균, 최대값 등)을 보관하는 변수들을 갖는다. `만약 변수의 이름에 Total, Sub, Average, Max, Min, Record, String, Pointer 등의 한정자를 사용해야 한다면, 이름의 끝이 이런 수정자를 입력하는 것이 좋다.`  

다음의 예를 보자

- 좋은 예  
revenueTotal  
expenseTotal  
revenueAverage  
expenseAverage  

- 나쁜 예  
totalRevenue  
expenseTotal  
revenueAverage  
averageExpense  

단 예외적인 경우가 있는데, Num 한정자의 관습적인 위치이다.  
변수의 이름 앞에 있는 Num은 총계를 가르킨다(numCustomer: 전체 고객의 수).  
변수의 이름 끝에 있는 Num은 인덱스를 가르킨다(customerNum: 특정 고객의 번호).  
이런 혼란을 피하기 위해 Num이라는 단어를 피하고 customerCount(전체 고객의 수), customerIndex(특정 고객의 번호)와 같은 이름을 쓰는 것이 좋다.

## 좋은 루틴의 이름

다음은 효과적인 루틴 이름을 만들기 위한 지침이다.

`1. 루틴이 하는 모든 것을 표현하라`  
ComputeReportTotals()는 무엇을 하는지 모호하기 때문에 적절한 이름이 아니다. ComputeReportTotalAndOpenOutputFile()은 적절한 이름이겠지만, 너무 길고 우스꽝스럽다. 이에 대한 해결책은 부수적인 효과를 갖기보다는 직접적인 효과를 유발시키도록 프로그램을 작성하고 그에 맞는 새로운 이름을 짓는 것이다.  

`2. 의미가 없거나 모호하거나 뚜렷한 특징이 없는 동사들을 피하라.`  
HandleCalculation(), PerformServices(), OutputUser(), ProcessInput(), DealWithOutput()과 같은 이름들은 그 루틴이 무엇을 하는지 말해 주지 않는다. 실제로 루틴은 잘 설계되었지만, 루틴의 이름에 뚜렷한 특징이 없기 때문에 문제가 되기도 한다. 만약 HandleOutput()이 FormatAndPrintOutput()으로 대체된다면, 루틴이 무엇을 하는지 상당히 정확하게 이해할 수 있다.  
때론 루틴이 처리하는 연산 자체가 모호하기 때문에 이름이 모호해지는 경우도 있다. 루틴의 목적이 취약하다는 것이 문제이고, 서투른 이름은 그로인한 증상이다. 이런 경우 해당 루틴을 적절하게 리팩토링하여 명확한 처리를 하도록 해야 한다.(리팩토링에 대해서는 다음에 포스팅을 하도록 하겠다.)

`3. 루틴 이름을 숫자만으로 구분하지 말라.`  
Part1(), Part2() 또는 OutputUser1(), OutputUser2()와 같은 이름은 좋은 이름은 잘못된 이름이다. 루틴의 이름 끝에 있는 숫자는 루틴이 표현하는 서로 다른 추상화에 대해서 아무런 정보도 제공하지 않는다.

`4. 함수의 이름을 지을 때, 리턴 값에 대한 설명을 사용하라.`  
리턴 값을 따서 이름을 작성하는 것은 좋은 방법이다. cos(), customerId.Next(), print.IsReady(), pen.CurrentColor()는 함수가 리턴하는 것을 정확하게 보여주고 있기 때문에 모두 좋은 이름이다.  

`5. 프로시저의 이름을 지을 때, 확실한 의미를 갖는 동사 다음에 객체를 사용하라.`  
프로시저의 이름은 프로시저가 무엇을 하는지를 반영해야 하기 때문에, 객체에 대한 연산은 동사+객체 형태의 이름을 갖는다. PrintDocument(), CalcMonthlyRevenues(), CheckOrderInfo(), RepaginateDocument()는 모두 좋은 프로시저 이름이다.

`6. 공통적인 연산을 위한 규약을 만들어라.`  
```
employee.id.Get()
dependent.GetId()
supervisor()
candidate.id()
```
위 코드는 모두 특정 객체의 식별자를 얻기 위한 코드이다. Employee 클래스는 자신의 id 객체를 노출하고, id 객체는 Get() 루틴을 노출했으며, Dependent 클래스는 GetId() 루틴을 노출했다. Supervisor 클래스는 id를 기본 리턴캆으로 만들었고, Candidate 클래스는 id 객체의 기본 리턴 값이 id라는 사실을 이용하여 id 객체를 노출시켰다.  
id를 가져오는 이름 규칙이 있었다면 이러한 난잡한 코드가 생성되는 현상은 막을 수 있을 것이다.

# 데이터의 특정 타입에 대한 네이밍

## 루프 인덱스
루프에 있는 변수의 이름을 지을 때, i,j,k와 같은 이름들은 관습적으로 사용된다.
```
// 간단한 루프 변수 이름에 대한 예제
for ( i = firstItem; i < lastItem; i++ ) {
	data[i] = 0;
}
```

만약 변수가 루프 외부에서 사용되어야 한다면, 반드시 i,j,k 보다는 좀 더 의미 있는 이름을 제공해야 한다.
```
// 설명적인 루프 변수 이름에 대한 예제
recordCount = 0;
while ( moreScores() ) {
	score[ recordCount ] = GetNextScore();
	recordCount++;
}

// recordCount를 사용하는 코드
...
```

만약 루프가 길어진다면, i가 무엇을 나타내는지 쉽게 잊게 되기 때문에, 루프의 인덱스에 좀 더 의미 있는 이름을 제공하는 것이 좋을 것이다. 코드는 자주 변경되고 확장되고 다른 프로그램에 복사되기 때문에, 많은 숙련된 프로그래머들은 i와 같은 이름들은 피한다. 루프가 길어지는 한 가지 이유는 루프가 중첩되기 때문이다. 만약 여러 개의 중첩된 루프가 있다면, 가독성을 향상시키기 위해서 좀 더 긴 이름으로 루프 변수들을 작성한다.
```
// 중첩된 루프에서 좋은 루프 이름을 갖는 예제
for ( teamIndex = 0; teamIndex < teamCount; teamIndex++ ) {
	for ( eventIndex = 0; eventIndex < eventCount[ teamIndex ]; eventIndex++ ) {
		score[ teamIndex ][ eventIndex ] = 0;
	}
}
```
score[ teamIndex ][ eventIndex ]는 scores[ i ][ j ] 보다 많은 정보를 제공한다.  

## 상태 변수

`상태 변수에 대해서 flag보다 더 나은 이름을 생각한다.`  
'flag'는 변수 이름에 사용되지 않아야 한다. 왜나하면 'flag'라는 이름은 플래그가 무엇을 하는지 아무런 단서도 제공하지 않기 때문이다.  
다음의 두 코드를 비교해보자.
```
// 상태 변수를 잘 못 사용한 예제
if ( flag ) ...
if ( statusFlag & 0x0F ) ...
if ( printFlag == 16 ) ...
if ( computeFlag == 0 ) ...

flag = 0x1;
statusFlag = 0x80;
printFlag = 16;
computeFlag = 0;
```
```
// 상태 변수를 잘 사용한 예
if ( dataReady ) ...
if ( characterType & PRINTABLE_CHAR ) ...
if ( reportType == ReportType_Annual ) ...
if ( recalcNeeded == True ) ...

dataReady = true;
characterType = PRINTABLE_CHAR;
reportType = ReportType_Annual;
recalcNeeded = false;
```

## 임시 변수

임시 변수는 계산의 중간 결과를 보관하기 위한 임시 저장소로 사용되고 보조 수단으로 사용되는 값을 보관하기 위해 사용된다. 일반적으로 temp, x 또는 그 밖의 모호하고 설명적이지 않은 이름으로 만들어진다. 일반적으로, 임시 변수는 프로그래머가 프로그램을 완벽하게 이해하지 못하고 있다는 신호이다. 게다가 변수가 공식적으로 '임시'상태이기 때문에, 프로그래머는 임시 변수를 다른 변수들보다 별 생각 없이 다루게 되어 오류가 발생할 가능성이 높아진다.
```
// 2차 방정식의 근을 구한다.
// 이 코드는 (b^2-4*a*c)가 양수라고 가정하고 있다.
temp = sqrt(b^2 - 4*a*c);
root[0] = ( -b + temp ) / ( 2 * a );
root[1] = ( -b - temp ) / ( 2 * a );
```
temp라는 이름은 변수가 무엇을 하는지에 대해서 아무런 정보도 제공하지 않는다.
보다 나은 접근 방법은 다음 예제와 같다.
```
// 2차 방정식의 근을 구한다.
// 이 코드는 (b^2-4*a*c)가 양수라고 가정하고 있다.
discriminant = sqrt(b^2 - 4*a*c);
root[0] = ( -b + discriminant ) / ( 2 * a );
root[1] = ( -b - discriminant ) / ( 2 * a );
```

## 불린 변수

`1. 전형적인 불린 변수의 이름을 기억한다.`  

  - done
  - error
  - found
  - success나 ok  
단, 성공했다는 것을 정확하게 설명하는 구체적인 이름이 있다면 다른 이름으로 대체하는 것이 좋다.  
(예: processingComplete, found, 등)

`2. 참이나 거짓의 의미를 함축하는 불린 변수의 이름을 사용한다.`  
status, sourceFile 같은 변수들은 참이나 거짓이 명백하지 않기 때문에 좋지 못한 불린 이름이다.  
statusOK, sourceFileAvailable 또는 sourceFileFound와 같은 이름으로 대체해야 한다.

`3. 긍정적인 불린 변수 이름을 사용한다`  
notFound, nonDone, notSuccessful과 같은 부정적인 이름은 이 변수의 값이 부정이 되었을 때 읽기가 어려워진다.
```
if(notFound ==  false) ...
```
위와 같은 코드를 읽을 때는 한번 더 생각을 해야 한다. if(found == true) 가 훨씬 더 자연스럽다.  
이런 이름은 반드시 found, done, success로 대체되어야 한다.

# 체크리스트
마지막으로 위 내용을 점검 할 수 있는 체크리스트를 적어보았다.

## 네이밍에 대한 일반적인 고려사항
- 변수가 표현하고자 하는 것을 이름이 완벽하고 정확하게 설명하는가?
- 변수가 프로그래밍 언어의 해결책보다는 실세계의 문제를 참조하고 있는가?
- 고민할 필요가 없을 만큼 긴가?
- 계산 값 한정자가 이름의 마지막에 있는가?
- Num 대신 Count나 Index를 사용하는가?

## 특정 종류의 데이터에 대한 네이밍
- 루프 인덱스의 이름이 의미가 있는가(루프가 한 줄 이상이거나 중첩되어 있다면 i,j,k가 아닌 다른 것)?
- 모든 '임시' 변수가 보다 의미 있는 이름으로 다시 명명되었는가?
- 불린 변수가 참일 때 그 의미가 분명하도록 명명되었는가?

---

글이 꽤 길어졌다. 네이밍에 관련된 많은 지침들이 있지만, 내용이 너무 많으면 까먹을 것 같아서 반드시 지켜야 할 것이라고 생각되는 것들에 대해서만 이야기 해 보았다. 마지막으로 중요한 기억해야 할 사실은, `코드는 작성되는 것보다 훨씬 많이 읽혀진다. 코드 작성의 편의성 보다는 코드의 가독성에 좀 더 중점을 두는 습관을 들이도록 하자.`