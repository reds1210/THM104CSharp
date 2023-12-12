#第一章
-----------
## 存取修飾詞

* public
>所有程式都能訪問。沒有任何限制。
``` C#
public class MyClass
{
    public int MyProperty { get; set; }
}
```

* private
>只有相同類型的程式碼才能訪問。這是最嚴格的存取修飾詞。
```C#
public class MyClass
{
    private int myField;

    private void MyMethod()
    {
        // 只能在同一個類型中訪問 myField 和 MyMethod
    }
}

```

* protected
>只有相同類型和派生類型的程式碼才能訪問。
```C#
public class MyBaseClass
{
    protected int myField;
}

public class MyDerivedClass : MyBaseClass
{
    public void MyMethod()
    {
        // 在 MyDerivedClass 中可以訪問 MyBaseClass 的 protected 成員
        int value = myField;
    }
}

```

* internal
>只有相同組件（Assembly）中的程式碼才能訪問。
```C#
internal class MyInternalClass
{
    internal int MyProperty { get; set; }
}

```

* protected internal
>可以在相同組件中的任何地方訪問，而在不同組件中只能在派生類型中訪問。
```C#
public class MyBaseClass
{
    protected internal int MyField { get; set; }
}
```