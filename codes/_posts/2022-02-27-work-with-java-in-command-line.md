---
layout: code
title: Работа с Java в командной строке
date: 2022-02-27 11:43:51 +0000
categories: java command_line
tags: [java, command_line]
author: Evgeny
---

* TOC
{:toc}

[Оригинал статьи с Habr](https://habr.com/ru/post/125210/ "Оригинал статьи с Habr") от 29 июля 2011 года. <br>(Qwertovsky) Валерий Квертовский (Full-Stack Software Engineer) <br>сайт [https://www.qwertovsky.com](https://www.qwertovsky.com "личный сайт") <br>[GitHub](https://github.com/Qwertovsky "на GitHub")

---

## Содержание

- [От простого к ...](#ot_prostogo_k)
- [Один файл](#odin_fail)
- [Отделяем бинарные файлы от исходников](#otdelyaem_binarnye_faily_ot_ishodnikov)
- [Используем пакеты](#ispolzuem_pakety)
- [Если в программе несколько файлов](#esli_v_programme_neskolko_faylov)
- [Если удивляет результат](#esli_udivlyaet_rezultat)
- [Хорошо бы протестировать](#horosho_by_protestirovat)

---

!["Титульный"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-1.jpg "Java command line")

Сейчас уже никто не создаёт программы в консоли. Используя любимую IDE, разработчик чувствует себя неуютно за чужим компьютером, где её нет. <br>Решив разобраться в работе Ant и Maven, я поймал себя на том, что не смогу собрать приложение без них в консоли. <br>В данной статье я постарался уместить все этапы проектирования демонстрационного приложения, чтобы не искать справку по каждой команде на просторах интернета.

## От простого к ...  {#ot_prostogo_k}

Каждая программа обычно содержится в отдельном каталоге. Я придерживаюсь правила создавать в этом каталоге по крайней мере две папки: src и bin. В первой содержатся исходные коды, во второй - результат компиляции. В данных папках будет структура каталогов, зависящая от пакетов.

## Один файл  {#odin_fail}

Можно сделать и без лишних папок. <br>Берём сам файл HelloWorld.java

_HelloWorld.java_
```java
public class HelloWorld {
	public static void main(String[] args) {
		System.out.println("Hello World!");
	}
}
```

Переходим в каталог, где лежит данный файл, и выполняем команду:

```
javac -encoding utf-8 HelloWorld.java
```

В данной папке появится файл HelloWorld.class. Значит программа скомпилирована. Чтобы запустить выполняем команду:

```
java -classpath . HelloWorld
```

## Отделяем бинарные файлы от исходников  {#otdelyaem_binarnye_faily_ot_ishodnikov}

Теперь сделаем тоже самое, но с каталогами. Создадим каталог HelloWorld и в нём две папки src и bin. <br>Компилируем командой:

```
javac -encoding utf-8 -d bin src/HelloWorld.java
```

Здесь мы указали, что бинарные файлы будут сохраняться в отдельную папку bin и не путаться с исходниками.

Запускаем:

```
java -classpath ./bin HelloWorld
```

## Используем пакеты  {#ispolzuem_pakety}

А то, вдруг, программа перестанет быть просто HelloWorld-ом. Пакетам лучше давать понятное и уникальное имя. Это позволит добавить данную программу в другой проект без конфликта имён. Прочитав некоторые статьи, можно подумать, что для имени пакета обязательно нужен домен. Это не так. Домены - это удобный способ добиться уникальности. Если своего домена нет, воспользуйтесь аккаунтом на сайте (например, ru.habrahabr.mylogin). Он будет уникальным. Учтите, что имена пакетов должны быть в нижнем регистре. И избегайте использования спецсимволов. Проблемы возникают из-за разных платформ и файловых систем.

Поместим наш класс в пакет с именем com.qwertovsky.helloworld. Для этого добавим в начало файла строчку:

```java
package com.qwertovsky.helloworld;
```

В каталоге src создадим дополнительные каталоги, чтобы путь к файлу выглядел так: <br>`src/com/qwertovsky/helloworld/HelloWorld.java` <br>Компилируем:

```
javac -encoding utf-8 -d bin src/com/qwertovsky/helloworld/HelloWorld.java
```

В каталоге bin автоматически создастся структура каталогов как и в src.
```
HelloWorld
'---bin
'   '---com
'       '---qwertovsky
'           '---helloworld
'               '---HelloWorld.class
'---src
'   '---com
'       '---qwertovsky
'           '---helloworld
'               '---HelloWorld.java
```

Запускаем:

```
java -classpath ./bin com.qwertovsky.helloworld.HelloWorld
```

## Если в программе несколько файлов  {#esli_v_programme_neskolko_faylov}

Изменим программу.

_HelloWorld.java_
```java
package com.qwertovsky.helloworld;

public class HelloWorld {
	public static void main(String[] args) {
		int a = 2;
		int b = 3;
		Calculator calc = new Calculator();
		System.out.println("Hello World!");
		System.out.println(a + "+" + b + "=" + calc.sum(a, b));
	}
}
```
_Calculator.java_
```java
package com.qwertovsky.helloworld;

import com.qwertovsky.helloworld.operation.Adder;

public class Calculator {
	public int sum(int... a) {
		Adder adder = new Adder();
		for(int i : a) {
			adder.add(i);
		}
		return adder.getSum();
	}
}
```
_Adder.java_
```java
package com.qwertovsky.helloworld.operation;

public class Adder {
	private int sum;
	
	public Adder() {
		sum = 0;
	}
	
	public Adder(int a) {
		this.sum = a;
	}
	
	public void add(int b) {
		sum += b;
	}
	
	public int getSum() {
		return sum;
	}
}
```

Компилируем:

```
javac -encoding utf-8 -d bin src/com/qwertovsky/helloworld/HelloWorld.java
```

!["Ошибка"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-2.jpg "cannot find class Calculator")

Ошибка возникла из-за того, что для компиляции нужны файлы с исходными кодами классов, которые используютя (класс Calculator). Надо указать компилятору каталог с файлами с помощью ключа `-sourcepath`. <br>Компилируем:

```
javac -encoding utf-8 -sourcepath ./src -d bin src/com/qwertovsky/helloworld/HelloWorld.java
```

Запускаем:

```
java -classpath ./bin com.qwertovsky.helloworld.HelloWorld
```

!["Выполнение программы"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-3.jpg "run HelloWorld")

## Если удивляет результат  {#esli_udivlyaet_rezultat}

Есть возможность запустить отладчик. Для этого существует `jdb`. <br>Сначала компилируем с ключом `-g`, чтобы у отладчика была информация.

```
javac -encoding utf-8 -g -sourcepath ./src -d bin src/com/qwertovsky/helloworld/HelloWorld.java
```

Запускаем отладчик:

```
jdb -classpath bin -sourcepath src com.qwertovsky.helloworld.HelloWorld
```

!["Запуск отладчика"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-4.jpg "run jdb")

Отладчик запускает свой внутренний терминал для ввода команд. Справку по последним можно вывести с помощью команды `help`. <br>Указываем точку прерывания на 7 строке в классе Calculator:

```
stop at com.qwertovsky.helloworld.Calculator:7
```

!["Точка прерывания на 7 строке"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-5.jpg "stop on 7 of string")

Запускаем на выполнение:

```
run
```

!["Запуск на выполнение"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-6.jpg "run with jdb")

Чтобы соориентироваться можно вывести кусок исходного кода, где в данный момент находится курсор:

```
list
```

!["Вывод куска кода с курсором"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-7.jpg "out code_src with cursor")

Узнаем, что из себя представляет переменная a:

```
print a
```

!["тип переменной a"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-8.jpg "type of variable a")

Значение переменной a:

```
dump a
```

!["значение переменной a"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-9.jpg "value of variable a")

Указываем точку прерывания на 15 строке в классе Adder:

```
stop at com.qwertovsky.helloworld.operation.Adder:15
```

!["Точка прерывания на 15 строке"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-10.jpg "stop on 15 of string class Adder")

Продолжим исполнение:

```
cont
```

!["Продолжим выполнение"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-11.jpg "continue execution")

Выполним код в текущей строке и увидим, что sum стала равняться 2:

```
step
```

!["изменение значения переменной sum"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-12.jpg "change the value of variable sum")

Поднимемся из класса Adder в вызвавший его класс Calculator:

```
step up
```

!["из класса Adder в класс Calculator"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-13.jpg "go from class Adder to class Calculator")

Удаляем точку прерывания:

```
clear com.qwertovsky.helloworld.operation.Adder:15
```

!["удаляем точку прерывания в классе Adder"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-14.jpg "delete breakpoint in class Adder")

Выполним код в текущей строке:

```
step
```

!["выполним код в текущей строке"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-15.jpg "run code in current line")

Можно избежать захода в методы, используя команду:

```
next
```

!["не заходим в методы"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-16.jpg "not enter to metods")

Проверяем значение выражения и завершаем выполнение:

```
eval adder.getSum()
```

!["проверка значения выражения"]({{ site.url }}{{ site.baseurl }}/assets/images/codes/work-with-java-in-command-line-17.jpg "equal value expression")

## Хорошо бы протестировать  {#horosho_by_protestirovat}

Используем JUnit.

_TestCalculator.java_
```java
package com.qwertovsky.helloworld;

import static org.junit.Assert.*;

import java.util.Arrays;
import java.util.Collection;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized.Parameters;

@RunWith(value=org.junit.runners.Parameterized.class)
public class TestCalculator {
	int expected;
	int[] arg;
	
	@Parameters
	public static Collection<int[][]> parameters() {
		return Arrays.asList(new int[][][]{
			{ {4}, {2, 2} },
			{ {-1}, {4, -5} },
			{ {0}, {0, 0, 0} },
			{ {0}, {} }
			});
	}
	
	public TestCalculator(int[]  expected, int[] arg) {
		this.expected = expected[0];
		this.arg = arg;
	}
	
	@Test
	public void testSum() {
		Calculator c = new Calcilator();
		assertEquals(expected, c.sum(arg));
	}
}
```

## Исходные файлы  {#Ishodnye_faily}

[Исходные файлы]({{ site.url }}{{ site.baseurl }}/assets/source_code/Example-2_Rabota_s_Java_v_komandnoy_stroke.rar "source_code_example")

