# JS
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
  Копbрование удобно если в объекте нет методов и undefined свойств:
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
