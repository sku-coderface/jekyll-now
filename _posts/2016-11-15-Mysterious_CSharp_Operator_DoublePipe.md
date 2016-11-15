---
layout: post
title: "C#: 연산자 &&와 ||의 묘연함"
author: sftblw
---

C#의 연산자 오버로딩에서, 오버로딩할 수 없는 연산자가 있는데, 그것은 `&&`와 `||`입니다. 하지만 다른 방법으로 오버로딩할 수 있죠. 연산자 `true`, `false`, `&`(논리 연산자), `|`(논리 연산자) 를 오버로딩하면 자동으로 되는 셈입니다. 그런데 그 동작이 특이합니다.

## 동작

클래스에 오버로딩된 `T.true()`와 `T.false()` 연산자는 직접 호출할 수 없지만, 여러 상황에서 간접 호출됩니다. 이번 상황도 그러하구요.

`&&`와 `||`는 통상 Short-circuit operator라고 해서, 앞 항으로 전체 항이 뭔지 알 수 있으면 뒤에를 계산하지 않습니다. 반면, 비트 연산자가 아닌 `&`와 `|`는 연산대상을 모두 연산합니다.

`&&`와 `||`의 동작은 [스펙](https://msdn.microsoft.com/en-us/library/aa691312(v=vs.71).aspx)에서 다음처럼 말하고 있습니다.

- `x && y` 이면 `T.false(x) ? x : T.&(x, y)`
- `x || y` 이면 `T.true(x) ? x : T.|(x, y)`

그러니까 `&&`의 경우 x에 대해 오버로딩된 `T.false()` 연산자가 참이면 (값이 false 이면) 아예 y 값은 확인도 안 한다는 소리입니다.

## 그런데

그래서 `&&`의 경우 `T.false()` 연산자의 결과가 참이면 그냥 끝나야 하는데...

오늘 질문 들어온 코드는 그렇지 않았습니다. 지금에서야 알았지만, 왠지 `T.true()`나 `T.false()`가 한 번씩 더 있습니다. 

```
b1은 true, b2는 false임

XBool.Constructor(): not_initialized
XBool.Constructor(): not_initialized

=== evaluating b1 && b2 ===
XBool.false()
XBool.&()
XBool.true()
b1 && b2 == false
```
`true && false`의 호출 순서가 `T.false()` → `T.&()` → `T.true()` 라고요? 왜 마지막에 `T.true()`가??? 

그래서 개체의 이름을 나타내는 부분을 추가했는데요.

```
XBool.Constructor(): not_initialized
XBool.Constructor(): not_initialized

=== evaluating b1 && b2 ===
XBool.false(): b1
XBool.&(): b1, b2
XBool.Constructor(): not_initialized
XBool.true(): not_initialized
b1 && b2 == false
```

아하. `T.&()` 이나 `T.|()` 에서 반환한 새 개체에 대해 `T.true()`가 호출되는군요.

## 범인은 if문

그래서 알아차렸습니다. 범인은 if문이었습니다. if 에서 값을 평가하기 위해 `&&`이나 `||`에서 반환된 값에 `T.true()`를 한 번 더 호출한 것이죠. 그래서 if를 뗐더니 스펙에 나와있는대로입니다.

```
=== evaluating b1 && b2 ===
XBool.false(): b1
XBool.&(): b1, b2
XBool.Constructor(): not_initialized
b3 : False

=== evaluating if(b1 && b2) ===
XBool.false(): b1
XBool.&(): b1, b2
XBool.Constructor(): not_initialized
XBool.true(): not_initialized
b1 && b2 == false
```

정말 조심해야겠다고 생각한 게, 커스텀 `&&`이나 `||`, `T.|()`, `T.&()`는 `bool`이 아닌 `T`를 반환한다는 점입니다.  

## 부록: 테스트용 최종 코드

```csharp
using System;

class Program
{
	class XBool
	{
		public bool b;
		public string name = "not_initialized";
		public XBool(bool b)
		{
			Console.WriteLine("XBool.Constructor(): " + name);
			this.b = b;
		}
		public static explicit operator bool(XBool x)
		{
			Console.WriteLine("XBool.explicit-bool(): " + x.name);
			return x.b;
		}
		public static bool operator true(XBool x)
		{
			Console.WriteLine("XBool.true(): " + x.name);
			return x.b ? true : false;
		}
		public static bool operator false(XBool x)
		{
			Console.WriteLine("XBool.false(): " + x.name);
			return x.b ? false : true;
		}
		public static XBool operator &(XBool x, XBool y)
		{
			Console.WriteLine("XBool.&(): " + x.name + ", " + y.name);
			return new XBool(x.b && y.b);
		}
		public static XBool operator |(XBool x, XBool y)
		{
			Console.WriteLine("XBool.|(): " + x.name + ", " + y.name);
			return new XBool(x.b || y.b);
		}
	}
	static void Main(string[] args)
	{
		XBool b1 = new XBool(true); b1.name = "b1";
		XBool b2 = new XBool(false); b2.name = "b2";

		Console.WriteLine();
		Console.WriteLine("=== evaluating b1 && b2 ===");
		XBool b3 = b1 && b2;
		b3.name = "b3";
		Console.WriteLine("b3 : " + b3.b);

		Console.WriteLine();
		Console.WriteLine("=== evaluating if(b1 && b2) ===");
		if (b1 && b2) Console.WriteLine("b1 && b2 == true");
		else Console.WriteLine("b1 && b2 == false");

		Console.WriteLine();
		Console.WriteLine("=== evaluating if(b1 || b2) ===");
		if (b1 || b2) Console.WriteLine("b1 || b2 == true");
		else Console.WriteLine("b1 || b2 == false");

		Console.WriteLine();
		Console.WriteLine("=== evaluating if(b2 || b1) ===");
		if (b2 || b1) Console.WriteLine("b2 || b1 == true");
		else Console.WriteLine("b2 || b1 == false");
	}
}
```

## 참고

- [스펙 7.11.2 User-defined conditional logical operators](https://msdn.microsoft.com/en-us/library/aa691312(v=vs.71).aspx)
- [스펙 7.2.2 Operator overloading](https://msdn.microsoft.com/en-us/library/aa691324(v=vs.71).aspx)
- 스택오버플로우도 있었는데 까먹음