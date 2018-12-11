---
layout: post
comments: true
title: "[TIL] 2018-12-03"
tags:
  - TIL
  - C#
  - delegate
  - anonymous funcion
  - lambda expressions
---

# delegate, anonymous function, lambda

책에서 본 내용을 정리했다.

delegate의 아주 단순한 설명이다. 찾아보니 더 심오한 내용이 많네. 일단 여기까지만.

## delegate

```cs
public delegate bool Judgement(int value);

public int Count(int[] numbers, Judgement judge)
{
    int count = 0;
    foreach (var n in numbers)
    {
        if (judge(n))
        {
            Console.WriteLine(n);
            count++;
        }
    }
    return count;
}

public void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };
    Judgement judge = IsEven;
    var count = Count(numbers, judge);
    Console.WriteLine(count);
    Console.ReadLine();
}

public bool IsEven(int n)
{
    return n % 2 == 0;
}
```

delegate로 선언된 Judgement도 일종의 타입이다.

Count 메서드는 int 타입 배열과 delegate를 인자로 받는다.

Do 메서드에서 delegate에 IsEven 함수를 지정했다.

delegate 변수에 할당하기 위해 IsEven 같은 함수를 별도로 작성하는 것은 번거롭다.

더 거슬러 올라가서 생각해보면 delegate를 선언하는 것도 번거롭다.

## anonymous method

익명 메서드

```cs
public int Count(int[] numbers, Predicate<int> judge)
{
    int count = 0;
    foreach (var n in numbers)
    {
        if (judge(n))
        {
            Console.WriteLine(n);
            count++;
        }
    }
    return count;
}

public void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };
    var count = Count(numbers, delegate(int n) { return n % 2 == 0; });
    Console.WriteLine(count);
    Console.ReadLine();
}
```

delegate 선언 부분을 제거하고 Count 메서드의 시그니처에 `Predicate<int>`가 추가됐다.

Predicate는 delegate의 generic 버전이다. .NET 2.0 부터 사용가능하다.

Count를 호출하는 쪽에서는 delegate 키워드를 사용하여 익명 메소드를 만들고 Count에 인자로 전달한다.

익명 메소드는 IsEven을 별도로 선언하지 않아도 간단하게 해결할 수 있다.

## 람다식

간단한 람다식을 만들어가는 과정

```cs
public static int Count(int[] numbers, Predicate<int> judge)
{
    int count = 0;
    foreach (var n in numbers)
    {
        if (judge(n))
        {
            Console.WriteLine(n);
            count++;
        }
    }
    return count;
}

public static void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };

    Predicate<int> judge = (int n) =>
    {
        if (n % 2 == 0)
            return true;
        else
            return false;
    };

    var count = Count(numbers, judge);
    Console.WriteLine(count);
    Console.ReadLine();
}
```

람다식을 가장 길게 풀어쓴 코드다.

judge 변수의 우변이 람다식이다.

delegate 키워드가 `=>`(람다 연산자)로 치환되었다.

`=>`의 좌변에 인수를 선언한다. 여기서는 (int n)이 인수를 의미한다.

`=>`의 우변에 메서드의 본문을 작성한다.

```cs
public static void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };

    var count = Count(numbers, (int n) =>
    {
        if (n % 2 == 0)
            return true;
        else
            return false;
    });
    Console.WriteLine(count);
    Console.ReadLine();
}
```

judge 변수에 담아 Count에 전달했던 것을 변수를 없애고 식을 직접 메서드의 인수로 지정했다.

```cs
public static void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };
    var count = Count(numbers, (int n) => { return n % 2 == 0; });
    Console.WriteLine(count);
    Console.ReadLine();
}
```

한 단계 더 축약했다.

다음과 같은 이유로 if 문을 없앨 수 있다.

return의 오른쪽에 식을 쓸 수 있다.

n % 2 == 0 은 식이며 bool 형의 값을 가진다.

```cs
public static void Do()
{
    var numbers = new[] { 5, 3, 9, 6, 7, 5, 8, 1, 0, 5, 10, 4 };
    var count = Count(numbers, (int n) => n % 2 == 0);
    Console.WriteLine(count);
    Console.ReadLine();
}
```

람다식에서 `{}`가 하나의 명령문만을 포함할 때는 `{}`와 return을 생략할 수 있다.

```cs
var count = Count(numbers, (n) => n % 2 == 0);
```

람다식에서 인수의 형을 생략할 수 있다.

컴파일러가 타입 추론을 한다.

```cs
var count = Count(numbers, n => n %2 == 0);
```

인수가 1개일 때는 `()`를 생략할 수 있다.

다음과 같이 활용할 수 있다.

```cs
var count = Count(numbers, n => 5 <= n && n < 10);
```

5 이상 10 미만인 수의 갯수

```cs
var count = Count(numbers, n => n.ToString().Contains('1'));
```

숫자에 1이 포함된 수의 갯수

Count 메서드에 `Predicate<T>` 형을 받아들이게 선언하면 람다식을 이용하여 다양하게 활용할 수 있다.

이런 상황을 '어떻게 하는가(How)가 아니라 무엇을 하는가(What)라는 것을 생각하면서 코드를 작성할 수 있게 됐다고 표현한다.

간단하게 추상도가 높아졌다라고 말한다.
