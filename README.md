# JS
## Start
  1. JS синхронный или асинхронный и многопоточный или однопоточный?
     - Синхронный и однопоточный
  2. За счет чего можно делать асинхронные операции?
     - За счет механизма `Event loop`
  3. Как обрабатывает задачи `Event loop`?
     - Сначала синхронный код, потом все микротаски из очереди, потом одну макротаску и т.д. по кругу.  
  4. Как будет выполняться код?
     ```javascript
     fetch('http://...')
       .then((resp) => console.log(resp))
       .catch((err) => console.log(err))

     while (true) console.log(1)
     ```
     - JS отправит fetch запрос, но никогда не выводет ни resopnse ни error в случае ошибки, т.к. будет занят выполнением синхронного бесконечного цикла while.
  5. Я хочу использовать `console.log()`, но не хочу каждый раз писать это полностью, как я могу сократить вызов до `log()` или `doLog`?
     - `const { log } = console;` или `const { log: doLog } = console`
  6. Порядок вывода в консоль
     ```javascript
     console.log(1)
     setTimeout(() => console.log(2))
     Promise.resolve().then(() => console.log(3))
     Promise.resolve().then(() => setTimeout(() => console.log(4)))
     Promise.resolve().then(() => console.log(5))
     setTimeout(() => console.log(6))
     console.log(7)
     new Promise(resolve => {
       console.log(8)
       setTimeout(() => console.log(9))
     }).then(() => console.log(10))
     ```
     - 1,7,8,3,5,2,6,9,4
  7. Что выведет цепочка вызова?
     ```javascript
     Promise
       .reject('a')
       .catch(i => i + 'b')
       .catch(i => i + 'c')
       .then(i => i + 'd')
       .finally(i => i + 'e')
       .then(i => console.log(i))
     ```
     - abd
  
     ```javascript
     Promise
       .reject('a')
       .catch(i => i + '1', i => i + '2')
       .catch(i => i + 'b')
       .catch(i => i + 'c')
       .then(i => i + 'd1')
       .then('d2')
       .then(i => i + 'd3')
       .finally(i => i + 'e')
       .then(i => console.log(i))
     ```
     - a1d1d3

  8. Что выведет в консоль?
     ```javascript
      console.log(typeof f1)
      console.log(typeof f2)
      console.log(typeof f3)
      
      function f1 () {}
      var f2 = function () {}
      let f3 = function () {}
     ```
     - function, undefined, error

 8. Объяснить что это в JS  
     ?.  
     ...  
     ??  
     ||=  
     &&=  
     [1, 2, 3].at(...)  
   
    .? - необязательная цепочка вызова - foo?.bar?.fn?.()  
    ... - спред или рест оператор, чтобы собрать множетсво в одно - [..arr1, ...arr2]  
    ?? - нулевой оператор использует правое значение, если левый null или undefined  
    ||= - привоение логического или, присваивает правое значение если левое отвечает логическому false  
     ```javascript
     const a = { duration: 50, title: '' };
     a.duration ||= 10;
     console.log(a.duration); // Expected output: 50
    
     a.title ||= 'title is empty.';
     console.log(a.title); // Expected output: "title is empty"
     ```
    &&= - привоение логического и, присваивает правое значение если левое отвечает логическому true  
     ```javascript
     let a = 1;
     let b = 0;
    
     a &&= 2;
     console.log(a); // Expected output: 2
    
     b &&= 2;
     console.log(b); // Expected output: 0
     ```
    .at(-1) - берет указанный индекс из массива, в случае -1, берет первый с конца и .т.д.

  9. Напишите, какое значение должно быть в theVal, чтобы получилось true true
      ```javascript
      const theVal = ...;
      
      console.log(`${theVal}` === 'Yo', theVal == 3); // true true
      ```
      Ответ:  
      ```javascript
      const theVal = {
        toString: () => 'Yo',
        valueOf: () => 3
      };
      
      console.log(`${theVal}` === 'Yo', theVal == 3); // true true
      ```

## 1. `prototype` и `__proto__`

  `__proto__` - это свойство объекта, которое само является объектом
  `__proto__` есть у каждого объекта и ссылается на `prototype` другого объекта, а любой объект создается с помощью класса
  Чтобы понимать чемк равен `__proto__` нужно точно знать с помощью какой функции-конструктора создан данный объект
  
  `prototype` есть только у class и function (за исключением стрелочной функции)
  
  Вопросы:
```javascript
  let obj1 = {};
  let obj2 = {};
  
  console.log(obj1 === obj2, obj1.__proto__ === obj2.__proto__) // false, true
```
  Потому что объекты являются уникальными, но т.к. их `__proto__` ссылается на prototype `Object.prototype`, то `__proto__` являются одинаковыми
  
  Есть ли `__proto__` у булева значения? - Да
  ```javascript
  console.log(true.__proto__)
  ```
  Есть ли `__proto__` у строки?
  Да, потому что при обращении к свойству `__proto__` строка временно оборачивается в `new String()`

  Почему тут ошибка, `console.log(18.__proto__)`
  а тут нет
  ```javascript
  let age = 18
  console.log(age.__proto__)
  ```
  Потому что в переменную кладется ссылка, а ссылки в js являются объектами и в этот объект кладется `new Number` у которого есть это свойство
  
 Как нам это может быть полезно в работе?
 
 К примеру, если мы попытаемся прочитать свойство объекта (вызвать метод), а этого свойства нет в объекте, то js полезет искать его через ссылку в `__proto__` в prototype
 Мы так же можем создать метод в прототипе функции и потом иметь этот метод (в единичном экземпляре в памяти) во всех новых конструкциях этой функции, например:
 ```javascript
 function Fn(name) { this.name = name }
 Fn.prototype.hello = function() { alert('Hello ' + this.name) }
 let say = new Fn('Jhon');
 say.hello();
 ```

Как для объекта `point` добавить ссылки на методы из `pointMethods`?
```javascript
const pointMethods = {
  move(x, y) {
    this.x += x;
    this.y += y;
  },

  toString() {
    return `[${this.x}, ${this.y}]`;
  },
};

const point = {
  x: 10,
  y: 20,
};

// Ответ
// point.__proto__ = pointMethods;
Object.setPrototypeOf(point, pointMethods);

point.move(2, 3);
console.log(point.toString());
```
## 2. Как скопировать объект? И какие виды копирования объекта бывают?

  Виды копирования: Поверхностное - копирование происходит вглубь на один уровень
  Методы: через спред (...) оператор или через `Object.assign()`
  Минусы - Будет действительно создана копия объекта, но если внутри объекта одно из свойств было само объектом, то создасться ссылка на это свойство и при изменении в начальном объекте свойства из вложенного объекта оно поменяется и в копии которую мы создали.             
  Например:
 ```javascript
  const sourceObject = {
    title: 'obj',
    permission: {
      one: true,
      two: false,
    }
  }

    
  const copyObj = {...sourceObject}
  copyObj.permission.one = false;
  
  console.log(sourceObject.permission.one, copyObj.permission.one) // false, false
```
    
  Глубокое копирование - `JSON.parse(JSON.stringify(obj))`
  Копирование удобно если в объекте нет методов и undefined свойств:
  ```javascript
  const sourceObject = {
    title: 'obj',
    birdhDate: new Date('2001-01-01'),
    permission: {
      one: true,
      two: false,
    },
    nullProp: null,
    undefinedProp: undefined,
    fn() {
      console.log('fn')
    },
  }
  
  const copyObj = JSON.parse(JSON.stringify(sourceObject))
  
  console.log(copyObj)
  /*
  {
    title: "obj"
    birdhDate: "2001-01-01T00:00:00.000Z" - перестал быть объектом Date
    permission: {
      one: true
      two: false
    }
    nullProp: null
    -- undefinedProp пропал
    -- fn пропал
   }    
  */
  ```
  Так же можно делать глубокое копирование через `structuredClone()` из ES2022:
  ```javascript
  const sourceObject = {
    title: 'obj',
    birdhDate: new Date('2001-01-01'),
    permission: {
      one: true,
      two: false,
    },
    nullProp: null,
    undefinedProp: undefined,
    // fn() {                 -- будет ошибка, функции нельзя
    //   console.log('fn')
    // },
  }
  
  const copyObj = structuredClone(sourceObject)

  console.log(copyObj)
```
# TS
## В чем основные отличия определения типов Interface и Type?
**Type** позволяет создавать типы под новым наименованием.
**Interface** может расширяться наследованием от других интерфейсов.
Позволяет описывать функциональность.
Интерфейсы с одинаковым именем сливаются в один.

## Можем ли мы слелать для Interface extends от Type?
- можем, но только если Type не Union

## Можно ли функцию описать интерфейсом?
- можно, например
  ```typescript
  interface IFn {
    (str: string): string;
  };
  ```
## Как описать функцию типом?
```typescript
  type IFn = (str: string) => string;
  ```
## Покажи как в TS описать функцию, которая принимает в качестве аргуметна другую функцию со своими агруметами
  ```typescript
  function fn(val: string, update: (arg0: () => void) => void): void {
  	update(() => {...});
  }
  ```

# `Vue`

## 1. `v-html` что делает и какие особенности у директивы?
  Обновляет свойство элемента innerHTML. Учтите, что содержимое будет вставляться как обычный HTML и не будет компилироваться или обрабатываться как шаблоны Vue.  
  Минусы:  
    - Динамическая отрисовка произвольного HTML-кода на сайте — опасная практика, которая приводит к XSS-уязвимостям (opens new window). Передавайте в v-html содержимое, которому можно доверять, и НИКОГДА не передавайте содержимое, которое предоставляется пользователем.  
    - Локальные стили (scoped) в однофайловых компонентах также не будут применяться к содержимому v-html, потому что этот HTML никак не обрабатывается компилятором шаблонов Vue. При необходимости стилизовать содержимое в v-html вместо локального CSS можно использовать CSS-модули (opens new window)или указать дополнительный глобальный элемент <style> с собственной стратегией модульности, такой как БЭМ.
## 2. Как нам скрыть нескомпилированные шаблоны до тех пор, пока не будет готов экземпляр компонента? (т.е. скрыть компонент пока он не завершит свою компиляцию)
  - через директиву `v-cloak`
```vue
<div v-cloak>
  {{ message }}
</div>
[v-cloak] {
  display: none;
}
```
## 3. Расскажите про использование `v-memo` вместе с циклом `v-for`, какую проблему это может решить?
`v-memo` нужна исключительно для микрооптимизации сценариев, где критически важна производительность, и должна использоваться крайне редко. Наиболее частый случай, где она может оказаться полезной — отрисовка больших списков с помощью `v-for` (когда length > 1000):
```vue
<div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
  <p>ID: {{ item.id }} - выбран: {{ item.id === selected ? 'Да' : 'Нет' }}</p>
  <p>...больше дочерних элементов</p>
</div>
```
При повторной отрисовке компонента, если `v-memo="[item.id === selected]` остались неизменными, то будут пропущены все обновления этого `<div>` и его дочерних элементов. Фактически, будет пропущено даже создание VNode виртуального DOM, поскольку можно переиспользовать мемоизировую копию поддерева.
