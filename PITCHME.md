# Code Like the Go Team

---

## This Talk

[https://cda.ms/jP](https://cda.ms/jP)

---

#### Write Code Like the Go Team

- how to organize your code into packages, and what those packages should contain  
- code patterns and conventions that are prevalent in the standard library  
- how to write your code to be more clear and understandable  
- unwritten Go conventions that go beyond “go fmt” and make you look like a veteran Go contributor wrote it

Note:
- Как организовать код в пакеты и что эти пакеты должны содержать
- Шаблоны и соглашения по коду, которые приняты в стандартной библиотеке
- Как писать более чистый и читабельный код
- негласные соглашения которые делают ваш код таким, будто его писал ветеран контрибьютор на Go

---

#### Outline

- Packages
- Naming Conventions
- Source Code Conventions

Note:
- Пакеты
- Соглашения об именовании
- Соглашения об организации кода

---

## Packages

---

#### Code Organization

There are two key areas of code organization in Go that will make a huge impact on the usability, testability, and functionality of your code:

Note:
Есть две ключевых вещи в организации кода, которые имеют большое влияние на используемость, тестируемость и функциональность вашего кода

---

#### Code Organization

- Package Naming
- Package Organization

Note:
- Именование пакетов
- Организация пакетов 
- если у вас сотня файлов в одном пакете, этот код сложно использовать
- или наоборот сотни пакетов и в каждом содержится всего один файл с кодом

---

#### Code Organization

We can't talk about them separately because one influences the other.

Note:
Нельзя говорить о них по раздельности, потому-что они имеют влияние друг на друга

---

#### Package Organization - Libraries

- Packages contain code that has a single purpose

```
	archive	cmd	crypto	errors	go	index	math	path	sort	syscall	unicode bufio	compress  database  expvar	hash	internal  mime      reflect	strconv	testing	unsafe builtin	container debug	flag  
```

Note:
- один пакет - одно предназначение

---

#### Package Organization - Libraries

When a group of packages provides a common set of functionalities with different implementations, they're organized under a parent.  Look at the contents of package *encoding*:

```
	ascii85     base32      binary      encoding.go hex         pem
	asn1        base64      csv         gob         json        xml
```

Note:
- Иногда группа пакетов предоставляет общий набор функциональности, но разной реализацией, тогда они объединяются одним общим пакетом. Например пакет encoding
- И в родительском пакете есть интерфейс, который реализуют все дочерние пакеты

---

#### Package Organization - Libraries

Some commonalities:

- Packages names describe their purpose
- It's very easy to see what a package does by looking at the name
- Names are generally short
- When necessary, use a descriptive parent package and several children implementing the functionality -- like the *encoding* package

Note:
- Наименование всегда описывает назначение, никаких utility
- Должно быть легко понять что делает пакет просто взглянув на имя
- Имена как правило короткие
- Когда необходимо используйте родительский пакет и несколько дочерних, которые реализуют функциональность, например как encoding

---


#### Package Organization - Applications

The packages we've seen are all libraries.  They're intended to be imported and used by some executable program like a service or command line tool.

What should the organization of your executable applications look like?

Note:
Пакеты которые были приведены в пример это библиотеки, они предназначены быть импортированными для использования в других приложениях.
Как должна выглядеть организация кода вашего приложения?

---

#### Package Organization - Applications

When you have an application, the package organization is subtly different.  The difference is the command, the executable that ties all of those packages together.

Note:
Когда у вас приложение, организация пакетов другая, отличие в том что у вас есть запускаемая команда, или то что объединяет все пакеты вместе

---

#### Package Organization - Applications

Application package organization has a huge impact on the testability and functionality of your system.

Note:
Организация пакетов в приложении имеет большое влияние на тестируемость и функциональность вашей системы.
Часто люди имеют большие проблемы с тестированием кода, потому-что пакеты неправильно организованы

---

#### Package Organization - Applications

When writing an application your goal should be to write code that is easy to understand, easy to refactor, and easy for someone else to maintain.

Note:
При написании приложения ваша цель написать легко читаемый код, код который можно легко отрефактирить и легко поддерживать другим людям.
Go из коробки дает возможноть писать простой и понятный код по сравнению с некоторыми языками (c#)

---

#### Package Organization - Applications

Most libraries focus on providing a singularly scoped function; logging, encoding, network access.

Your application will tie all of those libraries together to create a tool or service.  That tool or service will be much larger in scope. 

Note:
Цель большинства библиотек - предоставить набор функций для выполнения одной цели: логирование, кодирование, сетевые функции
Ваше же приложение должно объединить функциональность нескольких библиотек для создания вашего микросервиса, который может быть довольно объемным

---

#### Package Organization - Applications

When you're building an application, you should organize your code into packages, but those packages should be centered on two categories:

- Domain Types
- Services

Note:
Когда вы создаете приложение, вы должны организовывать код в пакеты, но все пакеты должны делиться на две основные категории:
- Доменные типы
- Сервисы

---

#### Package Organization - Applications

*Domain Types* are types that model your business functionality and objects. 

*Services* are packages that operate on or with the domain types.

https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1

Note:
- Доменные типы моделируют вашу бизнес-функциональность и сущности (нужно ли объяснять?)
- Сервисы - пакеты которые оперируют над или с доменными объектами

---

#### Package Organization - Applications

A domain type is the substance of your application.  If you have an inventory application, your domain types might include *Product* and *Supplier*.  If you have an HR administration system, your domain types might include *Employee*, *Department*, and *Business Unit*.

Note:
- Доменные типы это сущность в вашем приложении. Если у вас приложение для инвентаризации товаров, то у вас скорее всего будут типы "товар" и "поставщик".
- Если у вас система для управления персоналом, то скорее всего будут типы "Работник", "Отдел", "Подразделение"

---

#### Package Organization - Applications

The package containing your domain types should also define the interfaces between your domain types and the rest of the world.  These interfaces define the things you want to do with your domain types.

- *ProductService*  
- *SupplierService*
- *AuthenticationService*
- *EmployeeStorage*
- *RoleStorage*

Note:
Пакеты, которые содержат домены должны определять интерфейсы для них которые будут описывать их взаимодействие с остальным миром. Эти интерфейсы опредеяют вещи, которые вы хотите совершать с доменами.
Например есть работник Employee и предположим его нужно сохранять - save, например в БД

---

#### Package Organization - Applications

Your domain type package should be the root of your application repository.  This makes it clear to anyone opening the codebase what types are being used, and what operations will be performed on those types.

Note:
Пакет с доменными типами должен быть корневым в репозитории вашего приложения. Это позволяет каждому кто открывает ваш код сразу понять что за типы используются и какие операции над ними происходят

---

#### Package Organization - Applications

The domain type package, or *root* package of your application should not have any external dependencies.  
> It exists for the sole purpose of describing your types and their behaviors.

Note:
Пакет с доменными типами или корневой пакет не должен иметь никаких внешних зависимостей
Он существует лишь для того чтобы описать типа и их поведение. То есть говорит вам "Что" и "Как"

---

#### Package Organization - Applications

The implementations of your domain interfaces should be in separate packages, organized by dependency.

Note:
Реализация интерфейсов для работы с доменами должна находиться в отдельных пакетах, организованных по внешним зависимостям
Например: есть employee и есть интерфейс EmployeeStorage который определяет метод save, то для реализации создаем пакет postgres внутри которого реализуем интерфейс EmployeeStorage для сохранения в postgres бд

---

#### Package Organization - Applications

Dependencies include:

- External data sources
- Transport logic (http, RPC)

You should have one package per dependency.

Note:
Зависимости:
 - внешние источники данных
 - транспорт (http, rpc, ШИНА)

---

#### Package Organization - Applications

Why one package per dependency?

- Simple testing
- Easy substitution/replacement
- No circular dependencies

Note:
Почему один пакет на одну зависимость?
- Упрощает тестирование (моки, DI)
- Упрощает замену
- Нет циклических зависимостей

---

## Conventions

---

#### Naming Conventions

> there are two hard things in computer science: 
> cache invalidation, naming things, and off-by-one errors
> - Every developer on Twitter


Naming things *is* hard, but putting some thought into your type, function, and package names will make your code more readable.

Note:
В программировании есть только две по-настоящему сложных вещи: инвалидация кэша, именование и ошибка неучтённой единицы (каждый разработчик в твиттере)
Именование это сложно, но если подумать, то можно сделать название типа, функции или пакета более читабельным
Сравнивает Go со скалой (мы могли быть сделать го как скала, но мы сделали его более читаемым)

---

#### Naming Conventions - Packages

A package name should have the following characteristics:

- short
	- Prefer "transport" over "transportmechanisms"

- clear 
	- Name for clarity based on function: "bytes" 
	- Name to describe implementation of external dependency: "postgres" 

Note:
Именование:
- Коротко
    - предпочти "transport" over "transportmechanisms"
- Ясно
	- Именование основанное на функциональности - bytes
	- Именование основанное на внешней зависимости - postgres

---

#### Naming Conventions - Packages

Packages should provide functionality for one and only one purpose.  Avoid *catchall* packages:

-	util
-	helpers
-	etc.

Frequently they're a sign that you're missing an interface somewhere.

Note:
Не допускайте появления пакетов util, helpers etc
Чаще всего они означают что у вас где-то не хватает интерфейса

---

#### Naming Conventions - Packages

 `util.ConvertOtherToThing()` should probably be refactored into a Thinger interface

*catchall* packages are always the first place you'll run into problems with testing and circular dependencies, too.

Note:
 `util.ConvertOtherToThing()` скорее всего должно быть в Thinger interface
всеохватывающие пакеты это первое месты где у вас начнутся проблемы с тестированием и циклическими зависимостями.
Пример: доменный тип зависит от util потому-что нам нужно знать как конвертировать его и пакет postgres так же имеет зависимость util 

---

#### Naming Conventions - Variables

Some common conventions for variable names:

- use camelCase not snake_case
- use single letter variables to represent indexes
	- `for i:=0; i < 10; i++ {}`
- use short but descriptive variable names for other things
	- `var count int`
	- `var cust Customer`

Note:
некоторые общие соглашения об именовании:
- использовать camelCase
- использовать однобуквенные переменные для представленя индексов
- использовать короткие, но описательные имена для остальных переменных

---

#### Naming Conventions - Variables

There are no bonus points in Go for obfuscating your code by using unnecessarily short variables.  Use the scope of the variable as your guide.  The farther away from declaration you use it, the longer the name should be.

Note:
Нету никаких бонусов за обфусирование вашего кода с использованием слишком коротким переменных.
Понятие о длине может дать область видимости вашей переменной, чем дальше от объявления вы ее используете, тем длиннее должно быть имя
Например объявлена на 1-й и использована на 3-й строке
либо использована на 300-й строке

---

#### Naming Conventions - Variables

- use repeated letters to represent a collection/slice/array
	- `var tt []*Thing`
- inside a loop/range, use the single letter
	- `for i, t := range tt {}`

These conventions are common inside Go's own source code.

Note:
- используйте повторяющиеся буквы для именоавания коллекций, слайсов
- внутри цикла можно исползовать одну букву

---

#### Naming Conventions - Functions and Methods

Avoid a package-level function name that repeats the package name.  

	GOOD:  log.Info()
	BAD:   log.LogInfo()

The package name already declares the purpose of the package, so there's no need to repeat it.

Note:
Не дублируйте в названии функции название пакета

---

#### Naming Conventions - Functions and Methods

Go code doesn't have setters and getters.

	GOOD:  custSvc.Customer()
	BAD:   custSvc.GetCustomer()

Note:
В Го нет сеттеров и геттеров

---

#### Naming Conventions - Interfaces

If your interface has only one function, append "-er" to the function name:
```go
	type Stringer interface{
		String() string
	}
```

Note:
Если в интерфейсе всего одна функция - можно добавить -er к названию интерфейса

---

#### Naming Conventions - Interfaces

If your interface has more than one function, use a name to represent its functionality:

```go
	type CustomerStorage interface {
		Customer(id int) (*Customer, error)
		Save(c *Customer)  error
		Delete(id int) error
	}
```

Note:
Если в интерфейсе более одной функции, имя должно отображать функциональность интерфейса

---

#### Naming Conventions - Interfaces

Some purists think that all interfaces should end in `-er`.  I think interfaces should be descriptive and readable.  

```go
	type CustomerStorer interface {} // MEH
	type CustomerStorage interface {} // Better
```

Note:
Некоторые считают что все интерфейсы должны заканчивать на `-er`. Брайн считает что названия должны быть описывающими и читабельными

---

#### Naming Conventions - Source Code

Inside a package separate code into logical concerns.

If the package deals with multiple types, keep the logic for each type in its own source file:
```
	package: postgres

	orders.go
	suppliers.go
	products.go
```

Note:
Внутри пакета разделяйте код логически
Если пакет имеет дело с разными типами, держите логику для каждого типа в отдельном файле.

---

#### Naming Conventions - Source Code

In the package that defines your domain objects, define the types and interfaces for each object in the same source file:

```
	package: inventory

	orders.go 
	-- contains Orders type 
	   and OrderStorage interface
```

Note:
В пакете, который описывает домены, объявляйте сам тип и интерфейсы для каждого объекта в одном и том же файле

---

#### Smaller Tips

- Make comments in full sentences, always.
```go
	// An Order represents an order from a customer.
	type Order struct {}
```

Note:
Пишите комментарии полными предложениями, всегда

---

#### Smaller Tips

Use `goimports` to manage your imports, and they'll always be in canonical order. Standard lib first, external next. 

Note:
Используйте goimports для упорядочивания импортов.
Что она делает - сортирует, стандартные либы сначала, внешние после в алфавитном порядке

---

#### Smaller Tips

Avoid the `else` clause.  Especially in error handling.  
```go
	if err != nil {
		// error handling
		return // or continue, etc.
	} 
	// normal code
```

Note:
Не используйте else там где оно не требуется, особенно в отлове ошибок

---

## Coding Like the Go Team

Following these conventions will make your source code easier to read, easier to maintain, and easier for someone else to understand.

Note:
Следуя этим соглашениям вы сделаете исходный код более читабельным, поддерживаемым

---?image=assets/image/gitpitch-audience.jpg&opacity=100

@title[Thank You!]

## Thank You