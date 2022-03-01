---
layout: code
title: Работа с Java в командной строке
date: 2022-02-27 11:43:51 +0000
categories: java command_line
tags: [java, command_line]
author: Evgeny
---

[Оригинал статьи с Habr](https://habr.com/ru/post/125210/ "Оригинал статьи с Habr") от 29 июля 2011 года. <br>(Qwertovsky) Валерий Квертовский (Full-Stack Software Engineer) <br>сайт [https://www.qwertovsky.com](https://www.qwertovsky.com "личный сайт") <br>[GitHub](https://github.com/Qwertovsky "на GitHub")

---

## Содержание

- [От простого к ...](#ot_prostogo_k)
- [Один файл](#odin_fail)
- [Отделяем бинарные файлы от исходников](#otdelyaem_binarnye_faily_ot_ishodnikov)
- [Используем пакеты](#ispolzuem_pakety)
- [Если в программе несколько файлов](#esli_v_programme_neskolko_faylov)

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
