# printf 함수에 대하여

printf는 문자열을 출력하는 함수이다. C언어는 문자열을 ""(큰 따옴표)를 사용해서 표현하기 때문에 큰 따옴표로 문자열을 전달하며 printf를 호출하면 
전달된 문자열이 화면상에 출력된다.

# 특수문자를 출력하는 방법
특수문자는 작은 따옴표로 묶어서 독립된 문자상수로 존재 할 수도 있는데, 그 예시는 다음과 같다.
```
char n = '\n';
```
특수문자와 이스케이프 시퀀스(escape sequence)가 필요한 이유를 간단히 알아보자.  
>그는 말했다. "배고프다." 정말이다.

위 문장을 출력하고 싶은데, 이를 문자열 출력을 위해 ""를 앞뒤에 넣는다면
>"그는 말했다. "배고프다" 정말이다."  
위와 같이 넣을 수 있다. 하지만 이러면 오류가 난다.  

큰 따옴표의 시작과 끝을 문자열의 시작과 끝으로 보기때문에
1. "그는 말했다. " <-문자열로 인식된다.
2.  배고프다 <- 에러가 난다.
3. " 정말이다." <-문자열로 인식된다.
이런 식으로 인식하게 된다.

이러한 유형의 문제를 해결하기 위해 특수문자가 존재한다.  
C언어는 특수문자의 정의를 다음과 같이 이야기한다.
**"특수한 문자의 출력을 원하는 경우 \문자를 붙여주기로 하자."**

따라서 위 예시를 수정하면 다음과 같다.
>"그는 말했다. \"배고프다\" 정말이다."  
위는 정상적으로 출력된다.

## 특수문자의 종류

|종류|의미|
|:--|:--|
|\a|경고음|
|\b|백스페이스|
|\f|폼 피드|
|\n|개 행(new line)|
|\r|캐리지 리턴(carriage return)||
|\t|수평 탭|
|\v|수직 탭|
|\\'|작은 따옴표 출력|
|\\"|큰 따옴표 출력|
|\\?|물음표 출력|
| \\\\ |역슬래쉬 출력|  

참고로 \f 와 \v는 모니터 출력이 아닌 프린터 출력을 위해 정의된 특수문자이기 때문에 모니터의 출력과 상관없는 특수문자이다.

## printf 함수의 서식지정

printf 의  f는 formatted(서식이 지정된)이라는 의미이다. 따라서 printf라는 함수의 이름에는 서식이 지정된 형태의 출력이라는 뜻이 
담겨있다.

서식이란 서류를 작성하는 양식이라고 사전적정의가 되어있으니, 서식이 지정된 형태의 출력이란 **출력양식이 만들어진 상태에서 출력을 진행한다.**라는 뜻이다.

```
제 나이는 10진수로 10살, 16진수로 A살입니다.
```
위 출력을 위해서는 10진수와 16진수의 형태로 서식을 지정하여야 한다.  
따라서 C언어는 이를 정의해놓았는데, 10진수는 %d로, 16진수는 %x로 지정해 놓았다.  
이들을 서식문자라고 한다.

따라서 위 문장을 간략하게 소스코드와 시키면

```
int a=10;

printf("제 나이는 10진수로 %d살, 16진수로 %X살입니다.", a, a);
```
위와 같이 printf함수로 3개의 인자를 넘겨 첫번째 인자에 있는 서식문자 2개에 두번째, 세번째 인자를 넘겨주는 형태가 된다.  
*(참고로 hex형은 %x로 지정하면 소문자로, %X로 지정하면 대문자로 16진수가 출력된다.)*

## 서식문자의 종류와 그 의미.

서식문자의 종류에 대해 나열하겠다.

|서식문자|자료형|출력 형태|
|:--|:--|:--|
|%d| char, short, int| 부호있는 10진수 정수|
|%ld| long | 부호 있는 10진수 정수|
|%lld| long long| 부호 있는 10진수 정수|
|%u| unsigned int| 부호 없는 10진수 정수|
|%o| unsigned int| 부호 없는 8진수 정수|
|%x, %X| unsigned int| 부호 없는 16진수 정수|
|%f|float, double| 10진수 방식의 부동소수점 실수|
|%Lf|long double| 10진수 방식의 부동소수점 실수|
|%e, %E|float, double|e또는 E방식의 부동소수점 실수|
|%g, %G|float, double| 값에 따라 %f와 %e사이에서 선택|
|%c| char, short, int| 값에 대응하는 문자|
|%s| char * | 문자열|
|%p| void * | 포인터의 주소 값|

## 정수 출력을 위한 서식문자들 %d, %u, %o, %x

8진수와 16진수 출력에 사용되는 %o와 %x를 이용해서는 양의 정수만 출력이 가능함을 위 표를 보고 알 수 있다.  
따라서 음의 정수를 출력하기 위해서는 %d서식문자를 사용하여야한다. 8진수와 16진수는 음의 정수 표현에 거의 사용되지 않는 표현법이기 때문에

서식문자에서도 양의 정수만 출력이 가능하도록 제한하고 있다.

그리고 그냥 %o, %x로 출력하면 8진수와 16진수 그대로 출력되고, %#o, %#x를 이용해 출력하면  
07, 0x6등 앞에 8진수와 16진수를 의미하는 문자가 붙어서 나온다.

16진수로 출력된 내용이 16진수임을 알리기위해서 가급적이면 #을 붙이는 것이 좋다.

## 실수 출력을 위한 서식문자들 %f, %e, %g

실수 출력에 f와 e출력은 다음과 같은 차이가 있다.

```
printf("%f", 0.1234); //0.123400
printf("%e", 0.1234); //0.1234000e-001
```
*0.1234000e-001*

이러한 형태의 출력을 e표기법이라고 하며, 지수형태의 출력을 의미합니다.

0.0001이 숫자를 지수 형태로 표현하면 다음과 같습니다.
> 0.1 x 10<sup>-3</sup>  

그런데 프로그램상에서는 지수를 표현할 수 없기 때문에 이를 다음과 같이 표현하기로 했다.
> 0.1e-3

%e와 %E의 차이는 대문자 소문자 표현차이일뿐이다.

%g는 간단하게 이해할 수 있다.
**"소수점 이하의 자릿수가 늘어나면 e표기법으로 출력한다."**

즉 간단한 실수라면 %f로, 소수점 이하의 자릿수가 긴 실수라면 %e로 출력하는 것이 크기를 파악하기 좋기때문에 이를 적절한 형태로 출력해준다.  
%g와 %G의 차이역시 대소문자 차이이다.  

## 필드 폭을 지정하여 정돈된 출력 보이기

서식문자에는 출력의 필드 폭을 지정하는 옵션을 추가할 수 있다.  
printf는 명령 프롬프트 상에 출력하기 때문에 보기 좋은 출력을 위해서 필드이 폭을 지정하는 것도 필요하다.  
다음 몇몇 예시를 통해 쉽게 이해하자.  
>%8d 필드 폭을 8칸 확보하고, 오른쪽 정렬해서 출력을 진행한다.
>
>%-8d 필드 폭을 8칸 확보하고, 왼쪽 정렬해서 출력을 진행한다.
```
printf("%-8d %5s \n", 123, 44);
printf("%-8d %5s \n", 12, 4);

/* 출력

    123        44
    12          4
*/
```
유용하게 사용되는 옵션이니 기억해두면 좋다.