# **C언어 : 열거형, 공용체**

---

## 1. 열거형(enum)

> **열거형이란?**
>
> : "열거"는 나열한다는 뜻으로, 어떤 유사한 성질을 갖는 내용들을 정수로 표현할 때가 있는데,  
> 예로 1. red / 2. Blue / 3. Yellow / 4. green 과 같은 것을 저장해두고 사용하면 편하다.

> **열거형의 정의**
>
> `typedef enum 열거형이름 { 내용 } 열거형별칭;`
>
> - enum은 중괄호 {} 안에 사용할 문자를 지정하고 콤마로 구분한다.
>
> - 값을 정의하지 않으면 enum은 정수 0부터 차례대로 시작하게 되어있다.
>
> - 위의 예시를 열거형으로 정의하는 방법은 다음과 같다.
>
>   ```c
>   # typedef is not used
>   # 열거형 정의하기
>   enum color {
>     RED, BLUE, YELLOW, GREEN
>   };
>   
>   # typedef is used
>   # 열거형과 열거형 별칭 정의하기
>   typedef enum t_color {
>     RED, BLUE, YELLOW, GREEN
>   } s_color;
>   ```
>
>   - 값을 지정해주지 않으면 0부터 차례대로 시작된다.
>   - 자신이 원하는 값으로 지정할 수 있다.
>   - 지정한 값 이후에 다음 값을 지정하지 않으면 자동으로 이전 값의 +1을 한 값을 가지게 된다.

> **열거형 선언방법**
>
> > **typedef 사용하지 않은 경우** (`enum color`를 하나의 타입으로 생각해야 한다.)
> > : `enum color C;`
>
> > **typedef 사용한 경우**
> > : `s_color C;`
>
> - 위와 같이 선언하면 열거형 값들 중 하나의 값만 가져야 한다.
> - `C = RED` 이면, C는 RED 값을 가지면서 RED는 정수 0을 가리킨다.

> **열거형을 사용하는 이유?**
>
> : 가독성을 높이고, 편리하게 관리할 수 있으며 여러 실수들을 줄일 수 있다.

출처

- [Hardner's tistory blog : C언어 열거형](https://hardner.tistory.com/8)

---

## 2. 공용체(Union)

> **공용체(union)란?**
>
> - 공용체는 union 키워드로 정의하며 멤버 중에서 가장 큰 자료형의 공간을 공유합니다.  
> - 공용체는 구조체와 비슷하지만, 모든 구성원은 동일한 영역을 의미하며, 공용체의 인스턴스는 멤버 중 가장 큰 크기에 맞게 만들어 진다. 
> - 단일 값을 여러 형태로 표현하고 싶은 경우에 적용 할 수 있다.
> - 공용체의 구문은 매우 구조체와 비슷하지만, 모든 멤버가 동일한 기록 영역을 공유한다는 점에서 그 성격이 크게 다르다.

> **공용체 선언**
>
> ```c
> // 공용체 정의하기
> union 공용체이름 {
>     자료형 멤버이름;
> };
> 
> // 공용체 변수 선언하기
> union 공용체이름 변수이름;
> 
> // 공용체를 정의하는 동시에 변수 선언하기
> union 공용체이름 {
>     자료형 멤버이름;
> } 변수;
> 
> // 공용체를 정의하면서 공용체 별칭 정의하기
> typedef union 공용체이름 {
>     자료형 멤버이름;
> } 공용체별칭;
> 
> // 익명 공용체 정의하기
> typedef union {
>     자료형 멤버이름;
> } 공용체별칭;
> 
> // 공용체 별칭으로 변수 선언하기
> 공용체별칭 변수이름;
> ```

> **이해를 돕기위한 코드**
>
> - **코드 1**
>
>   ```c
>   typedef unsigned char   byte; // 1바이트
>   typedef unsigned int    u32; // 4바이트
>   
>   typedef struct {
>       byte    b1, b2, b3, b4;
>   } size_byte_t;
>   
>   typedef union {
>       u32         size; 
>       size_byte_t bytes;
>   } size_data_t;
>   ```
>
>   - `size_data_t` 공용체는 멤버 중 가장 크기가 큰 `size` 변수를 기준으로 메모리 공간이 형성된다. **(4바이트)**
>
>   - `size_data_t` 공용체의 멤버인 `size_byte_t` 구조체는 1바이트 변수 4개로 구성되어 있다.
>
>   - 바이트 단위로 구분하면 총 4개의 구분된 메모리 공간이 생기는데, 이 공간은 b1, b2, b3, b4 로 핸들링이 가능하다.
>
>   - 다음의 이미지를 보면서 이해해보자.
>
>     ![img](http://forum.falinux.com/_lectimages/+1000_994/992_union.png)

> **어떤식으로 사용할 수 있을까?**
>
> - 시리얼 통신이나, TCP/IP 통신에서 가끔 사용되기도 한다.
>
> - 시리얼로 한 바이트씩 정수 값을 받아 들이는 경우에 요긴하게 사용될 수 있다.
>
>   > - 상대방이 보내려는 정수 값 : 0x12345678
>   > - 상대방이 하나 씩 보낸 순서 : 0x12, 0x34, 0x56, 0x78
>   >
>   > 이럴 때, Union을 사용하면 편한데 대부분 버퍼로 받아서는 직접 계산을 한다.
>   >
>   >
>   > **보통의 경우 다음과 같이 계산한다.**
>   >
>   > `n_size = buff[3] * 256 * 256 * 256 + buff[2] * 256 * 256 + buff[1] * 256 + buff[0]` 
>
>   위의 복잡한 계산을 수행하지 않고 다음과 같은 코드로 메모리를 저장할 수 있다.
>
>   ```c
>   #include <stdio.h>
>   
>   typedef unsigned char   byte;
>   typedef unsigned int    u32;
>   
>   typedef struct {
>   
>       byte    b1, b2, b3, b4;
>   
>   } size_byte_t;
>   
>   typedef union {
>   
>       u32         size;
>       size_byte_t bytes;
>   } size_data_t;
>   
>   int main(void){
>   
>       size_data_t s_size;
>   
>     	// 시리얼로 4개의 바이트를 다음과 같이 받았다고 가정
>       s_size.bytes.b1 = 0xFF;
>       s_size.bytes.b2 = 0xEE;
>       s_size.bytes.b3 = 0xCC;
>       s_size.bytes.b4 = 0xAA;
>   
>       printf( "data : %x\n", s_size.size);
>       printf("b1 : %p, b2 : %p, b3 : %p, b4 : %p\n", &s_size.bytes.b1, &s_size.bytes.b2, &s_size.bytes.b3, &s_size.bytes.b4);
>       printf("size : %p\n", &s_size.size);
>       return 0;
>   }
>   
>   ```
>
>   <img width="829" alt="image-20201029192041983" src="https://user-images.githubusercontent.com/43171508/97556186-73d9eb80-1a1c-11eb-9365-bd6ff364570f.png">
>
>   

