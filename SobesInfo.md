# `JS`
- [`JS`](#js)
  - [`Прототипное наследование`](#прототипное-наследование)
  - [`Оператор new и функции`](#оператор-new-и-функции)
  - [`Типы данных`](#типы-данных)
    - [`Примитивные значения`](#примитивные-значения)
    - [`Ссылочные данные`](#ссылочные-данные)
    - [`Объекты как аргументы функций`](#объекты-как-аргументы-функций)
  - [`var, let, const`](#var-let-const)
  - [`Контекст this`](#контекст-this)
  - [`Операторы for..in и for..of`](#операторы-forin-и-forof)
  - [`Функции-объекты`](#функции-объекты)
  - [`Создать объект`](#создать-объект)
  - [`EventPropagation`](#eventpropagation)
  - [`DOM`](#dom)
  - [`Замыкание`](#замыкание)
  - [`Каррирование`](#каррирование)
  - [`bind, call, apply`](#bind-call-apply)
  - [`Event Loop`](#event-loop)
    - [`Макро и микро таски`](#макро-и-микро-таски)
  - [`Promise`](#promise)
    - [`Promise.all, Promise.rase`](#promiseall-promiserase)
    - [`Callback`](#callback)
  - [`Fetch`](#fetch)
  - [`Мелочи`](#мелочи)
  - [`localStorage и sessionStorage`](#localstorage-и-sessionstorage)
- [`CSS`](#css)
  - [`Каскадная таблица стилей`](#каскадная-таблица-стилей)
  - [`Селекторы`](#селекторы)
  - [`Flex`](#flex)
  - [`Box-sizing`](#box-sizing)
- [`React`](#react)
  - [`Life-cicle`](#life-cicle)
    - [`Mounting`](#mounting)
    - [`Updation`](#updation)
  - [`Redux`](#redux)
  - [`Hooks`](#hooks)
  - [Глоссарий:](#глоссарий)

---
<details><summary>

## `Прототипное наследование`
</summary>

JS основан на парадигме прототипного наследования. Разберём подробнее:

JS это объектно-ориентированный ЯП. 

Т.е. все сущности могут быть лишь двух типов:
- объекты (неупорядоченная коллекция из ключа и значения).
- примитивы (тоже обладают соответствующими им объектами-обёртками, которые предоставляют определенные методы)

У каждого объекта есть объект-прототип.

Объект-прототип выступает в качестве шаблона и от которого объект наследует методы и свойства.

Связь устанавливается посредством свойства `proto`

`proto` есть абсолютно у каждого объекта.

Таким образом `proto` любого объекта указывает на прототип того класса или той функции, с помощью которого он создан.

Доступ к `proto` рекомендуется посредством 
- геттера Object.getPrototypeOf
- сеттера Object.setPrototypeOf.

Если у объекта нет необходимого поля или метода, тогда интерпретатор ищет его у объекта, на который указывает `proto`.

Если поле/метод найдены, тогда они называются унаследованными:

```js
const animal = {eat: true};
const rabbit = {jump: true};

rabbit._proto_ = animal; // rabbit.jump, rabbit.eat
```

В конечном счёте `proto` ссылается на прототип соответствующего ему класса типа данных:

```js
(0).__proto__ === Number.prototype;
false.__proto__ === Boolean.prototype;
"string".__proto__ === String.prototype;
(new Date).__proto__ === Date.prototype;
(function(){}/* new Function */).__proto__ === Function.prototype;
```

В самом верху цепочки прототипов находится Object.

Все типы данных наследуются от объекта Object.

```js
Number.prototype.__proto__ === Object.prototype
```    

От объекта Object наследуются такие методы, как, например, toString()

И завершением цепочки является null:

```js
Object.prototype.__proto__ // null
```    

Поля и методы можно переопределять:

```js
const animal = {
  walk() {console.log(`ANIMAL WALK`)}
};

const rabbit = {
  __proto__: animal,
};

rabbit.walk = function() {
  console.log(`RABBIT WALK`);
}

rabbit.walk();
```

</details>

---
<details><summary>

## `Оператор new и функции`
</summary>

У каждого класса и функции (но не у стрелочной) есть свой `prototype`.

У функции prototype играет особенную роль: `prototype` - это объект, у которого есть единственное свойство `constructor`, ссылающийся на саму функцию.

В сочетании с оператором new конструктор позволяет:

- создать новый объект
- создать у него свойство `__proto__`
- передать в `__proto__` ссылку на functionName.prototype
- вернуть экземпляр объекта и передать ему значение `this`

Если нужно добавить, к примеру, функционал, тогда создаём функцию, а допфункционал прикрепляем к прототипу:

```js
function Human(name){
  this.name = name;
}

Human.prototype.hello = function() {
  alert(this.name)
}

const user = new Human(`John`);
user.hello()

// Или:

const human = new Object({
  name: `Vasya`,
})

Object.prototype.greet = function() {
  console.log(`Hello, ${this.name}`);
}

human.greet()
```

Отличным примером будет добавление к стандартному массиву нового функционала. Вот как выглядит обычный пример:

```js
const arr = [1, 2, 4, 5, 6];

function mult(ar, m){
  return ar.map((i) => i * m)
};

console.log(mult(arr, 2))

// А так можно эффектно сделать, используя прототипы:

Array.prototype.mult = function(m) {
  return this.map((i) => i * m)
};

console.log(arr.mult(3))
```
</details>

---
<details><summary>

## `Типы данных` 
</summary>

Типы данных делятся на: `примитивные` и `ссылочные`.

### `Примитивные значения`

Перечислим семь примитивных типов данных:

`string`, `number`, `boolean`, `symbol`, `null`, `undefined`, `bigint` 

и один ссылочный (`object`)

Важно: JS это язык со слабой и динамической типизацией. Т.е. к переменной можно последовательно применять различные типы данных.

`Simbol` - создает уникальное неизменяемое значение, которое используется в качестве идентификатора - ключа в свойствах объектов

`Number` - это числа целые и с плавающей запятой

Существуют специальные числовые значения: `infinity`(бесконечность. К примеру, 1/0) и `NaN` (вычислительная ошибка. "string"/2)

Ссылочный тип данных - это значение в памяти, на которое можно ссылаться с помощью идентификаторов

Примитивные типы данных сравниваются операторами:

- нестрогое (==). Данный оператор пытается привести операнды к одному типу. После чего их сравнивает
- строгое (===) - без каких либо преобразований

Определить тип данных можно посредством операции `typeof`

В двух случаях `typeof` работает не совсем корректно: 
- `typeof null` определяется как `Object` 
- `typeof function` как `function`

Важно: примитивы обладают соответствующими им объектами-обёртками: 
String, Number, Boolean, Symbol и BigInt

В свою очередь обёртки предоставляют соответствующие им методы.

### `Ссылочные данные`

Различие между примитивными типами и ссылочными состоит в способе их хранения в памяти. 

Примитивные значения хранятся в переменной как есть. Т.е. представляют из себя набор байтов в оперативной памяти.

К ссылочным типа относятся объекты, массивы и функции.

Обращение к таким данным происходит по ссылке на участок памяти, в котором они хранятся.

Из этого вытекает следствие: 
примитивные типы сравниваются по значениям (т.е. побайтово), а ссылочные - по участкам памяти.

В этой связи появляется понятие `мутации`. 
Когда несколько переменых владеет одной ссылкой (), изменение по одной из них, неизбежно приводит к изменениям в остальных.

Набор методов, которые позволяют копировать объект: 
`Object.assign`, `spread-оператор`, встроенный метод `slice`, `structuredClone` и `сериализация методами JSON`

```js
const a = {first: [0,1,2]};
const b = Object.assign({}, a);
const c = {...a};
const d = a.slice(0);
const e = structuredClone(a);
const f = JSON.parse(JSON.stringify(a));
```

Однако между ними есть существенные различия:

- Object.assign, slice и spread-оператор осуществляют лишь поверхностное копирование (shallow). 
Т.е. на один лишь уровень вглубь.
- Для глубокого копирования (deep) используется метод structuredClone.
- Сериализация методами JSON не распознаёт если значения данных представлены как undefined, 
function или Symbol:

```js
const a = [undefined, function() {console.log()}, Symbol("foo")];
JSON.parse(JSON.stringify(a)); // [undefined, undefined, undefined]
```

### `Объекты как аргументы функций`
</summary>

При передаче данных в качестве аргументов, все особенности его типа данных сохраняются. 

```js
const a = { first: 'first' }; // работаем с объектом

function test(user) {
  user.second = 'second'; // добавляем новое поле
  return user;
}

const b = test(a); // b === a
```
В примере с массивами можно воспользоваться встроенными 
методами `sort`, `reverse`, `splice` которые мутируют массивы, на которых применяются:

```js
const a = Array.from({length: 10}, () => Math.from(Math.random() * 100) + 1);
const test = (arr) => arr.sort(); 
const b = test(a); // b === a. И, кроме того, массивы оказались отсортированы
```

Ранее, чтобы избежать мутации исходного массива, использовались методы описанные выше 
(`Object.assign`, `spread-оператор`, встроенный метод `slice`, `structuredClone` и `сериализация методами JSON`). 
Например:

```js
const b = [...a].sort((a, b) => a - b);
```

В новейших спецификациях JS появились методы, которые НЕ мутируют исходник:

```js
const a = Array.from({length: 10}, () => Math.floor(Math.random() * 100) + 1);
const b = a.toSorted();
const c = a.toReversed();
const d = a.toSpliced(0, 3, 1, 2, 3);
```

Но нужно помнить, что все вышеперечисленные методы возвращают поверхностные копии (shallow copy);

</details>

---
<details>
<summary>

## `var, let, const`
</summary>

1. Область видимости `var` ограничена функцией. Если обратиться к `var` до её объявления результатом будет `undefined`

2. Область видимости `let`, const ограничены блоком. Если обратиться к `let`, `const` до их объявления результатом будет `referenceError`

3. `let` можно менять, `const` - нельзя

</details>

---
<details>
<summary>

## `Контекст this`
</summary>

Функция всегда выполняется в контексте какого-либо объекта.

Ключевое слово `this` указывает на объект, в контексте которого он был вызван.

Если меняется контекст, то и меняется значение this. 

Это означает, что `this` в процессе выполнения программы может изменяться.

Итого:

- `this` не имеет значения, пока функция не будет вызвана
- `this` вычисляется в момент вызова метода и указывает на тот объект, который вызывает метод
- `this` меняется в зависимости от контекста

Т.е. важен момент вызова!

Неважно, где находится метод - в прототипе или самом объекте - this всегда будет указывать на тот объект, который перед точкой:

```js
const human1 = {
  name: `Vasya`,
  greet() {console.log(`Hello ${this.name}`)}
};

const human2 = {
  name: `Misha`,
  greet() {console.log(`Hello ${this.name}`)}
};

human1.greet()

human2.__proto__ = human1;

human2.greet() // Misha
```

В JS существует два режима: строгий и нестрогий.

В самом простом случае, если функция вызывается без объекта, то при строгом режиме `this` равен `undefined`, в нестрогом - на `объект window`.

```js
function hello() {
  console.log(`hello`, this)
}

const john = {
  name: `john`,
  hi: hello
};

hello() // this указывает на объект window
john.hi() // this указывает на объект hello
```

`this` может указывать не на то, что ожидается.

Например, если функция вызывается в контексте другого объекта:

```js
const coffee = {
  amount: 120,
  drink(){
    setTimeout(() => {
      if(this.amount) {
        this.amount -= 10;
        console.log(this.amount);
      };
    }, 5)
  }
}

coffee.drink() // 110
```

Пример выше неверно отработал потому, что setTimeout определен в объекте window.

Стрелочные функции не имеют собственного контекста.

Они копируют его из внешнего окружения.

Это означает, что this стрелочной функции указывает на лексическое окружение.

В следующем примере `this` у hiArrow ссылается не на объект john, а на внешнюю область видимости (на window):

```js
const john = {
  name: `John`,
  hi() {
    return this.name;
    },
  hiArrow: () => this.name,
};

john.hi() // 'John'
john.hi2() // ''
```

Интересной особенностью `this` является возможность составлять цепочки вызовов. Например:

```js
const john = {
  count: 0,
  up() {
    count++
  },
  down() {
    count--
  },
  print() {console.log(this.count)}
};

john.up()
john.up()
john.up()
john.print() // 2
```

Но теперь попробуем вернуть контекст:

```js
const john = {
  count: 0,
  up() {
    this.count++;
    return this
  },
  down() {
    this.count--;
    return this;
    },
  print() {console.log(this.count)}
};

john.up().up().up().down().print() // 2
```

</details>

---
<details>
<summary>

## `Операторы for..in и for..of`
</summary>

С помощью цикла `for..in` можно перебрать как собственные поля/методы, так и унаследованные.

Чтобы отсечь унаследованные, следует использовать `.hasOwnProperty(свойство)`

```js
for (let property in someObject) {
    if (someObject.hasOwnProperty(property) {
        console.log(property)
    }
}
```

Важным моментом является то, что обход в for..in происходит в произвольном порядке. 
Чаще всего - в порядке создания.

Оператор `for..of` применяется для итерируемых объектов: string, array, Set, Map. 
Производит итерацию по всем собственным значениям сущности:

```js
const hello = "Hello World";

for (const i of hello) {
    console.log(i)  // H e l l o " " W o r l d
}
```
</details>

---
<details>
<summary>

## `Функции-объекты`
</summary>

Блок кода - это то, что заключено между фигурными скобками {}:

```js
{ result = "first"; }
```

Ему можно дать имя и тогда это станет объектом:

```js
const a = { result: "first"; }
```

Объект это неупорядоченная коллекция пар ключ - значение

Круглые скобки () называются оператором группировки. 
Контролируют порядок выполнения в выражениях. 
Если добавить его к объекту, то получится функция:

```js
const a = () => { result = "first"; }

a();
```

Видно, что функция - это блок кода с прикрепленным к нему механизмом выполнения. 

Фактически же, функция - это объект (специальный) в JS.

Раз это объект, то функции можно добавлять свойства:

```js
const a = () => { console.log("first"); }
a.msg = "second";
a(); // first
console.log(a.msg) // second
```

Поскольку функция это объект, то переменная, указывающая на неё имеет ссылочный тип. 

А это значит, что функция может передаваться в другую функцию как и все другие объекты.

```js
const a = (fn) => fn();
const b = () => console.log("hello");
a(b); // hello
```

Т.о функции являются объектами первого класса. Это значит:

- функции можно сохранять в переменные, передавать как аргументы и возвращать из других функций
- функция, которая работает с другими функциями, принимая их в качестве аргумента или возвращая их

Характерный пример - метод map:

```js
const arr1 = [1, 2, 3];
const arr2 = arr1.map(item => item * 2);
```
    
Выделяется особый вид функции - чистые:

- при передаче одних и тех же аргументов возвращает один и тот же результат
- не должна иметь побочных эффектов

Два синтаксиса создания функций:

- `Function Declaration` (создаётся ДО выполнения кода)
- `Function Expression` (создаётся, когда поток доходит до объявления)

</details>

---
<details>
<summary>

## `Создать объект`
</summary>

Обычно объекты создаются с помощью литерала объекта

Под капотом объекты создаются посредством new Object()

Помимо этого объект можно создать посредством create:

```js
const human1 = {
  name: `Vasya`,
  greet() {console.log(`Hello ${this.name}`)}
};

const human2 = Object.create(human1);

human2.greet()
```

Создать объект без прототипа:

```js
const o = Object.create(null);
console.log(o.toString); // undefined
```

</details>

---
<details>
<summary>


## `EventPropagation`
</summary>

Это процесс распространение события

Когда происходит событие, оно начинает распространяться.

Существует два варианта распространения:

- Всплытие события. Это поведение по умолчанию. Начинается в самом элементе (`event.target`), поднимается до его родителя и так до `объекта window`
- Погружение события. Когда оно начинается в объекте window и опускается через всех предков объекта события

Направлением процесса можно управлять: если передать прослушивателю событий (конкретнее - `addEventTarget`) третьим параметром true.

```js
addEventListener(event, callback, isCapturing = true)
```

`event.currentTarget` - элемент, к которому прикреплён прослушиватель

Отключить поведение элемента по умолчанию можно посредством `event.preventDefault`. 
Для формы, например, это отменит отправку посредством `submit`

</details>

---
<details>
<summary>


## `DOM`
</summary>

`DOM` - это API (прикладной программный интерфейс) который позволяет представить структурный HTML документ в виде объектов

Когда браузер парсит HTML документ, он формирует `DOM`: структуру - древовидный объект с узлами.

JS может динамически менять `DOM`

`DOM` может быть очень громоздким. Поэтому существующие библиотеки (REACT) используют `Virtual DOM`.

`Virtual DOM` это легковесная копия DOM в которую вносятся изменения по мере необходимости

Затем `VDOM` и `DOM` сравниваются и изменятся только то, в чем была разница

</details>

---
<details>
<summary>


## `Замыкание`
</summary>

Замыкание - способность функции, во время её создания, запоминать ссылки на переменные.

Как это работает: 

JS интерпретируемый язык программирования.

Т.е. для того, чтобы запустить программу на выполнение, должно произойти две фазы:

- компиляция. Функциональные выражения и переменные `var` поднимаются вверх глобальной области видимости
- исполнение

`Область видимость` - это область доступности переменной или функции.

Существует три области видимости:

- глобальная. Переменные доступны из любого места в коде
- функциональная
- блочная

Функция сама создает область видимости и сама вызывается в какой-то области видимости.

При вызове функции у неё создается:

- `lexicalEnvironment` - это объект, в котором все переменные из данной области видимости, хранятся как свойства. 
(Т.е. в нём хранятся все локальные переменные)
- `scope` - это ссылка на внешнюю область видимости

Существует набор правил по которым идет поиск переменной:

- Сначала интерпретатор ищет переменную в текущем LE. Если находит, то по завершению выполнения функции LE уничтожается
- Если интерпретатор не находит переменную в текущем LE, он ищет её в scope. Т.е. поиск происходит во внешней области видимости
- Если переменная найдена во внешнем LE, то ссылка сохраняется. А если существует хоть одна ссылка, LE продолжает существовать. Возникает, т.н. closure (замыкание)

Классический пример:

```js
const makeCount = () => {
  let count = 0;
  return () => count++
}

const a = makeCount();
console.log(a());
console.log(a());

// Аналогично:

const run = (() => {
  let count = 0;
  return () => count++;
})()

console.log(run());
console.log(run());
```

Пример с двумя замыканиями:

```js
const fn = (count) => () => count++;

const a = fn(0);
const b = fn(10);

console.log(a(2));
console.log(b(2));
```

Или чуть более продвинутый вариант. 
В нем создается два замыкания. 
Они ссылаются на одно и то же определение, но, при этом, имеют разное окружение:

```js
const makeFunc = (x) => (y) => (x + y);

const a = makeFunc(5);
const b = makeFunc(15);

console.log(a(2));
console.log(b(2));
```

Важно: свойство `scope` устанавливается по тому месту, где функция была объявлена, а не вызвана:

```js
const phrase = `Hello`;

function say() {
  console.log(phrase);
}

function start() {
  const phrase = `Bye`;
  say()
}

start()
```

</details>

---
<details>
<summary>

## `Каррирование`
</summary>

Это трансформация функции с несколькими аргументами и последовательность вложенных функций:

```js
const sum = (a, b) => (a + b);

const wrap = (f) => (a, b) => console.log(`Результат: `, f(a, b));

const logged = wrap(sum)

x(3, 4) // 'Результат:  7'
```

</details>

---
<details>
<summary>

## `bind, call, apply`
</summary>

Методы позволяют вызвать функцию так, будто она является методом некоторого объекта.

`bind()` - позволяет явно определять, какой именно объект будет привязан к `this` в момент вызова функции.

Важно: в момент вызова функции!

В самом простом примере (сделать для браузера):

```js
function hello() {
  console.log(`hello`, this)
}

const john = {
  name: `john`,
  hi: hello,
  hiWindow: hello.bind(window)
};

john.hi() // объект john
john.hiWindow() // объект window
```

Важно: метод `bind` не вызывает привязанную функцию, а создает новую функцию, которую можно вызвать либо сразу, либо позже:

```js
const john = {
  name: `john`,
  hi() {
    console.log(this.name)
  }
};

john.hi()

const anna = {
  name: `anna`
};

john.hi.bind(anna)() // сразу

// аналогичный вариант:
const annaHi = john.hi.bind(anna);
annaHi() // вызываем позже
```

В bind можно передавать дополнительные параметры. Доработаем пример выше:

```js
const john = {
  name: `john`,
  hi(age) {
    console.log(this.name, age)
  }
};

john.hi(50)

const anna = {
  name: `anna`
};

john.hi.bind(anna, 44)()

// аналогичный вариант:
const annaHi = john.hi.bind(anna, 23);
annaHi()
```

Потеря контекста. Например, если сделать следующий вызов:

```js
const user = {
  name: `Vasya`,
  f() {console.log(this.name)}
};

setTimeout(user.f, 1000)
```

То получим ошибку, т.к. в реальности:

- т.к. setTimeout получает функцию f отдельно от объекта user, контекст теряется. Все это эквивалентно: const fun = user.f; setTimeout(user.f, 1000)
- setTimeout - это API, this которого равен window (либо undefined, если это строгий режим), а у него нет метода f

Проблему можно решить посредством вызова анонимной функции. Она будет получать user.f из замыкания:

```js
setTimeout(() => user.f, 1000)
```
Но в интервале 1000мс может произойти событие, которое поменяет, например, значение user.name.

Поэтому есть другое решение - посредством bind:

```js
const user = {
  name: `Vasya`,
  f() {console.log(this.name)}
};

const printF = user.f.bind(user);
setTimeout(printF, 1000)
```
Методы `call` и `apply` аналогичны `bind`, но имеют отличие:

- они вызываются немедленно
- в `call` параметры передаются через запятую
- в `apply` параметры передаются массивом

Например:

```js
const john = {
  name: `john`,
  hi(age, lastName) {
    console.log(this.name, age, lastName)
  }
};

john.hi(50, `Annapurna`)

const anna = {
  name: `anna`
};

john.hi.call(anna, 44, `Annapurna`)
john.hi.apply(anna, [44, `Annapurna`])
```

</details>

---
<details>
<summary>

## `Event Loop`
</summary>

JS однопоточный. 
То есть код выполняется синхронно и последовательно - строка за строкой. 
Пока не выполнится первый блок кода, выполнение следующего будет заблокировано.

Часто нужно выполнять асинхронные операции 
(пользовательские действия, таймер, запросы/ответы к серверу, загрузка файлов).

Для этого интерпретатор создаёт:

- `стек` - синхронную "очередь" выполнения. 
Стек работает по принципу: последним зашел, первым вышел. 
В этом плане он напоминает стопку книг: та книга, которую положили последней, будет наверху стопки. 
Со стеком такая же ситуация: 
если существует цепочка вызовов (например, функция A вызывает функцию B, которая вызывает функцию С), 
он их сохраняет и последовательно выполняет начиная с последней записанной (С, потом B, потом - А), удаляя их из стека.
- в браузере создается `WebAPI`, которое предоставляет данные из браузера и окружающей компьютерной среды.

Данное WebAPI не является частью языка JS. 
Это сторонний сервис, который предоставляется браузерной средой (движком браузера). 
Состоит из:
- `callback queue`
- `event loop` - цикла событий. 
Все пользовательские действия (например, таймер window.setTimeout или запросы/ответы к серверу) попадают в `event loop`.

EventLoop, в свою очередь, состоит из task queue и microtask queue. 

Обратим внимание на то, что `очередь` работает по тому принципу, что первый попавший в неё, покидает её первым.

Разберём работу setTimeout:

```js
console.log(`FIRST`); // Вызовется 1
setTimeout(() => { console.log(`SECOND`); }, 2000) // Вызовется 3
console.log(`THIRD`); // Вызовется 2
```
Но, почему программа не блокируется и в нужный момент вызывается? Механизм концепта:

- все функции попадают в стек
- в стеке регистрируется и функция, определенная в setTimeout
- setTimeout попадает в WebAPI и, одновременно, выводит определенную в ней функцию из синхронного потока
- выполняется стек
- параллельно сторонний API выполняет setTimeout
- когда выполнится setTimeout, определенная в ней функция попадает в специальную очередь (`callback queue`)
- в очереди работает цикл(`event loop`). Цикл пробегает по очереди и фиксирует, выполнился ли сторонний API
- Если сторонний API выполнился (setTimeout), значит и функция готова и она отправляется обратно в стек на выполнение
- Важно: функция из `callback queue` попадет в стек только после того, когда закончатся все элементы в стеке

Именно для setTimeout нужно помнить, что даже если время исполнения установить в 0 секунд, 
он отработает не моментально, а попадёт в tasks queue. И отработает через один цикл событий.

### `Макро и микро таски`

В процессе выполнения асинхронного кода появляется два цикла задач: `tasks` (макро) и `jobs` (микро).

`tasks` - setTimeout, browser events. Выполняется одна таска за цикл

`jobs` - это `promises`. После каждого `task` движок выполняет все задачи из очереди `jobs`. 
При этом, если `job` был добавлен в этом же цикле, то и выполнен будет в этом же цикле. 
В отличие от `task`. 

Это позволяет выполнить промис-цепочку синхронно.

Только после завершения `jobs` начинается отрисовка

</details>

---
<details>
<summary>

## `Promise`
</summary>

Это объект внутри которого выполняется асинхронный код.

Promise позволяет систематизировать выполнение асинхронного кода.

`Promise` дает возможность дождаться завершения асинхронной операции и после - 
вернуться к обычному ходу программы.

`Promise` — особый объект, хранящий, помимо значения, информацию о своём состоянии: «ожидание» (pending), «выполнен» (fulfilled) или «отклонён» (rejected).

Внутри `promise` есть исполняемый код. В него передается два коллбэка: `resolve` и `reject`

В самом `promise` есть два свойства: `state` и `result`

Их изначальное состояние:
- `state = 'fullfilled`' - promise решён
- `result = undefined`

В состоянии ожидания (pending):
- `state = 'pending'`
- `result = undefined`

Если вызывается коллбэк resolve, тогда promise считается решенным (resolved):
- `state = 'fullfilled'`
- `result = value`

Если вызывается коллбэк reject, тогда promise считается отклоненным (rejected):
- `state = 'reject'`
- `result = error`

Таким образом, результатом вызова promise может быть либо значение, либо ошибка

Ошибку рекомендуется вызывать как `new Error` для того, чтобы ее можно было перехватить в обработчике try-catch

Существуют функции потребители: `.then`, `.catch`, `.finelly`

`then` имеет два аргумента. 
В первый попадает значение, когда promise получил состояние `fullfilled`, во второй - `state = 'reject'`

### `Promise.all, Promise.rase`

Promise.all() заставляет дождаться выполнения всех перечисленных промисов:

```js
const r1 = fetch('api/a');
const r2 = fetch('api/b');
const r3 = fetch('api/c');
Promise.all([r1, r2, r3]).then((values) => console.log(values));
```

В данном случае в переменной values будет находиться массив с результатами каждого из промисов, 
в порядке их перечисления.

Promise.race() выполняет первый же завершившийся промис:

```js
const r1 = new Promise((resolve, reject) => setTimeout(resolve, 1, 'first'));
const r2 = new Promise((resolve, reject) => setTimeout(resolve, 0, 'second'));

Promise.race([r1, r2]).then((value) => console.log(value)); // 'second'
```

### `Callback`

Способ обработки асинхронных действий. 

Это функция, которая вызывается в ответ на какие-либо действия. 

Выглядит как: "отработай, когда случится это событие".

</details>

---
<details>
<summary>

## `Fetch`
</summary>

Обрабатывать сетевые запросы в JavaScript можно разными способами. 
Раньше все пользовались XMLHttpRequest, но этот интерфейс устарел, 
а на смену ему пришёл куда более простой и гибкий `Fetch API`.

Функция fetch() принимает два параметра:

- обязательный url — адрес для отправки запроса,
- необязательный options.

В самом просто варианте fetch:

```js
async function post() {
  const response = await fetch('api/posts');
  return await response.json();
}
```

Причём fetch() без options — это обыкновенный GET-запрос, который просто скачивает данные по-указанному URL.

А вот с помощью options его можно настроить: например, 
- выбрать HTTP-метод (method), 
- добавить заголовок (headers), 
- прикрепить данные для отправки (body) 
- или включить cookies (credentials).

Возвращает функция обещания (`promise`) — удобную 
альтернативу обратным вызовам (callbacks) при управлении асинхронными процессами.

Пока promise находится «в ожидании», он не имеет значения, 
но может в любой момент перейти в другое состояние и получить его. 
Как только сервер отвечает, promise выполняется или отклоняется и 
возвращает объект встроенного класса response с полученным значением или причиной отказа.

Объект response хранит в себе статус HTTP-запроса и, если он выполнился успешно, заголовок ответа. 
Тело ответа можно получить в разных форматах: 
от простого текста до бинарного массива — нужно только выбрать подходящий метод. 
Самые популярные варианты:
- response.text() для обычного текста,
- response.json() для формата JSON.

Важное замечание: когда сервер отвечает на fetch() сообщением об ошибке, 
promise не отклоняется автоматически: ведь сам запрос успешно произошёл. 
Следует помнить про такие случаи и при необходимости обрабатывать их вручную.

</details>

---
<details>
<summary>

## `Мелочи`
</summary>

Логическое И `&&` возвращает первое ложное значение или последний операнд, когда все значения истинные

Логическое ИЛИ `||` возвращает первое истинное значение или последний операнд, когда все значения ложные

Почему obj.someprop.x приводит к ошибке? Потому, что мы пытаемся получить доступ к свойству x свойства someprop, которое имеет значение undefined:

```js
const obj = {}
console.log(obj.someprop.x)
```

`arguments` - коллекция аргументов, переданных функции. Для стрелочной функции arguments можно заменить `spread`:

```js
function f1(a, b) {
  return arguments[1];
}

console.log(f1(12, 15)); // 15

const f2 = (...arg) => arg[1];

console.log(f2(12, 15)); // 15
```

Метод `Array.from` принимает объект, проверяет, является ли он итерируемым (т.е. объектом, который реализует Symbol.iterator) и делает из него настоящий Array

`Генераторы` это функции, которые приостанавливают свое выполнение

```js
const counter = function* () {
  let i = 0
  while (true) yield i++
}()

counter.next().value
```

</details>

---
<details>
<summary>

## `localStorage и sessionStorage`
</summary>

За каждым веб-источником (за каждым поддоменом) закрепляется веб-хранилище.

У веб-хранилища есть два объекта: `localStorage` и `sessionStorage`.

Эти объекты могут хранить информацию:
- sessionStorage хранит информацию после обновления страницы
- localStorage хранит информацию даже после перезагрузки браузера

Важно знать следующее: 
- объекты веб-хранилища не отправляются на сервер 
- сервер не может манипулировать веб-хранилищем
- каждый поддомен создает свое веб-хранилище и другие поддомены не имеют к ним доступа.

Указанные объекты хранятся в виде пары ключ - значение.~ 

Указанные объекты обладают одинаковыми методами и свойствами:
- setItem(key, value)
- getItem(key)
- removeItem(key)
- clear() - очистить все записи
- length

</details>

---
# `CSS`

---
<details>
<summary>

## `Каскадная таблица стилей`
</summary>

Каскадные таблицы стилей - это язык, позволяющий добавлять HTML элементам набор свойств для стилизации (размеры, цвета и т.д.)

Существует три основных правила приоритета стилей: правило источников, правило расположения, правило селекторов.

Правило источников:

- Атрибут тега. Приоритетный. Использовать не рекомендуется
- Стиль в отдельном источнике. Например, в отдельном файле. Меньший приоритет
- Стиль по умолчанию, который добавляет браузер

Правило расположения: если в одном из источников правило дублируется, тогда используется последний:

```css
.blue {color: blue;}
.blue {color: red;}
```

Правило селекторов. Каждому типу селекторов соответствует свой вес:

| Selector          | Weight |
|-------------------|--------|
| По тегу           | 1      |
| По классу         | 10     |
| По id             | 100    |
| В инлайн-атрибуте | 1000   |

</details>

---
<details>
<summary>

## `Селекторы`
</summary>

    * - любые элементы
    # - по id
    .class - по классу
    [name="value"] - по атрибуту

    div p - элементы p, являющиеся потомками div
    div > p - непосредственные потомки
    div ~ p - все p на том же уровне вложенности, которые идут после div
    div + p - p на том же уровне вложенности, которые идут после div

    :first-child - первый потомок своего родителя.
    :last-child – последний потомок своего родителя
    :nth-child(a) – потомок номер a своего родителя. Нумерация начинается с 1

    :first-of-type - во время подсчёта игнорируют элементы с другими тегами, чем тот, к которому применяется фильтр

</details>

---
<details>
<summary>

## `Flex`
</summary>

Способ отображения элементов

В основе лежит идея оси

Состоит из гибкого контейнера и гибких элементов

Позволяет решить следующие вопросы:

- `flex-direction` - располагать элементы по X и Y, и менять реверс
- `flex-wrap` - переносить элементы в рамках контейнера, не создавая его переполнение
- `order` - изменять порядок отображения
- `justify-content` - в рамках контейнера изменять центрирование и расположение по основной оси
- `align-items` - в рамках контейнера изменять центрирование и расположение по поперечной оси
- `align-self` - переопределяет положение конкретного элемента по поперечной оси

---
## `Box-sizing`

Это алгоритм расчета высоты и ширины элемента. Может быть:

- `content-box`: width и height включают в себя размеры контента (то, что находится внутри паддингов)
- `border-box`: width и height включают в себя размеры с границей

</details>

---
# `React` 

<details>
<summary>

## `Life-cicle`
</summary>

Для того чтобы можно было влиять на работу компонента, существует жизненный цикл

Он состоит из нескольких последовательных этапов, в которых происходят соответствующие процессы

### `Mounting`

- `constructor` - начальная инициализация компонента
- `render`
- `componentDidMount` - можно обращаться к удаленным ресурсам.

`constructor` срабатывает всякий раз когда создается новый объект. 
Он инициализирует стейт на основе новых данных или на основе пропсов.

`componentDidMount` происходит последним (в т.ч. после componentDidUpdate), после того, 
как и сам компонент и его дочерние отрисовались без ошибок.

`componentWillMount` соответствует конструктору и на данный момент deprecated

### `Updation`

- `getDerivedStateFromProps` - синхронизация стейта и пропсов
- `shoudComponentUpdate` - указывает должен ли обновиться компонент. Сравнивает между собой nextProps, nextState, nextContext
- `render` - отрисовка компонента
- `componentDidUpdate` - можно обращаться к удаленным ресурсам

В конечном счете происходит componentWillUnmount - вызывается перед удалением компонента из DOM. 
Используется для очистки сайд эффектов

(уточнить) `PureComponent` - компонент перерисовывается только в случае изменения свойства props

</details>

---
<details>
<summary>

## `Redux`
</summary>

Это инструмент для управления данными приложения

Базовые принципы:

`Хранилище данных` - единый источник информации. Доступ к информации - только для чтения
`actions` - набор действий как именно меняется хранилище
`dispatch` - коллбэк в который попадает action
`редуктор` - логика обработки данных и изменения хранилища. 

Изменение хранилища возможна только через редукторы (чистые функции). 
Результат работы редуктора - новый объект с актуальными данными

</details>

---
<details>
<summary>

## `Hooks`
</summary>

Это функции, которые позволяют управлять состоянием и жизненным циклом
#useState
Предназначен для управления состоянием компонента.</p>
Возвращает кортеж из геттера, которому можно передать инициализирующее состояние, и функции - сеттера</p>
#useEffect
Предназначен для запуска побочных эффектов (например, запросы к серверу или добавление обработчика событий)</p>
Запускается после монтирования и отрисовки компонента</p>
Принимает два аргумента: коллбэк и массив зависимостей</p>
Массив зависимостей может быть:</p>

<ul>
<li>массив не указан: хук будет выполняться при каждом рендере</li>
<li>массив пуст: хук будет выполняться только один раз</li>
<li>массив с элементами: хук выполняется при изменении любого из них</li>
</ul>
Отписаться от эффектов возможно вернув их из useEffect в return</p>
#useRef
Хук позволяет:</p>
<ul>
<li>Хранить объект, в котором можно мутировать значения без ререндера компонента</li>
<li>Работать c DOM элементами напрямую</li>
</ul>
Во первых, с помощью useRef можно извлекать и устанавливать значения, которые сохраняются на всем протяжении жизни компонента</p>
Во вторых, в react каждый DOM элемент имеет атрибут ref.</p>
Хук useRef возвращает объект, у которого есть свойство current.</p>
Если передать в него ссылку на ref, к DOM элементу можно получить прямой доступ посредством ref.current и манипулировать им:</p>
<pre>
const refInput = useRef(null);

        // const el = refInput.current

        &lt;input ref={refInput} />
      </pre>

#useContext
Позволяет передавать пропсы компонентам находящимся на любом уровне вложенности</p>
Это позволяет избежать последовательной передачи пропсов от родителя к глубоко вложенным потомкам</p>

</section>
</details>

---
<details>
<summary>


## Глоссарий:
</summary>



</details>