---
version: 1.2.1
title: پایه
---

با مسائل پایه ای، انواع داده ها و عملگرهای پایه‌ای شروع می‌کنیم.


{% include toc.html %}

## شروع کردن

### نصب الیکسیر

دستورالعمل نصب کردن الیکسیر، برای هر نوع و هر توزیعی از هر سیستم‌عاملی، در آموزش های سایت رسمی الیکسیر، یعنی [Installing Elixir](http://elixir-lang.org/install.html) موجود است.

بعد از اینکه الیکسیر نصب شد، به راحتی میتوانید از نصب شدن آن با دستور گرفتن شماره نسخه الیکسیر، مطمئن شوید

    % elixir -v
    Erlang/OTP {{ site.erlang.OTP }} [erts-{{ site.erlang.erts }}] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

    Elixir {{ site.elixir.version }}

### امتحان کردن حالت تعاملی (Interactive Mode)

الیکسیر با یک محیط تعاملی، یعنی IEx معرفی شد که به ما اجازه می‌دهد دستورات الیکسیر را همانند چیزی که در ادامه میبینیم اجرا کنیم.

برای شروع، `iex` را اجرا کنید:

    Erlang/OTP {{ site.erlang.OTP }} [erts-{{ site.erlang.erts }}] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

    Interactive Elixir ({{ site.elixir.version }}) - press Ctrl+C to exit (type h() ENTER for help)
    iex>

و حالا بیایید با نوشتن چند دستور ساده، آن را امتحان کنیم:

```elixir
iex> 2+3
5
iex> 2+3 == 5
true
iex> String.length("The quick brown fox jumps over the lazy dog")
43
```

از اینکه در حال حاضر معنای این دستورات را متوجه نمی‌شوید، نگران نباشید. البته ما امیدواریم ایده‌ای در مورد آنها داشته باشید.

## انواع پایه‌ای داده ها

### نوع صحیح (Integers)

```elixir
iex> 255
255
```

پشتیبانی از اعداد باینری، مبنای هشت و مبنای شانزده (هگزادسیمال ها) به شکل زیر صورت میگیرد

```elixir
iex> 0b0110
6
iex> 0o644
420
iex> 0x1F
31
```

### نوع اعشاری (Floats)

در الیکسیر، قبل از علامت اعشار، حتما به حداقل یک عدد صحیح نیاز است. اعداد اعشاری دقت ۶۴ بیتی دارند و با e میتوانیم از توان و اعداد توان دار استفاده کنیم:

```elixir
iex> 3.14
3.14
iex> .14
** (SyntaxError) iex:2: syntax error before: '.'
iex> 1.0e-10
1.0e-10
```

### بولین ها

الیکسیر از `true` و `false` به عنوان نوع بولی پشتیبانی می‌کند؛ هر چیزی درست و صحیح است به جز `false` و `nil`:

```elixir
iex> true
true
iex> false
false
```

### اتم ها

یک اتم، مقدار ثابتیست که نام آن با مقدار آن برابر است. اگر با روبی آشنا باشید، این اسم (اتم) مترادف سیمبل است:

```elixir
iex> :foo
:foo
iex> :foo == :bar
false
```

انواع بولی `true` و `false` هم از جنس اتم هستند، به و به ترتیب معادل `:true` و `:false` هستند.

```elixir
iex> true |> is_atom
true
iex> :true |> is_boolean
true
iex> :true === true
true
```

نام ماژول ها در الیکسیر هم از جنس اتم است. `MyApp.MyModule` یک نوع اتم معتبر است، حتی اگر چنین ماژولی تعریف نشده باشد.

```elixir
iex> is_atom(MyApp.MyModule)
true
```

کاربرد دیگر اتم ها اینست که بتوان از کتابخانه های زبان ارلنگ (Erlang) استفاده کرد که منظور کتابخانه های درون‌ساخته‌ی این زبان هم هستند.

```elixir
iex> :crypto.strong_rand_bytes 3
<<23, 104, 108>>
```

### رشته ها (Strings)

رشته ها در الیکسیر از فرمت کدگذاری شده UTF-8 تبعیت می‌کنند و داخل دو دبل کوتیشن (علامت ") مشخص می‌شوند.

```elixir
iex> "Hello"
"Hello"
iex> "dziękuję"
"dziękuję"
```

رشته ها از چند خطی و کاراکتر های فرار (escape sequences) هم پشتیبانی میکنند:

```elixir
iex> "foo
...> bar"
"foo\nbar"
iex> "foo\nbar"
"foo\nbar"
```

الیکسیر انواع داده‌ای پیشرفته تری هم دارد. ما در این مورد در قسمت [مجموعه‌ها](../collections/) و [توابع](../functions/) بیشتر یاد خواهیم گرفت

## عملگرهای پایه‌ای

### ریاضیاتی

الیکسیر از عملگرهای `+`، `-`، `*` و `/` همانطور که مورد انتظار است (مشابه دیگر زبان ها و طبق تعاریف ریاضی) پشتیبانی می‌کند.
حتما به خاطر بسپارید که `/` همیشه مقداری اعشاری برمیگرداند:

```elixir
iex> 2 + 2
4
iex> 2 - 1
1
iex> 2 * 5
10
iex> 10 / 5
2.0
```

اگر شما به تقسیمی نیاز دارید که خروجی آن عدد صحیح باشد یا باقیمانه تقسیم را میخواهید، الکسیر دو تابع مفید را به ارمغان آورده است:

```elixir
iex> div(10, 5)
2
iex> rem(10, 3)
1
```

### بولی

الیکسییر عملگرهای بولی `||`، `&&` و `!` را فراهم آورده است. این عملگرها از هر نوع عملوندی پشتیبانی می‌کنند:

```elixir
iex> -20 || true
-20
iex> false || 42
42

iex> 42 && true
true
iex> 42 && nil
nil

iex> !42
false
iex> !false
true
```

همچنین سه عدد عملگر هم داریم که عملوند اول آنها _باید_ از نوع بولین باشد (یعنی `true` یا `false` باشد ) 

```elixir
iex> true and 42
42
iex> false or true
true
iex> not false
true
iex> 42 and true
** (ArgumentError) argument error: 42
iex> not 42
** (ArgumentError) argument error
```

نکته: عملگرهای `and` و `or` دقیقا به `andalso` و `orelse` در ارلنگ اشاره دارند.

### مقایسه‌ای

الیکسیر با تمامی عملگرهای مقایسه‌ای وارد شده است، عملگرهای `==`، `!=`، `===`، `!==`، `<=`، `>=`، `<` و `>`.

```elixir
iex> 1 > 2
false
iex> 1 != 2
true
iex> 2 == 2
true
iex> 2 <= 3
true
```

برای مقایسه سخت‌گیرانه در مورد انواع اعشاری و صحیح باید از `===` استفاده کنیم.

```elixir
iex> 2 == 2.0
true
iex> 2 === 2.0
false
```

یکی از قابلیت‌های مهم الیکسیر این است که هر دو نوع تایپی قابلیت مقایسه شدن را دارند؛ این مسئله به طور ویژه در هنگام مرتب کردن (sorting) مفید است. ما نیازی به حفظ کردن مرتبه انواع زیر (به ترتیب) نداریم، اما بهتر است از آنها مطلع باشیم:

```elixir
number < atom < reference < function < port < pid < tuple < map < list < bitstring
```

این میتواند باعث اتفاقات جالبی شود که از نظر الیکسیر معتبرند، مقایسه هایی که در باقی زبان‌ها یافت نمی‌شوند:

```elixir
iex> :hello > 999
true
iex> {:hello, :world} > [1, 2, 3]
false
```

### جادهی رشته

اگر پیش از این، از روبی استفاده کرده باشید، جادهی رشته در الیکسیر نیز مشابه آن است:

```elixir
iex> name = "Sean"
iex> "Hello #{name}"
"Hello Sean"
```

### پیونددهی رشته

رشته می‌تواند با عملگر `<>` پیوند داده شود:

```elixir
iex> name = "Sean"
iex> "Hello " <> name
"Hello Sean"
```
