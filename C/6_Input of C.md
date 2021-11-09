# 키보드로부터 데이터 입력, C언어의 키워드
C언어에서 키보드로부터 다양한 형태의 데이터를 입력받을때는 scanf 라는 함수를 사용한다.
scanf의 사용방법은 printf와 매우 유사하다(의미는 정반대이다.)

scanf의 기본적인 사용 방법은 다음과 같다.
```
int main(void)
{
    int num;
    scanf("%d", &num);
}
```

키보드로부터 데이터를 입력 받으려면 데이터의 종류에 맞게 메모리 공간을 미리 할당해야 한다.  
따라서 변수를 미리 선언해주었다.  

scanf함수에는 한번에 여러 변수를 입력 받을 수 있는데, 이를 구분하기 위해서는 공백에 해당하는  
스페이스, 탭, 또는 엔터를 입력해야한다.

```
int main(void)
{
    int num;
    scanf("%d %d %d", &num, &num, &num);
}

/*
    입력 : 3 3 3
*/

```

## C언어의 표준 키워드

int, return과 같은 단어들은 이미 그 기능적 의미가 정해져 있다.  
기능적 의미가 정해져 C언어의 문법을 구성하는 단어들을 가리켜 **키워드(KEYWORD)**라고 한다.  

다음은 그 키워드의 종류다.

> auto  
> _Bool  
> break  
> case  
> char  
> _Complex  
> const  
> continue  
> default  
> do  
> double  
> else  
> enum  
> extern  
> float  
> for  
> goto  
> if  
> _Imaginary  
> return  
> restrict  
> short  
> signed  
> sizeof  
> static  
> struct  
> switch  
> typedef  
> union  
> unsigned  
> void  
> volatile  
> while   
