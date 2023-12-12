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


# 變數型別
| C# 型別/關鍵字 | 範圍                                                    | 大小                                | .NET 類型                     |
| -------------- | ------------------------------------------------------- | ----------------------------------- | ----------------------------- |
| sbyte          | -128 到 127                                             | 帶正負號的 8 位元整數               | System.SByte                  |
| byte           | 0 至 255                                                | 不帶正負號的 8 位元整數	System.Byte |
| short          | -32,768 至 32,767                                       | 帶正負號的 16 位元整數              | System.Int16                  |
| ushort         | 0 到 65,535                                             | 不帶正負號的 16 位元整數            | System.UInt16                 |
| int            | -2,147,483,648 至 2,147,483,647                         | 帶正負號的 32 位元整數              | System.Int32                  |
| uint           | 0 到 4,294,967,295                                      | 不帶正負號的 32 位元整數            | System.UInt32                 |
| long           | -9,223,372,036,854,775,808 至 9,223,372,036,854,775,807 | 帶正負號的 64 位元整數	System.Int64 |
| ulong          | 0 到 18,446,744,073,709,551,615                         | 不帶正負號的 64 位元整數            | System.UInt64                 |
| nint           | 取決於平臺 （在執行時間計算）                           | 帶正負號的32位或64位整數            | System.IntPtr                 |
| nuint          | 取決於平臺 （在執行時間計算）                           | 不帶正負號的32位或64位整數          | System.UIntPtr                |