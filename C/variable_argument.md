# C 언어



## 가변 인자

> ### 함수에서 가변인자 사용하기
>
> : C 언어에서 함수를 사용하다 보면 printf, scanf같이 매개변수의 개수가 정해지지 않은 함수가 있습니다. 이렇게 매번 함수에 들어가는 인수(argument)의 개수가 변하는 것을 가변 인자(가변 인수, variable argument)라고 합니다.

> ### 가변 인자의 형태
>
> : 함수에서 가변 인자를 정의할 때는 고정 매개변수가 한 개 이상 있어야 하며 고정 매개변수 뒤에 ...을 붙여 매개변수의 개수가 정해지지 않았다는 표시를 해줍니다. 단, ... 뒤에는 다른 매개변수를 지정할 수 없습니다.
>
> ```c
> 반환값자료형 함수이름(자료형 고정매개변수, ...)
> {
> }
> ```

> ### 가변 인자 사용하기
>
> : 가변 인자를 사용하려면 stdarg.h 헤더 파일에 정의된 매크로를 이용해야 합니다. 따라서 #include로 stdarg.h 헤더 파일을 포함해줍니다. stdarg.h에 정의된 가변 인자 처리 매크로는 다음과 같습니다.
>
> - va_list: 가변 인자 목록. 가변 인자의 메모리 주소를 저장하는 포인터입니다.
>
> - va_start: 가변 인자를 가져올 수 있도록 포인터를 설정합니다.
>
> - va_arg: 가변 인자 포인터에서 특정 자료형 크기만큼 값을 가져옵니다.
>
>   > **※ va_arg는 어떻게 사용이 될까?**
>   >
>   > int num = va_arg(ap, int);를 실행하면 현재 ap에서 4바이트(int 크기)만큼 역참조하여 값을 가져온 뒤 ap를 4바이트만큼 순방향으로 이동시킵니다.
>
> - va_end: 가변 인자 처리가 끝났을 때 포인터를 NULL로 초기화합니다.
>
>   ```c
>   va_end(ap);    // 가변 인자 목록 포인터를 NULL로 초기화
>   ```





## ft_printf



### 코드 분해



#### 1. parse_width(va_list ap, char **str, t_info *info)

```c
void		parse_width(va_list ap, char **str, t_info *info)
{
	if (**str == '*')
	{
		info->width = va_arg(ap, int);
		if (info->width < 0)
		{
			if (info->zero)
				info->zero = 0;
			info->width *= -1;
			info->align = 1;
		}
	}
	while (**str >= '0' && **str <= '9')
	{
		info->width = (info->width * 10) + **str - '0';
		(*str)++;
	}
}
```

- flags 자리에 * ?
  : field의 폭을 가변 인수로 결정. (인수가 존재해야한다.)
  : 만약 가변인수가 음수면? [왼쪽 정렬, field 폭] 을 의미한다.

- flags 자리에 *가 아닌 field의 폭에 해당하는 숫자가 들어오면 width에 추가해준다.



### 참고 자료

- [코딩도장 - 가변인자](https://dojang.io/mod/page/view.php?id=577)