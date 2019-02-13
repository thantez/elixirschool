---
version: 1.2.0
title: توابع
---

در الیکسیر و بسیاری از زبان های تابعی، توابع گل‌های سرسبد هاین زبان ها هستند!
ما درباره‌ی انواع توابع در الیکسیر خواهیم آموخت، اینکه چه چیزی باعث تفاوت هرکدام آنها خواهد شد و چگونه باید از آنها استفاده کنیم.

{% include toc.html %}

## توابع بی‌نام (Anonymous Functions)

همانطور که از اسمش پیداست، یک تابع بی‌نام هیچ اسمی ندارد.
همانطور که در درس `Enum` خواهیم دید، این توابع  بارها به توابع دیگر ارسال خواهند شد.
برای تعریف یک تابع بی‌نام در الیکسیر، ما نیاز به کلمات کلیدی `fn` و `end` داریم.
بدون انها ما می‌توانیم هر تعداد پارامتر و بدنه‌ای جدا برای توابع با نشانه `->` تعریف کنیم

بیایید به مثالی پایه‌ای نگاه کنیم

```elixir
iex> sum = fn (a, b) -> a + b end
iex> sum.(2, 3)
5
```

### کوتاه شده

استفاده از توابع بی‌نام یک عمل متداول در الیکسیر است.
اینجا یک راه کوتاه شده برای این کار آورده شده است:

```elixir
iex> sum = &(&1 + &2)
iex> sum.(2, 3)
5
```

احتمالا باید حدس زده باشید که در حالت کوتاه شده، پارامتر های ما به صورت  `&1`، `&2`، `&3` و مشابه اینها، در دسترسند.

## تطابق الگو (Pattern Matching)

تطابق الگو در الیکسیر فقط محدود به متغیرها  نیست، همانطور که در این قسمت خواهید دید، این کار میتواند به طریقه‌بیان توابع (و امضای آنها) هم بسط داده شود.

الیکسیر از تطابق الگو استفاده می‌کند تا تمام حالات و ویژگی‌های قابل مطابقت دادن را پیدا کند و اولین ویژگی تطبیق داده شده را برای اجرا انتخاب کند:

```elixir
iex> handle_result = fn
...>   {:ok, result} -> IO.puts "Handling result..."
...>   {:ok, _} -> IO.puts "This would be never run as previous will be matched beforehand."
...>   {:error} -> IO.puts "An error has occurred!"
...> end

iex> some_result = 1
iex> handle_result.({:ok, some_result})
Handling result...

iex> handle_result.({:error})
An error has occurred!
```

## توابع نام‌دار

ما می‌توانیم توابعی نام‌دار تعریف کنیم که به راحتی قابل دسترسی در آینده باشند.
توابع نام‌دار داخل ماژول‌ها و با کلمه کلیدی `def` تعریف می‌شوند.
ما در مورد ماژول ها در درس بعدی بیشتر خواهیم آموخت.
در حال حاضر، فقط روی توابع نام‌دار تمرکز می‌کنیم.

توابعی که داخل یک ماژول تعریف شده اند در دیگر ماژول‌ها هم  قابل استفاده اند.
این قضیه در الیکسیر به طور ویژه برای ساختن بلوک کاربرد دارد:

```elixir
defmodule Greeter do
  def hello(name) do
    "Hello, " <> name
  end
end

iex> Greeter.hello("Sean")
"Hello, Sean"
```

اگر توابع ما تک‌خطی باشند، می‌توانیم از راه کوتاه‌تری استفاده کنیم با استفاده از `do:`:

```elixir
defmodule Greeter do
  def hello(name), do: "Hello, " <> name
end
```

همینقدر اطلاعاتی که از تطابق الگو داریم، ما را به سلاحی مجهز کرده است که بتوانیم از توابع بازگشتی استفاده کنیم.

```elixir
defmodule Length do
  def of([]), do: 0
  def of([_ | tail]), do: 1 + of(tail)
end

iex> Length.of []
0
iex> Length.of [1, 2, 3]
3
```

### Function Naming and Arity

قبلا در مورد توابعی که با نامی که گرفته اند و اریتی (Arity: تعداد آرگومان‌ها) داده شده، نام‌گذاری می‌شوند، آشنا شده‌ایم.
این بدان معناست که شما قادرید کارهایی مثل این را انجام دهید:

```elixir
defmodule Greeter2 do
  def hello(), do: "Hello, anonymous person!"   # hello/0
  def hello(name), do: "Hello, " <> name        # hello/1
  def hello(name1, name2), do: "Hello, #{name1} and #{name2}"
                                                # hello/2
end

iex> Greeter2.hello()
"Hello, anonymous person!"
iex> Greeter2.hello("Fred")
"Hello, Fred"
iex> Greeter2.hello("Fred", "Jane")
"Hello, Fred and Jane"
```

ما نام توابع لیست شده را در تفریظ‌های (کامنت‌ها) بالا آورده‌ایم.
اولین پیاده‌سازی، هیچ آرگومانی ندارد و به همین دلیل به صورت `hello/0` مشخص شده است، دومی یک آرگومان  گرفته، پس به صورت  `hello/1` نشان داده شده و به همین ترتیب تا آخر.
برخلاف بارگذاری‌های مضاعف توابع (function overloads) در بسیاری از زبان‌ها، ما در اینجا شاهد تفکر _تفاوت داشتن_ توابع از یکدیگر هستیم.
(تطابق الگو که همین چند لحظه پیش تشریح شد، تنها زمانی اعمال می‌شود که چندین تعریف برای تعریف توابع با تعداد آرگومان‌های _یکسان_ وجود داشته باشد.)

### توابع و تطابق الگو

در پشت تمام این داستان‌ها، توابع در حال تطابق دادن الگو روی آرگومان‌هایی هستند که با آنها صدا زده می‌شوند.

بگذارید اینطور بگویم، ما نیاز به تابعی داریم که یک مپ را بگیرد اما قرار است ما فقط به یک کلید خاص از این مپ توجه کنیم.
ما می‌توانیم آرگومان را برای آن کلید خاص تطبیق الگو کنیم" به این صورت که... :

```elixir
defmodule Greeter1 do
  def hello(%{name: person_name}) do
    IO.puts "Hello, " <> person_name
  end
end
```

حالا، بیایید بگوییم که ما یک مپ داریم که فردی به اسم فِرد راتوصیف می‌کند:
```elixir
iex> fred = %{
...> name: "Fred",
...> age: "95",
...> favorite_color: "Taupe"
...> }
```

این، نتیجه‌ی صدا زدن `Greeter1.hello/1` با مپ فرد است:

```elixir
# call with entire map
...> Greeter1.hello(fred)
"Hello, Fred"
```

چه اتفاقی می‌افتد وقتی که ما تابعی را با مپی صدا می‌زنیم که شامل کلید `:name` _نیست_ ؟!

```elixir
# call without the key we need returns an error
...> Greeter1.hello(%{age: "95", favorite_color: "Taupe"})
** (FunctionClauseError) no function clause matching in Greeter1.hello/1

    The following arguments were given to Greeter1.hello/1:

        # 1
        %{age: "95"}

    iex:12: Greeter1.hello/1

```

علت این رفتار آنست که تطابق الگوی الیکسیر در مورد آرگومان‌هایی که تابع با آن صدا زده می‌شود خلاف اریتی هست که تابع با آن تعریف شده است.

بیایید به این فکر کنیم که وقتی داده‌ها وارد `Greeter1.hello/1` می‌شوند، چطور به نظر می‌رسند:

```Elixir
# incoming map
iex> fred = %{
...> name: "Fred",
...> age: "95",
...> favorite_color: "Taupe"
...> }
```
`Greeter1.hello/1` انتظار آرگومانی شبیه این را دارد:
```elixir
%{name: person_name}
```
در `Greeter1.hello/1`، مپی که ما آن را ارسال کرده‌ایم (یعنی همان `fred`) متفاوت با آرگومان ما ارزیابی می‌شود (`%{name: person_name}`):

```elixir
%{name: person_name} = %{name: "Fred", age: "95", favorite_color: "Taupe"}
```

یعنی، می‌فهمد که اینجا کلیدی هست که با `name`ی که در مپ آمده، مطابقت دارد
ما یک تطابق یافتیم! و به عنوان نتیجه‌ای برای این موفقیت، مقدار کلید `:name` در مپی که سمت راست است (مثلا فرد) محدود به متغیری می‌شود که در سمت چپ است (`person_name`).

حالا، چه می‌شود اگر بخواهیم اسم فِرد را در `person_name` قرار دهیم با توجه به اینکه ما هنوز می‌خواهیم که اطلاعات کل مپ person را نگهداری کنیم؟
در این وضعیت، چون ما فقط در مورد کلید `:name`مپمان عمل تطابق الگو را انجام داده‌ایم، پس مقدار آن کلید به یک متغیر بند (بایند شدن یا binding) خواهد شد و تابع از باقیمانده مپ فرد اطلاعاتی نخواهد داشت.

به این ترتیب، برای حفظ کردن و نگهداری کل مپ، ما نیاز داریم که کل مپ را به یک متغیر نگاشت کنیم تا توانایی استفاده از آن برایمان فراهم شود.

بیایید یک تابع جدید بسازیم:
```elixir
defmodule Greeter2 do
  def hello(%{name: person_name} = person) do
    IO.puts "Hello, " <> person_name
    IO.inspect person
  end
end
```

به خاطر داشته باشید که الیکسیر همانگونه که نشان داده شده عمل تطابق الگو را انجام می‌دهد؛ بنابراین، در این مثال هر قسمت در مورد چیزی مورد تطبیق الگو قرار میگیرد که از آرگومان آمده است.
بیایید با توضیح سمت راست شروع کنیم:

```elixir
person = %{name: "Fred", age: "95", favorite_color: "Taupe"}
```

حالا، `person` معادل مپ فرد است و به تمام آن محدود شده است.
سراغ تطابق الگو بعدی می‌رویم:
```elixir
%{name: person_name} = %{name: "Fred", age: "95", favorite_color: "Taupe"}
```

حالا این مشابه تابع اصلی `Greeter1` است که ما مپ را مورد تطابق الگو قرار داده‌ایم و فقط نام فرد برگردانده می‌شود.
چیزی که به دست آمده، دو متغیر است که از هرکدام به جای دیگری می‌توان استفاده کرد:
1. `person`, برای اشاره به `%{name: "Fred", age: "95", favorite_color: "Taupe"}`
2. `person_name`, برای اشاره به `"Fred"`

پس وقتی ما `Greeter2.hello/1` را صدا می‌زنیم، ما می‌توانیم از کل اطلاعات فرد استفاده کنیم:

```elixir
# call with entire person
...> Greeter2.hello(fred)
"Hello, Fred"
%{age: "95", favorite_color: "Taupe", name: "Fred"}
# call with only the name key
...> Greeter2.hello(%{name: "Fred"})
"Hello, Fred"
%{name: "Fred"}
# call without the name key
...> Greeter2.hello(%{age: "95", favorite_color: "Taupe"})
** (FunctionClauseError) no function clause matching in Greeter2.hello/1

    The following arguments were given to Greeter2.hello/1:

        # 1
        %{age: "95", favorite_color: "Taupe"}

    iex:15: Greeter2.hello/1
```

خب. تا اینجا فهمیدیم که تطابق الگو در لایه‌های مختلفی صورت می‌گیرد چون هر آرگومان به یک داده‌ی مستقلی مطابق می‌شود، فعلا از متغیرهایی که داخل تابع فراخوانی میشوند، گذر می‌کنیم.

اگر جای  `%{name: person_name}` و `person` را در لیست عوض کنیم، نتیجه‌ی یکسانی میگیریم؛ زیرا هرکدام آنها به تنهایی با فرد مطابقت دارند.

جای متغیر و مپ را عوض می‌کنیم:
```elixir
defmodule Greeter3 do
  def hello(person = %{name: person_name}) do
    IO.puts "Hello, " <> person_name
    IO.inspect person
  end
end
```

و آن را با همان داده‌هایی که در `Greeter2.hello/1` استفاده کردیم فراخوانی می‌کنیم:
```elixir
# call with same old Fred
...> Greeter3.hello(fred)
"Hello, Fred"
%{age: "95", favorite_color: "Taupe", name: "Fred"}
```

به یاد داشته باشید، هر چند که به نظر می‌آید که در `%{name: person_name} = person` ما تطابق الگویی در مورد `%{name: person_name}` نسبت به متغیر `person` داشته باشیم، اما در حقیقت  ما در حال انجام دادن عمل تطابق الگو روی _هر_ آرگومانی هستیم که ارسال شده است.

**خلاصه:** در توابع، تطابق الگو روی هر داده‌ای که به طور مستقل به تابع فرستاده می‌شود، صورت می‌گیرد.
با این قابلیت، ما می‌توانیم مقادیری را به متغیرهایی جداگانه درون خود تابع، نسبت بدهیم.

### توابع مخفی

ما نمی‌توانیم از دیگر ماژول‌ها بخواهیم که به یک تابع خاصی (از ماژولی که در آن تعریف شده‌اند) دسترسی داشته باشند، اما می‌توانیم تابعی را مخفی کنیم.
توابع مخفی فقط داخل ماژول خودشان قابل فراخوانی هستند.
ما در الیکسیر، آنها را با `defp` تعریف می‌کنیم:

```elixir
defmodule Greeter do
  def hello(name), do: phrase() <> name
  defp phrase, do: "Hello, "
end

iex> Greeter.hello("Sean")
"Hello, Sean"

iex> Greeter.phrase
** (UndefinedFunctionError) function Greeter.phrase/0 is undefined or private
    Greeter.phrase()
```

### محافظان (Guards)

به طور خلاصه محافظان را در درس [ساختار‌های کنترلی](../control-structures) پوشش داده‌ایم، اکنون می‌خواهیم ببینیم چگونه می‌شود از آنها در توابع نام‌دار استفاده کرد.
الیکسیر هر محافظی که در مورد یک تابع تطبیق یافته (تابع مدنظر) تعریف شده است را یکبار مورد ارزیابی قرار می‌دهد. (هر گاردی که برای تابع تعریف شده را یکبار تست می‌کند)

در مثال زیر ما دو تابع با امضا (طریقه‌ی تعریف) مشابه داریم که وابسته به محافظانی هستند که محاسبه می‌کنند کدامشان، طبق نوع آرگومان، مورد استفاده قرار گیرد:

```elixir
defmodule Greeter do
  def hello(names) when is_list(names) do
    names
    |> Enum.join(", ")
    |> hello
  end

  def hello(name) when is_binary(name) do
    phrase() <> name
  end

  defp phrase, do: "Hello, "
end

iex> Greeter.hello ["Sean", "Steve"]
"Hello, Sean, Steve"
```

### آرگومان‌های پیش‌فرض

اگر بخواهیم یک مقدار پیش‌فرضی را برای یک آرگومان در نظر بگیریم باید از نحوی (سینتکسی)  به شکل `argument \\ value` استفاده کنیم:

```elixir
defmodule Greeter do
  def hello(name, language_code \\ "en") do
    phrase(language_code) <> name
  end

  defp phrase("en"), do: "Hello, "
  defp phrase("es"), do: "Hola, "
end

iex> Greeter.hello("Sean", "en")
"Hello, Sean"

iex> Greeter.hello("Sean")
"Hello, Sean"

iex> Greeter.hello("Sean", "es")
"Hola, Sean"
```

وقتی محافظ مثالمان را با آرگومان پیش‌فرض دار ترکیب می‌کنیم، ما با یک مشکل مواجه می‌شویم.
بیایید ببینیم که چه می‌شود:

```elixir
defmodule Greeter do
  def hello(names, language_code \\ "en") when is_list(names) do
    names
    |> Enum.join(", ")
    |> hello(language_code)
  end

  def hello(name, language_code \\ "en") when is_binary(name) do
    phrase(language_code) <> name
  end

  defp phrase("en"), do: "Hello, "
  defp phrase("es"), do: "Hola, "
end

** (CompileError) iex:31: definitions with multiple clauses and default values require a header.
Instead of:

    def foo(:first_clause, b \\ :default) do ... end
    def foo(:second_clause, b) do ... end

one should write:

    def foo(a, b \\ :default)
    def foo(:first_clause, b) do ... end
    def foo(:second_clause, b) do ... end

def hello/2 has multiple clauses and defines defaults in one or more clauses
    iex:31: (module)
```

الیکسیر علاقه‌ای به آرگومان‌های پیش‌فرض در توابع تطبیق شونده‌ی چندگانه ندارد، این می‌تواند کمی  گیج کننده باشد.
برای همین ما آرگومان‌های پیش‌فرض را به یک تابع ابتدایی اضافه کردیم:

```elixir
defmodule Greeter do
  def hello(names, language_code \\ "en")

  def hello(names, language_code) when is_list(names) do
    names
    |> Enum.join(", ")
    |> hello(language_code)
  end

  def hello(name, language_code) when is_binary(name) do
    phrase(language_code) <> name
  end

  defp phrase("en"), do: "Hello, "
  defp phrase("es"), do: "Hola, "
end

iex> Greeter.hello ["Sean", "Steve"]
"Hello, Sean, Steve"

iex> Greeter.hello ["Sean", "Steve"], "es"
"Hola, Sean, Steve"
```
