---
title: "Три символа у рядку, а довжина рядка - 4. Дивина, JavaScript, та й годі" 
description: "чому властивість `length` текстового рядка (String об'єкту) може відображати невірну довжину"
tags: 
  - javascript
  - object
  - length
date: 2022-01-22T20:06:36+01:00 
draft: false
--- 

По визначенню, JavaScript об'єкт _String_ -  текстовий рядок - (і в принципі це відноситься й до primitive) - набір букв/симовлів, які вираженні (пердставленні) за допомогою [UTF-16](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type). Кожен елемент в рядку займає свою позицію, місце, ідекс, який починається з 0. Довжина текстового рядка - це кількість букв в ньому. Напр, текст.рядок "ПТН ПНХ" містить 7 символів, його довжина (length) = 7, починаючи з індексу 0 (П) й закінчуючи індексом 6 (Х).

В ютф-16 символи (грубо кажучи букви, знаки, символи) [кодуються за допомогою _одного_ чи **двох** 16-бітних кодів (одиниць)](https://en.wikipedia.org/wiki/UTF-16).  В текстовому об'єкті джаваскріпту є властивість [_length_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/length), яка містить довжину тексту (текстового об'єкту). Здавалось б, що усе зрозуміло й доступно. Втім, ця властивість відображає довжину (кількість) текстового об'
єкту, вимірюючи кількість UTF 16-бітних кодів. Якщо буква, символ, знак формується (кодується) за допомогою **двох**, а не _одного_, 16-бітних кодів, тоді довжина тексту, вказана у властивості _length_, не відповідатиме реальній кількості символів.

Наприклад, візьмемо ось цей ієрогліф (показано як картинку)

{{< figure src="cjk_compatibility_ideograph2f804.png" >}}

Джаваскріпт закодує його двома 16-бітними кодами _'\uD87E\uDC04'_. Тоді 

```javascript
"A\uD87E\uDC04B".length == 4
```
Це тому, що length 'порахує' кожну кодову одиницю \uD87E і \uDC04 окремо, а також порахує букви A і B, які закодовані по одному 16-бітному коду, тобто в цій "довжині" 4 окремих 16-бітних коди, але це лише 3 символи (букви).

В документації [MDN наводиться такий приклад функції як це можна обійти](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/length#unicode)
```javascript
function getCharacterLength (str) {
  // The string iterator that is used here iterates over characters,
  //  not mere code units
  return [...str].length;
}

console.log(getCharacterLength('A\uD87E\uDC04Z')); // 3

// While not recommended, you could add this to each string as follows:

Object.defineProperty(String.prototype, 'charLength', {
  get () {
    return getCharacterLength(this);
  }
});

console.log('A\uD87E\uDC04Z'.charLength); // 3
```
