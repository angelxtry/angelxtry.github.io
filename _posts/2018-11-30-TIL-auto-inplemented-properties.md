---
layout: post
comments: true
title: "[TIL] 2018-11-30"
tags:
  - C#
  - TIL
  - Auto Implemented Properties
  - nullable
  - const
  - static readonly
---

## Auto Implemented Properties

자동으로 구현된 속성

```cs
class Product
{
    private int serialNumber = 0;
    public int SerialNumber
    {
        get { return serialNumber; }
        set { serialNumber = value; }
    }

    public void PrintSerialNumber()
    {
        Console.WriteLine("Snack's Serial Number: " + SerialNumber);
    }
}
```

위 코드는 다음과 같이 표현할 수 있다.

```cs
class Product
{
    public int SerialNumber { get; set; }

    public void PrintSerialNumber()
    {
        Console.WriteLine("Snack's Serial Number: " + SerialNumber);
    }
}
```

set만 private으로 지정할 수도 있다.

```cs
public int SerialNumber { get; private set; }
```

6.0 이상에서는 초기화도 된다.

```cs
public int SerialNumber { get; private set; } = 0;
```

## 상속

```cs
class Person
{
    public string Name { get; private set; }
    public DateTime Birthday { get; private set; }

    public Person(string name, DateTime birthday)
    {
        Name = name;
        Birthday = birthday;
    }

    public int GetAge()
    {
        DateTime today = DateTime.Today;
        int age = today.Year - Birthday.Year;
        if (today < Birthday.AddYears(age))
            age--;
        return age;
    }
}

class Employee : Person
{
    public int Id { get; private set; }
    public string DivisionName { get; private set; }

    public Employee(string name, DateTime birthday, int id, string divisionName)
        : base(name, birthday)
    {
        Id = id;
        DivisionName = divisionName;
    }
}

Employee employee = new Employee
(
    "lee",
    new DateTime(1979, 7, 05),
    1094,
    "Info"
);
Console.WriteLine("{0}({1})는 {2}에 소속이다.", employee.Name, employee.GetAge(), employee.DivisionName);
```

base 키워드를 이용해서 부모에 param을 전달한다.

## `Visual Studio vs C#`

| VS   | C#  |
| ---- | --- |
| 2010 | 4.0 |
| 2012 | 5.0 |
| 2013 | 5.0 |
| 2015 | 6.0 |
| 2017 | 7.x |

언제 vs2017 써보나.

## nullable

```cs
Nullable<int> i = null;

int? i = null;
```

두 표현은 동일하다. 아래가 축약형.

```cs
Nullable<int> i = null;
Console.WriteLine(i);
```

이렇게 출력하면 아무것도 출력되지 않는다.

nullable은 2가지 property와 GetValueOrDefault 메소드를 사용할 수 있다.

```cs
if (i.HasValue)
{
    Console.WriteLine("i: " + i.Value);
}
else
{
    Console.WriteLine("null.");
}
```

HasValue는 true/false를 리턴.
Value는 값이 있다면 값을 리턴, 값이 없다면 런타임에 InvalidOperationException 예외를 발생.
예외를 피하고 싶다면 GetValueOrDefault 메소드 사용.

nullable 변수를 일반 변수에 할당하려고 하면 컴파일 에러가 발생한다.
이 경우 `??`를 사용할 수 있다.

```cs
int? i = null;
int nonNullableInt = i ?? 0;
Console.WriteLine("NonNullableInt: " + nonNullableInt);
```

nullable 변수를 초기화하지 않고 바로 사용하면 할당되지 않은 지역 변수를 사용하려고 했다는 예외가 발생된다.
class의 멤버변수로 선언된 nullable 변수는 초기화하지 않아도 예외가 발생하지 않는다.

nullable 변수가 null이라면 비교연산자를 이용하여 다른 값과 비교할 수 없다.
이때는 Nullable.Compare, Nullable.Equal을 사용한다.

```cs
Nullable<int> i = null;
int? j = 0;

if (Nullable.Compare<int>(i, j) < 0)
    Console.WriteLine("i < j");
else if (Nullable.Compare<int>(i, j) > 0)
    Console.WriteLine("i > j");
else if (Nullable.Compare<int>(i, j) == 0)
    Console.WriteLine("i == j");
else
    Console.WriteLine("Not Compare");
```

null과 0을 비교해봤다. 결과는 `i < j`가 나온다.
null은 0과 같지 않고 오히려 더 작다고 나온다. j를 -1로 해도 결과는 `i < j`다.

## const vs static readonly

const로 지정한 상수는 public으로 지정하지 않는 것이 좋다.
public으로 지정해서 다른 클래스가 접근할 수 있게 한 경우에는 const 대신 static readonly를 사용하는 것이 좋다.
왠만하면 static readonly로 사용하는 것이 좋을듯?
