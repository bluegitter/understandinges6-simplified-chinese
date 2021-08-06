# 数组功能改进（Improved Array Capabilities）

The array is a foundational JavaScript object. But while other aspects of JavaScript have evolved over time, arrays remained the same until ECMAScript 5 introduced several methods to make them easier to use. ECMAScript 6 continues to improve arrays by adding a lot more functionality, like new creation methods, several useful convenience methods, and the ability to make typed arrays.

数组是一个基本的JavaScript对象。但是当JavaScript的其他方面随着时间的推移而发展，数组的特性一直没有太大改变，直到ECMAScript 5引入了几个让数组更容易使用的方法。ECMAScript 6通过添加更多的功能持续改进数组的特性，比如新的数组创建方法、一些有用的便利方法，以及创建类型化数组的能力。

<br />

### Summary
* [创建数组](#Creating-Arrays)
* [数组原型的新方法](#New-Methods-on-All-Arrays)
* [类型数组](#Typed-Arrays)
* [类型数组与普通数组的相似之处](#Similarities-Between-Typed-and-Regular-Arrays)
* [类型数组与普通数组的差异](#Differences-Between-Typed-and-Regular-Arrays)
* [总结](#Summary)

<br />

### <a id="Creating-Arrays"> 创建数组(Creating Arrays) </a>

Prior to ECMAScript 6, there were two primary ways to create arrays: the Array constructor and array literal syntax. Both approaches require listing array items individually and are otherwise fairly limited. Options for converting an array-like object (that is, an object with numeric indices and a length property) into an array were also limited and often required extra code. To make JavaScript arrays easier to create, ECMAScript 6 adds the Array.of() and Array.from() methods.

在ECMAScript 6之前，有两种主要的方法来创建数组: Array构造函数和数组字面语法。这两种方法都需要单独列出数组项，否则是相当受限的。将类数组对象(即具有数字索引和长度属性的对象)转换为数组的选项也受到限制，并且通常需要额外的代码。为了让JavaScript数组更容易创建，ECMAScript 6添加了Array.of()和Array.from()两个方法。

<br />

#### The Array.of() Method

One reason ECMAScript 6 adds new creation methods to JavaScript is to help developers avoid a quirk of creating arrays with the Array constructor. The new Array() constructor actually behaves differently based on the type and number of arguments passed to it. For example:

ECMAScript 6向JavaScript添加新的创建方法的一个原因是帮助开发人员避免使用Array构造函数创建数组。新的Array()构造函数实际上根据传递给它的参数的类型和数量而表现不同。例如:

```js
let items = new Array(2);
console.log(items.length);          // 2
console.log(items[0]);              // undefined
console.log(items[1]);              // undefined

items = new Array("2");
console.log(items.length);          // 1
console.log(items[0]);              // "2"

items = new Array(1, 2);
console.log(items.length);          // 2
console.log(items[0]);              // 1
console.log(items[1]);              // 2

items = new Array(3, "2");
console.log(items.length);          // 2
console.log(items[0]);              // 3
console.log(items[1]);              // "2"
```

When the Array constructor is passed a single numeric value, the length property of the array is set to that value. If a single non-numeric value is passed, then that value becomes the one and only item in the array. If multiple values are passed (numeric or not), then those values become items in the array. This behavior is both confusing and risky, as you may not always be aware of the type of data being passed.

当向Array构造函数传递单个数值时，数组的length属性被设置为该值。如果传入一个非数字的值，则该值将成为数组中的唯一项。如果传递了多个值(数字或非数字)，那么这些值将成为数组中的项。这种行为既令人困惑又有风险，因为您可能并不总是知道所传递的数据类型。

ECMAScript 6 introduces Array.of() to solve this problem. The Array.of() method works similarly to the Array constructor but has no special case regarding a single numeric value. The Array.of() method always creates an array containing its arguments regardless of the number of arguments or the argument types. Here are some examples that use the Array.of() method:

ECMAScript 6引入了Array.of()来解决这个问题。of()方法的工作方式类似于Array构造函数，但对于单个数值没有特殊情况。of()方法总是创建一个包含参数的数组，而不管参数的数量或参数类型。下面是一些使用Array.of()方法的例子:

```js
let items = Array.of(1, 2);
console.log(items.length);          // 2
console.log(items[0]);              // 1
console.log(items[1]);              // 2

items = Array.of(2);
console.log(items.length);          // 1
console.log(items[0]);              // 2

items = Array.of("2");
console.log(items.length);          // 1
console.log(items[0]);              // "2"
```

To create an array with the Array.of() method, just pass it the values you want in your array. The first example here creates an array containing two numbers, the second array contains one number, and the last array contains one string. This is similar to using an array literal, and you can use an array literal instead of Array.of() for native arrays most of the time. But if you ever need to pass the Array constructor into a function, then you might want to pass Array.of() instead to ensure consistent behavior. For example:

要使用array. of()方法创建数组，只需将数组中需要的值传递给它。这里的第一个例子创建了一个包含两个数字的数组，第二个数组包含一个数字，最后一个数组包含一个字符串。这类似于使用数组字面量，对于原生数组，大多数时候可以使用数组字面量而不是array .of()。但如果需要将Array构造函数传递给函数，则可能需要传递Array.of()以确保行为一致。例如:

```js
function createArray(arrayCreator, value) {
    return arrayCreator(value);
}

let items = createArray(Array.of, value);
```

In this code, the createArray() function accepts an array creator function and a value to insert into the array. You can pass Array.of() as the first argument to createArray() to create a new array. It would be dangerous to pass Array directly if you cannot guarantee that value won’t be a number.

在这段代码中，createArray()函数接受一个数组创建器函数和一个要插入数组的值。你可以将array. of()作为第一个参数传递给createArray()来创建一个新数组。如果不能保证该值不是数字，直接传递Array将是危险的。

<br />

> The Array.of() method does not use the Symbol.species property (discussed in Chapter 9) to determine the type of return value. Instead, it uses the current constructor (this inside the of() method) to determine the correct data type to return.
>
> Array.of()方法不使用Symbol。species属性(在第9章讨论)来确定返回值的类型。相反，它使用当前构造函数(位于of()方法内部)来确定要返回的正确数据类型。

<br />

#### The Array.from() Method

Converting non-array objects into actual arrays has always been cumbersome in JavaScript. For instance, if you have an arguments object (which is array-like) and want to use it like an array, then you’d need to convert it first. To convert an array-like object to an array in ECMAScript 5, you’d write a function like the one in this example:

在JavaScript中，将非数组对象转换为实际数组总是很麻烦。例如，如果你有一个arguments对象(类似数组)，并且想要像数组一样使用它，那么你需要首先对它进行转换。要在ECMAScript 5中将一个类数组对象转换为数组，你需要编写一个类似于下面这个例子的函数:

```js
function makeArray(arrayLike) {
    var result = [];

    for (var i = 0, len = arrayLike.length; i < len; i++) {
        result.push(arrayLike[i]);
    }

    return result;
}

function doSomething() {
    var args = makeArray(arguments);

    // use args
}
```

This approach manually creates a result array and copies each item from arguments into the new array. That works but takes a decent amount of code to perform a relatively simple operation. Eventually, developers discovered they could reduce the amount of code by calling the native slice() method for arrays on array-like objects, like this:

这种方法手动创建一个结果数组，并将参数中的每个项复制到新数组中。这是可行的，但需要相当数量的代码来执行一个相对简单的操作。最终，开发人员发现他们可以通过调用类数组对象上的数组的本机slice()方法来减少代码量，如下所示:

```js
function makeArray(arrayLike) {
    return Array.prototype.slice.call(arrayLike);
}

function doSomething() {
    var args = makeArray(arguments);

    // use args
}
```

This code is functionally equivalent to the previous example, and it works because it sets the this value for slice() to the array-like object. Since slice() needs only numeric indices and a length property to function correctly, any array-like object will work.Even though this technique requires less typing, calling Array.prototype.slice.call(arrayLike) doesn’t obviously translate to, “Convert arrayLike to an array.” Fortunately, ECMAScript 6 added the Array.from() method as an obvious, yet clean, way to convert objects into arrays.

这段代码在功能上与前面的示例相同，之所以有效，是因为它将slice()的这个值设置为数组类对象。因为slice()只需要数字索引和长度属性就可以正常工作，所以任何类似数组的对象都可以工作。尽管这种技术需要更少的类型，但调用array. prototype.slice.call(arrayLike)显然不会转换为“将arrayLike转换为数组”。幸运的是，ECMAScript 6添加了Array.from()方法，作为将对象转换为数组的一种明显而干净的方法。

Given either an iterable or an array-like object as the first argument, the Array.from() method returns an array. Here’s a simple example:

如果将可迭代对象或类数组对象作为第一个参数，array. from()方法将返回一个数组。下面是一个简单的例子:

```js
function doSomething() {
    var args = Array.from(arguments);

    // use args
}
```

The Array.from() call creates a new array based on the items in arguments. So args is an instance of Array that contains the same values in the same positions as arguments.

Array.from() 调用基于参数中的项创建一个新数组。因此args是Array的一个实例，它在与arguments相同的位置包含相同的值。

<br />

> The Array.from() method also uses this to determine the type of array to return.
>
> Array.from() 方法也使用它来确定要返回的数组类型。

<br />

##### 映射转换（Mapping Conversion）

If you want to take array conversion a step further, you can provide Array.from() with a mapping function as a second argument. That function operates on each value from the array-like object and converts it to some final form before storing the result at the appropriate index in the final array. For example:

如果您想进一步进行数组转换，您可以为 Array.from() 提供一个映射函数作为第二个参数。 该函数对来自类数组对象的每个值进行操作，并将其转换为某种最终形式，然后将结果存储在最终数组中的适当索引处。 例如：

```js
function translate() {
    return Array.from(arguments, (value) => value + 1);
}

let numbers = translate(1, 2, 3);

console.log(numbers);               // 2,3,4
```

Here, Array.from() is passed (value) => value + 1 as a mapping function, so it adds 1 to each item in the array before storing the item. If the mapping function is on an object, you can also optionally pass a third argument to Array.from() that represents the this value for the mapping function:

这里，Array.from() 作为映射函数传递了 (value) => value + 1，因此它在存储项目之前给数组中的每个项目加 1。 如果映射函数在一个对象上，您还可以选择将第三个参数传递给 Array.from() 表示映射函数的 this 值：

```js
let helper = {
    diff: 1,

    add(value) {
        return value + this.diff;
    }
};

function translate() {
    return Array.from(arguments, helper.add, helper);
}

let numbers = translate(1, 2, 3);

console.log(numbers);               // 2,3,4
```

This example passes helper.add() as the mapping function for the conversion. Since helper.add() uses the this.diff property, you need to provide the third argument to Array.from() specifying the value of this. Thanks to the third argument, Array.from() can easily convert data without calling bind() or specifying the this value in some other way.

本例传递helper.add()作为转换的映射函数。因为helper.add()使用了this。属性，您需要为Array.from()提供第三个参数来指定this的值。多亏了第三个参数，Array.from()可以轻松地转换数据，而无需调用bind()或以其他方式指定此值。

<br />

##### 使用迭代器（Use on Iterables）

The Array.from() method works on both array-like objects and iterables. That means the method can convert any object with a Symbol.iterator property into an array. For example:

Array.from() 方法适用于类似数组的对象和可迭代对象。 这意味着该方法可以将具有 Symbol.iterator 属性的任何对象转换为数组。 例如：

```js
let numbers = {
    *[Symbol.iterator]() {
        yield 1;
        yield 2;
        yield 3;
    }
};

let numbers2 = Array.from(numbers, (value) => value + 1);

console.log(numbers2);              // 2,3,4
```

Since the numbers object is an iterable, you can pass numbers directly to Array.from() to convert its values into an array. The mapping function adds one to each number so the resulting array contains 2, 3, and 4 instead of 1, 2, and 3.

由于 numbers 对象是一个可迭代对象，您可以直接将 numbers 传递给 Array.from() 以将其值转换为数组。 mapping函数为每个数字加1，因此结果数组包含 2、3 和 4，而不是 1、2 和 3。

<br />

> If an object is both array-like and iterable, then the iterator is used by Array.from() to determine the values to convert.
>
> 如果一个对象既是数组类对象又是可迭代对象，那么Array.from()将使用迭代器来确定要转换的值。

<br />

### <a id="New-Methods-on-All-Arrays"> New Methods on All Arrays </a>

Continuing the trend from ECMAScript 5, ECMAScript 6 adds several new methods to arrays. The find() and findIndex() methods are meant to aid developers using arrays with any values, while fill() and copyWithin() are inspired by use cases for typed arrays, a form of array introduced in ECMAScript 6 that uses only numbers.

延续ECMAScript 5的趋势，ECMAScript 6向数组添加了几个新方法。find()和findIndex()方法旨在帮助开发人员使用带有任意值的数组，而fill()和copyWithin()是受到类型化数组用例的启发，类型化数组是ECMAScript 6中引入的一种仅使用数字的数组形式。

<br />

#### The find() and findIndex() Methods

Prior to ECMAScript 5, searching through arrays was cumbersome because there were no built-in methods for doing so. ECMAScript 5 added the indexOf() and lastIndexOf() methods, finally allowing developers to search for specific values inside an array. These two methods were a big improvement, yet they were still fairly limited because you could only search for one value at a time. For example, if you wanted to find the first even number in a series of numbers, you’d need to write your own code to do so. ECMAScript 6 solved that problem by introducing the find() and findIndex() methods.

在 ECMAScript 5 之前，搜索数组很麻烦，因为没有内置的方法可以做这样的工作。 ECMAScript 5 添加了 indexOf() 和 lastIndexOf() 方法，最终允许开发人员搜索数组中的特定值。 这两种方法是一个很大的改进，但它们仍然相当有限，因为您一次只能搜索一个值。 例如，如果您想在一系列数字中找到第一个偶数，您需要编写自己的代码来执行此操作。 ECMAScript 6 通过引入 find() 和 findIndex() 方法解决了这个问题。

Both find() and findIndex() accept two arguments: a callback function and an optional value to use for this inside the callback function. The callback function is passed an array element, the index of that element in the array, and the array itself–the same arguments passed to methods like map() and forEach(). The callback should return true if the given value matches some criteria you define. Both find() and findIndex() also stop searching the array the first time the callback function returns true.
The only difference between these methods is that find() returns the value whereas findIndex() returns the index at which the value was found. Here’s an example to demonstrate:

find() 和 findIndex() 都接受两个参数：一个回调函数和一个在回调函数中用于 this 的可选值。 回调函数被传递一个数组元素、该元素在数组中的索引以及数组本身——传递给 map() 和 forEach() 等方法的相同参数。 如果给定值与您定义的某些条件匹配，则回调应返回 true。 find() 和 findIndex() 也会在回调函数第一次返回 true 时停止搜索数组。 这些方法之间的唯一区别是 find() 返回值，而 findIndex() 返回找到值的索引。 下面是一个示例来演示：

```js
let numbers = [25, 30, 35, 40, 45];

console.log(numbers.find(n => n > 33));         // 35
console.log(numbers.findIndex(n => n > 33));    // 2
```

This code calls find() and findIndex() to locate the first value in the numbers array that is greater than 33. The call to find() returns 35 and findIndex() returns 2, the location of 35 in the numbers array.

这段代码调用find()和findIndex()来定位数字数组中大于33的第一个值。调用find()返回35,finddex()返回2，即35在数字数组中的位置。

Both find() and findIndex() are useful to find an array element that matches a condition rather than a value. If you only want to find a value, then indexOf() and lastIndexOf() are better choices.

find() 和 findIndex() 都可用于查找匹配条件而不是值的数组元素。 如果你只想找到一个值，那么 indexOf() 和 lastIndexOf() 是更好的选择。

<br />

#### The fill() Method

The fill() method fills one or more array elements with a specific value. When passed a value, fill() overwrites all of the values in an array with that value. For example:

fill() 方法用特定值填充一个或多个数组元素。 当传递一个值时，fill() 会用该值覆盖数组中的所有值。 例如：

```js
let numbers = [1, 2, 3, 4];

numbers.fill(1);

console.log(numbers.toString());    // 1,1,1,1
```

Here, the call to numbers.fill(1) changes all values in numbers to 1. If you only want to change some of the elements, rather than all of them, you can optionally include a start index and an exclusive end index, like this:

在这里，对 numbers.fill(1) 的调用将 numbers 中的所有值都更改为 1。如果您只想更改某些元素，而不是所有元素，则可以选择包含开始索引和排他结束索引，如下所示：

```js
let numbers = [1, 2, 3, 4];

numbers.fill(1, 2);

console.log(numbers.toString());    // 1,2,1,1

numbers.fill(0, 1, 3);

console.log(numbers.toString());    // 1,0,0,1
```

In the numbers.fill(1,2) call, the 2 indicates to start filling elements at index 2. The exclusive end index isn’t specified with a third argument, so numbers.length is used as the end index, meaning the last two elements in numbers are filled with 1. The numbers.fill(0, 1, 3) operation fills array elements at indices 1 and 2 with 0. Calling fill() with the second and third arguments allows you to fill multiple array elements at once without overwriting the entire array.

在numbers.fill(1,2)调用中，2表示开始填充下标为2的元素。排他结束索引没有使用第三个参数指定，因此使用数字。Length用作结束索引，这意味着数字的最后两个元素用1填充。这些数字。Fill(0,1,3)操作将索引为1和2的数组元素填充为0。使用第二个和第三个参数调用fill()允许您在不覆盖整个数组的情况下一次性填充多个数组元素。

<br />

> If either the start or end index are negative, then those values are added to the array’s length to determine the final location. For instance, a start location of -1 gives array.length - 1 as the index, where array is the array on which fill() is called.
>
> 如果开始索引或结束索引为负，则将这些值添加到数组的长度中，以确定最终位置。例如，起始位置为-1的数组。array.length - 1作为索引，结果等效于调用fill()的数组。

<br />

#### The copyWithin() Method

The copyWithin() method is similar to fill() in that it changes multiple array elements at the same time. However, instead of specifying a single value to assign to array elements, copyWithin() lets you copy array element values from the array itself. To accomplish that, you need to pass two arguments to the copyWithin() method: the index where the method should start filling values and the index where the values to be copied begin.

copyWithin() 方法与 fill() 类似，因为它同时更改多个数组元素。 但是，copyWithin() 不是指定要分配给数组元素的单个值，而是让您从数组本身复制数组元素值。 为此，您需要将两个参数传递给 copyWithin() 方法：方法应指定填充值的索引和要复制的值开始的索引。

For instance, to copy the values from the first two elements in an array to the last two items in the array, you can do the following:

例如，要将数组的前两个元素的值复制到数组的最后两个元素，可以执行以下操作:

```js
let numbers = [1, 2, 3, 4];

// paste values into array starting at index 2
// copy values from array starting at index 0
numbers.copyWithin(2, 0);

console.log(numbers.toString());    // 1,2,1,2
```

This code pastes values into numbers beginning from index 2, so both indices 2 and 3 will be overwritten. Passing 0 as the second argument to copyWithin() indicates to start copying values from index 0 and continue until there are no more elements to copy into.
By default, copyWithin() always copies values up to the end of the array, but you can provide an optional third argument to limit how many elements will be overwritten. That third argument is an exclusive end index at which copying of values stops. Here’s an example:

此代码将值粘贴到从索引 2 开始的数字中，因此索引 2 和 3 都将被覆盖。 将 0 作为第二个参数传递给 copyWithin() 表示从索引 0 开始复制值并继续直到没有更多元素要复制到。 默认情况下，copyWithin() 始终将值复制到数组的末尾，但您可以提供可选的第三个参数来限制将覆盖的元素数量。 第三个参数是唯一的结束索引，值的复制在该结束索引处停止。 下面是一个例子：

```js
let numbers = [1, 2, 3, 4];

// paste values into array starting at index 2
// copy values from array starting at index 0
// stop copying values when you hit index 1
numbers.copyWithin(2, 0, 1);

console.log(numbers.toString());    // 1,2,1,4
```

In this example, only the value in index 0 is copied because the optional end index is set to 1. The last element in the array remains unchanged.

<br />

> As with the fill() method, if you pass a negative number for any argument to the copyWithin() method, the array’s length is automatically added to that value to determine the index to use.

<br />

The use cases for fill() and copyWithin() may not be obvious to you at this point. That’s because these methods originated on typed arrays and were added to regular arrays for consistency. As you’ll learn in the next section, however, if you use typed arrays for manipulating the bits of a number, these methods become a lot more useful.

<br />

### <a id="Typed-Arrays"> Typed Arrays </a>

Typed arrays are special-purpose arrays designed to work with numeric types (not all types, as the name might imply). The origin of typed arrays can be traced to WebGL, a port of Open GL ES 2.0 designed for use in web pages with the `<canvas>` element. Typed arrays were created as part of the port to provide fast bitwise arithmetic in JavaScript.

类型化数组是专门用于处理数字类型的数组（并非所有类型，顾名思义）。 类型化数组的起源可以追溯到 WebGL，它是 Open GL ES 2.0 的一个端口，设计用于带有` <canvas>` 元素的网页。 类型化数组是作为端口的一部分创建的，以在 JavaScript 中提供快速的按位算术。

Arithmetic on native JavaScript numbers was too slow for WebGL because the numbers were stored in a 64-bit floating-point format and converted to 32-bit integers as needed. Typed arrays were introduced to circumvent this limitation and provide better performance for arithmetic operations. The concept is that any single number can be treated like an array of bits and thus can use the familiar methods available on JavaScript arrays.

原生 JavaScript 数字的算术对于 WebGL 来说太慢了，因为数字以 64 位浮点格式存储并根据需要转换为 32 位整数。 引入了类型化数组以规避此限制并为算术运算提供更好的性能。 它的概念是，任何单个数字都可以被视为一个位数组，因此可以使用JavaScript数组中常见的方法。

ECMAScript 6 adopted typed arrays as a formal part of the language to ensure better compatibility across JavaScript engines and interoperability with JavaScript arrays. While the ECMAScript 6 version of typed arrays is not exactly the same as the WebGL version, there are enough similarities to make the ECMAScript 6 version an evolution of the WebGL version rather than a different approach.

ECMAScript 6 采用类型化数组作为语言的正式组成部分，以确保跨 JavaScript 引擎更好的兼容性以及与 JavaScript 数组的互操作性。 虽然 ECMAScript 6 版本的类型化数组与 WebGL 版本并不完全相同，但有足够的相似之处使 ECMAScript 6 版本成为 WebGL 版本的演变，而不是一种不同的方法。

<br />

#### Numeric Data Types

JavaScript numbers are stored in IEEE 754 format, which uses 64 bits to store a floating-point representation of the number. This format represents both integers and floats in JavaScript, with conversion between the two formats happening frequently as numbers change. Typed arrays allow the storage and manipulation of eight different numeric types:

JavaScript 数字以 IEEE 754 格式存储，该格式使用 64 位来存储数字的浮点表示。 这种格式在 JavaScript 中同时表示整数和浮点数，随着数字的变化，这两种格式之间的转换经常发生。 类型数组允许存储和操作八种不同的数字类型：

1. Signed 8-bit integer (int8)
2. Unsigned 8-bit integer (uint8)
3. Signed 16-bit integer (int16)
4. Unsigned 16-bit integer (uint16)
5. Signed 32-bit integer (int32)
6. Unsigned 32-bit integer (uint32)
7. 32-bit float (float32)
8. 64-bit float (float64)

If you represent a number that fits in an int8 as a normal JavaScript number, you’ll waste 56 bits. Those bits might better be used to store additional int8 values or any other number that requires less than 56 bits. Using bits more efficiently is one of the use cases typed arrays address.

如果您将一个适合int8的数字表示为一个普通的JavaScript数字，您将浪费56位。这些位可能更好地用于存储额外的int8值或任何其他需要少于56位的数字。更有效地使用位是类型化数组地址的用例之一。

All of the operations and objects related to typed arrays are centered around these eight data types. In order to use them, though, you’ll need to create an array buffer to store the data.

所有与类型化数组相关的操作和对象都以这八种数据类型为中心。 但是，为了使用它们，您需要创建一个数组缓冲区来存储数据。

<br />

> In this book, I will refer to these types by the abbreviations I showed in parentheses. Those abbreviations don’t appear in actual JavaScript code; they’re just a shorthand for the much longer descriptions.
>
> 在本书中，我将通过括号中所示的缩写来说明这些类型。这些缩写并没有出现在实际的JavaScript代码中;它们只是更长的描述的简写。

<br />

#### Array Buffers

The foundation for all typed arrays is an array buffer, which is a memory location that can contain a specified number of bytes. Creating an array buffer is akin to calling malloc() in C to allocate memory without specifying what the memory block contains. You can create an array buffer by using the ArrayBuffer constructor as follows:

所有类型化数组的基础是数组缓冲区，它是可以包含指定数量字节的内存位置。创建数组缓冲区类似于在C中调用malloc()来分配内存，而不指定内存块包含什么。你可以使用ArrayBuffer构造函数创建数组缓冲区，如下所示:

```js
let buffer = new ArrayBuffer(10);   // allocate 10 bytes
```

Just pass the number of bytes the array buffer should contain when you call the constructor. This let statement creates an array buffer 10 bytes long. Once an array buffer is created, you can retrieve the number of bytes in it by checking the byteLength property:

当调用构造函数时，只需传递数组缓冲区应该包含的字节数。let语句创建一个10字节长的数组缓冲区。一旦创建了一个数组缓冲区，你可以通过检查byteLength属性来检索其中的字节数:

```js
let buffer = new ArrayBuffer(10);   // allocate 10 bytes
console.log(buffer.byteLength);     // 10
```

You can also use the slice() method to create a new array buffer that contains part of an existing array buffer. The slice() method works like the slice() method on arrays: you pass it the start index and end index as arguments, and it returns a new ArrayBuffer instance comprised of those elements from the original. For example:

还可以使用slice()方法创建一个新的数组缓冲区，该数组缓冲区包含现有数组缓冲区的一部分。slice()方法的工作方式类似于数组中的slice()方法:将start index和end index作为参数传递给它，它将返回一个由原始数组中的元素组成的新ArrayBuffer实例。例如:

```js
let buffer = new ArrayBuffer(10);   // allocate 10 bytes


let buffer2 = buffer.slice(4, 6);
console.log(buffer2.byteLength);    // 2
```

In this code, buffer2 is created by extracting the bytes at indices 4 and 5. Just like when you call the array version of this method, the second argument to slice() is exclusive.

在此代码中，通过提取索引 4 和 5 处的字节来创建 buffer2。就像调用此方法的数组版本一样， slice() 的第二个参数是独占的。

Of course, creating a storage location isn’t very helpful without being able to write data into that location. To do so, you’ll need to create a view.

当然，如果不能将数据写入存储位置，那么创建存储位置并不是很有帮助。为此，您需要创建一个视图。

<br />

> An array buffer always represents the exact number of bytes specified when it was created. You can change the data contained within an array buffer, but never the size of the array buffer itself.
>
> 数组缓冲区总是表示创建时指定的确切字节数。您可以更改数组缓冲区中包含的数据，但不能更改数组缓冲区本身的大小。

<br />

#### Manipulating Array Buffers with Views

Array buffers represent memory locations, and views are the interfaces you’ll use to manipulate that memory. A view operates on an array buffer or a subset of an array buffer’s bytes, reading and writing data in one of the numeric data types. The DataView type is a generic view on an array buffer that allows you to operate on all eight numeric data types.

数组缓冲区代表内存位置，视图是你用来操作内存的接口。 视图对数组缓冲区或数组缓冲区字节的子集进行操作，以其中一种数值数据类型读取和写入数据。 DataView 类型是数组缓冲区上的通用视图，允许您对所有八种数值数据类型进行操作。

To use a DataView, first create an instance of ArrayBuffer and use it to create a new DataView. Here’s an example:

要使用DataView，首先创建ArrayBuffer的实例并使用它创建一个新的DataView。这里有一个例子:

```js
let buffer = new ArrayBuffer(10),
    view = new DataView(buffer);
```

The view object in this example has access to all 10 bytes in buffer. You can also create a view over just a portion of a buffer. Just provide a byte offset and, optionally, the number of bytes to include from that offset. When a number of bytes isn’t included, theDataView will go from the offset to the end of the buffer by default. For example:

本例中的视图对象可以访问缓冲区中的所有 10 个字节。 您还可以仅在缓冲区的一部分上创建视图。 只需提供一个字节偏移量，以及从该偏移量中包含的字节数（可选）。 当不包含多个字节时，DataView 将默认从偏移量到缓冲区的末尾。 例如：

```js
let buffer = new ArrayBuffer(10),
    view = new DataView(buffer, 5, 2);      // cover bytes 5 and 6
```

Here, view operates only on the bytes at indices 5 and 6. This approach allows you to create several views over the same array buffer, which can be useful if you want to use a single memory location for an entire application rather than dynamically allocating space as needed.

在这里，视图仅对索引 5 和 6 处的字节进行操作。这种方法允许您在同一个数组缓冲区上创建多个视图，如果您想为整个应用程序使用单个内存位置而不是动态分配空间，这将非常有用 如所须。

<br />

##### Retrieving View Information

You can retrieve information about a view by fetching the following read-only properties:

您可以通过获取以下只读属性来检索有关视图的信息：

* buffer - The array buffer that the view is tied to
* byteOffset - The second argument to the DataView constructor, if provided (0 by default)
* byteLength - The third argument to the DataView constructor, if provided (the buffer’s byteLength by default)

Using these properties, you can inspect exactly where a view is operating, like this:

使用这些属性，你可以精确地检查视图操作的位置，像这样:

```js
let buffer = new ArrayBuffer(10),
    view1 = new DataView(buffer),           // cover all bytes
    view2 = new DataView(buffer, 5, 2);     // cover bytes 5 and 6

console.log(view1.buffer === buffer);       // true
console.log(view2.buffer === buffer);       // true
console.log(view1.byteOffset);              // 0
console.log(view2.byteOffset);              // 5
console.log(view1.byteLength);              // 10
console.log(view2.byteLength);              // 2
```

This code creates view1, a view over the entire array buffer, and view2, which operates on a small section of the array buffer. These views have equivalent buffer properties because both work on the same array buffer. The byteOffset and byteLength are different for each view, however. They reflect the portion of the array buffer where each view operates.

这段代码创建了 view1，一个对整个数组缓冲区的视图，以及 view2，它对数组缓冲区的一小部分进行操作。 这些视图具有等效的缓冲区属性，因为它们都在同一个数组缓冲区上工作。 但是，每个视图的 byteOffset 和 byteLength 是不同的。 它们反映了每个视图操作的数组缓冲区部分。

Of course, reading information about memory isn’t very useful on its own. You need to write data into and read data out of that memory to get any benefit.

当然，读取关于内存的信息本身并不是很有用。您需要将数据写入或从该内存中读取数据才能获得任何好处。

<br />

##### Reading and Writing Data

For each of JavaScript’s eight numeric data types, the DataView prototype has a method to write data and a method to read data from an array buffer. The method names all begin with either “set” or “get” and are followed by the data type abbreviation. For instance, here’s a list of the read and write methods that can operate on int8 and uint8 values:

对于 JavaScript 的八种数值数据类型中的每一种，DataView 原型都有一个写入数据的方法和一个从数组缓冲区读取数据的方法。 方法名称均以“set”或“get”开头，后跟数据类型缩写。 例如，以下是可以对 int8 和 uint8 值进行操作的读写方法列表：

* getInt8(byteOffset, littleEndian) - Read an int8 starting at byteOffset
* setInt8(byteOffset, value, littleEndian) - Write an int8 starting at byteOffset
* getUint8(byteOffset, littleEndian) - Read an uint8 starting at byteOffset
* setUint8(byteOffset, value, littleEndian) - Write an uint8 starting at byteOffset

The “get” methods accept two arguments: the byte offset to read from and an optional boolean indicating whether the value should be read as little-endian. (Little-endian means the least significant byte is at byte 0, instead of in the last byte.) The “set” methods accept three arguments: the byte offset to write at, the value to write, and an optional boolean indicating whether the value should be stored in little-endian format.

Though I’ve only shown the methods you can use with 8-bit values, the same methods exist for operating on 16- and 32-bit values. Just replace the 8 in each name with 16 or 32. Alongside all those integer methods, DataView also has the following read and write methods for floating point numbers:

* getFloat32(byteOffset, littleEndian) - Read a float32 starting at byteOffset
* setFloat32(byteOffset, value, littleEndian) - Write a float32 starting at byteOffset
* getFloat64(byteOffset, littleEndian) - Read a float64 starting at byteOffset
* setFloat64(byteOffset, value, littleEndian) - Write a float64 starting at byteOffset

To see a “set” and a “get” method in action, consider the following example:

```js
let buffer = new ArrayBuffer(2),
    view = new DataView(buffer);

view.setInt8(0, 5);
view.setInt8(1, -1);

console.log(view.getInt8(0));       // 5
console.log(view.getInt8(1));       // -1
```

This code uses a two-byte array buffer to store two int8 values. The first value is set at offset 0 and the second is at offset 1, reflecting that each value spans a full byte (8 bits). Those values are later retrieved from their positions with the getInt8() method. While this example uses int8 values, you can use any of the eight numeric types with their corresponding methods.

Views are interesting because they allow you to read and write in any format at any point in time, regardless of how data was previously stored. For instance, writing two int8 values and reading the buffer with an int16 method works just fine, as in this example:

```js
let buffer = new ArrayBuffer(2),
    view = new DataView(buffer);

view.setInt8(0, 5);
view.setInt8(1, -1);

console.log(view.getInt16(0));      // 1535
console.log(view.getInt8(0));       // 5
console.log(view.getInt8(1));       // -1
```

The call to view.getInt16(0) reads all bytes in the view and interprets those bytes as the number 1535. To understand why this happens, take a look at Figure 10-1, which shows what each setInt8() line does to the array buffer.

```js
new ArrayBuffer(2)      0000000000000000
view.setInt8(0, 5);     0000010100000000
view.setInt8(1, -1);    0000010111111111
```

The array buffer starts with 16 bits that are all zero. Writing 5 to the first byte with setInt8() introduces a couple of 1s (in 8-bit representation, 5 is 00000101). Writing -1 to the second byte sets all bits in that byte to 1, which is the two’s complement representation of -1. After the second setInt8() call, the array buffer contains 16 bits, and getInt16() reads those bits as a single 16-bit integer, which is 1535 in decimal.

The DataView object is perfect for use cases that mix different data types in this way. However, if you’re only using one specific data type, then the type-specific views are a better choice.

<br />

##### Typed Arrays Are Views

ECMAScript 6 typed arrays are actually type-specific views for array buffers. Instead of using a generic DataView object to operate on an array buffer, you can use objects that enforce specific data types. There are eight type-specific views corresponding to the eight numeric data types, plus an additional option for uint8 values.

ECMAScript 6 类型化数组实际上是数组缓冲区的类型特定视图。 您可以使用强制执行特定数据类型的对象，而不是使用通用 DataView 对象对数组缓冲区进行操作。 有八种特定于类型的视图对应于八种数值数据类型，另外还有一个用于 uint8 值的选项。

Table 10-1 shows an abbreviated version of the complete list of type-specific views from section 22.2 of the ECMAScript 6 specification.

表10-1显示了ECMAScript 6规范第22.2节中特定类型视图的完整列表的缩写版本。

|  Constructor Name  |  Element Size (in bytes)  |  Description                                  |  Equivalent C Type  |
|  :--------------:  |  :---------------------:  |  :-----------------------------------------:  |  :---------------:  |
|  Int8Array         |  1                        |  8-bit two’s complement signed integer        |  signed char        |
|  Uint8Array        |  1                        |  8-bit unsigned integer                       |  unsigned char      |
|  Uint8ClampedArray |  1                        |  8-bit unsigned integer (clamped conversion)  |  unsigned char      |
|  Int16Array        |  2                        |  16-bit two’s complement signed integer       |  short              |
|  Uint16Array       |  2                        |  16-bit unsigned integer                      |  unsigned short     |
|  Int32Array        |  4                        |  32-bit two’s complement signed integer       |  int                |
|  Uint32Array       |  4                        |  32-bit unsigned integer                      |  int                |
|  Float32Array      |  4                        |  32-bit IEEE floating point                   |  float              |
|  Float64Array      |  8                        |  64-bit IEEE floating point                   |  double             |

The left column lists the typed array constructors, and the other columns describe the data each typed array can contain. A Uint8ClampedArray is the same as a Uint8Array unless values in the array buffer are less than 0 or greater than 255. A Uint8ClampedArray converts values lower than 0 to 0 (-1 becomes 0, for instance) and converts values higher than 255 to 255 (so 300 becomes 255).

左列列出了类型化数组的构造函数，其他列描述了每个类型化数组可以包含的数据。 除非数组缓冲区中的值小于 0 或大于 255，否则 Uint8ClampedArray 与 Uint8Array 相同。 Uint8ClampedArray 将小于 0 的值转换为 0（例如，-1 变为 0）并将大于 255 的值转换为 255（ 所以 300 变成 255）

Typed array operations only work on a particular type of data. For example, all operations on Int8Array use int8 values. The size of an element in a typed array also depends on the type of array. While an element in an Int8Array is a single byte long, Float64Array uses eight bytes per element. Fortunately, the elements are accessed using numeric indices just like regular arrays, allowing you to avoid the somewhat awkward calls to the “set” and “get” methods of DataView.

类型化数组操作仅适用于特定类型的数据。 例如，对 Int8Array 的所有操作都使用 int8 值。 类型化数组中元素的大小也取决于数组的类型。 虽然 Int8Array 中的元素是单字节长，但 Float64Array 每个元素使用 8 个字节。 幸运的是，元素可以像常规数组一样使用数字索引进行访问，从而避免了对 DataView 的“set”和“get”方法的一些笨拙的调用。

<br />

> #### Element Size

> Each typed array is made up of a number of elements, and the element size is the number of bytes each element represents. This value is stored on a BYTES_PER_ELEMENT property on each constructor and each instance, so you can easily query the element size:

```js
console.log(UInt8Array.BYTES_PER_ELEMENT);      // 1
console.log(UInt16Array.BYTES_PER_ELEMENT);     // 2

let ints = new Int8Array(5);
console.log(ints.BYTES_PER_ELEMENT);            // 1
```

<br />

##### Creating Type-Specific Views

Typed array constructors accept multiple types of arguments, so there are a few ways to create typed arrays. First, you can create a new typed array by passing the same arguments DataView takes (an array buffer, an optional byte offset, and an optional byte length). For example:

类型化数组构造函数接受多种类型的参数，因此有几种方法可以创建类型化数组。 首先，您可以通过传递与 DataView 相同的参数（一个数组缓冲区、一个可选的字节偏移量和一个可选的字节长度）来创建一个新的类型化数组。 例如：

```js
let buffer = new ArrayBuffer(10),
    view1 = new Int8Array(buffer),
    view2 = new Int8Array(buffer, 5, 2);

console.log(view1.buffer === buffer);       // true
console.log(view2.buffer === buffer);       // true
console.log(view1.byteOffset);              // 0
console.log(view2.byteOffset);              // 5
console.log(view1.byteLength);              // 10
console.log(view2.byteLength);              // 2
```

In this code, the two views are both Int8Array instances that use buffer. Both view1 and view2 have the same buffer, byteOffset, and byteLength properties that exist on DataView instances. It’s easy to switch to using a typed array wherever you use a DataView so long as you only work with one numeric type.

The second way to create a typed array is to pass a single number to the constructor. That number represents the number of elements (not bytes) to allocate to the array. The constructor will create a new buffer with the correct number of bytes to represent that number of array elements, and you can access the number of elements in the array by using the length property. For example:

```js
let ints = new Int16Array(2),
    floats = new Float32Array(5);

console.log(ints.byteLength);       // 4
console.log(ints.length);           // 2

console.log(floats.byteLength);     // 20
console.log(floats.length);         // 5
```

The ints array is created with space for two elements. Each 16-bit integer requires two bytes per value, so the array is allocated four bytes. The floats array is created to hold five elements, so the number of bytes required is 20 (four bytes per element). In both cases, a new buffer is created and can be accessed using the buffer property if necessary.

<br />

> **NOTE**: If no argument is passed to a typed array constructor, the constructor acts as if 0 was passed. This creates a typed array that cannot hold data because zero bytes are allocated to the buffer.

<br />

The third way to create a typed array is to pass an object as the only argument to the constructor. The object can be any of the following:

* **A Typed Array** - Each element is copied into a new element on the new typed array. For example, if you pass an int8 to the Int16Array constructor, the int8 values would be copied into an int16 array. The new typed array has a different array buffer than the one that was passed in.

* **An Iterable** - The object’s iterator is called to retrieve the items to insert into the typed array. The constructor will throw an error if any elements are invalid for the view type.

* **An Array** - The elements of the array are copied into a new typed array. The constructor will throw an error if any elements are invalid for the type.

* **An Array-Like Object** - Behaves the same as an array.

In each of these cases, a new typed array is created with the data from the source object. This can be especially useful when you want to initialize a typed array with some values, like this:

```js
let ints1 = new Int16Array([25, 50]),
    ints2 = new Int32Array(ints1);

console.log(ints1.buffer === ints2.buffer);     // false

console.log(ints1.byteLength);      // 4
console.log(ints1.length);          // 2
console.log(ints1[0]);              // 25
console.log(ints1[1]);              // 50

console.log(ints2.byteLength);      // 8
console.log(ints2.length);          // 2
console.log(ints2[0]);              // 25
console.log(ints2[1]);              // 50
```

This example creates an Int16Array and initializes it with an array of two values. Then, an Int32Array is created and passed the Int16Array. The values 25 and 50 are copied from ints1 into ints2 as the two typed arrays have completely separate buffers. The same numbers are represented in both typed arrays, but ints2 has eight bytes to represent the data while ints1 has only four.

<br />

### <a id="Similarities-Between-Typed-and-Regular-Arrays"> 类型化数组和常规数组之间的相似性(Similarities Between Typed and Regular Arrays )</a>

Typed arrays and regular arrays are similar in several ways, and as you’ve already seen in this chapter, typed arrays can be used like regular arrays in many situations. For instance, you can check how many elements are in a typed array using the length property, and you can access a typed array’s elements directly using numeric indices. For example:

类型化数组和常规数组在许多方面是相似的，正如您在本章中已经看到的，类型化数组在许多情况下可以像常规数组一样使用。例如，您可以使用length属性检查类型化数组中有多少元素，并且可以使用数字索引直接访问类型化数组的元素。例如:

```js
let ints = new Int16Array([25, 50]);

console.log(ints.length);          // 2
console.log(ints[0]);              // 25
console.log(ints[1]);              // 50

ints[0] = 1;
ints[1] = 2;

console.log(ints[0]);              // 1
console.log(ints[1]);              // 2
```

In this code, a new Int16Array with two items is created. The items are read from and written to using their numeric indices, and those values are automatically stored and converted into int16 values as part of the operation. The similarities don’t end there, though.

在此代码中，创建了一个包含两个项目的新 Int16Array。 使用它们的数字索引读取和写入这些项目，并且这些值作为操作的一部分被自动存储并转换为 int16 值。 不过，相似之处还不止于此。

<br />

> Unlike regular arrays, you cannot change the size of a typed array using the length property. The length property is not writable, so any attempt to change it is ignored in non-strict mode and throws an error in strict mode.
>
> 与常规数组不同，您不能使用length属性更改类型化数组的大小。length属性是不可写的，因此在非严格模式下任何更改它的尝试都将被忽略，并在严格模式下抛出错误。

<br />

#### Common Methods

Typed arrays also include a large number of methods that are functionally equivalent to regular array methods. You can use the following array methods on typed arrays:

类型化数组还包括大量在功能上与常规数组方法等效的方法。 您可以在类型化数组上使用以下数组方法：

* copyWithin()
* entries()
* fill()
* filter()
* find()
* findIndex()
* forEach()
* indexOf()
* join()
* keys()
* lastIndexOf()
* map()
* reduce()
* reduceRight()
* reverse()
* slice()
* some()
* sort()
* values()

Keep in mind that while these methods act like their counterparts on Array.prototype, they are not exactly the same. The typed array methods have additional checks for numeric type safety and, when an array is returned, will return a typed array instead of a regular array (due to Symbol.species). Here’s a simple example to demonstrate the difference:

```js
let ints = new Int16Array([25, 50]),
    mapped = ints.map(v => v * 2);

console.log(mapped.length);        // 2
console.log(mapped[0]);            // 50
console.log(mapped[1]);            // 100

console.log(mapped instanceof Int16Array);  // true
```

This code uses the map() method to create a new array based on the values in ints. The mapping function doubles each value in the array and returns a new Int16Array.

<br />

#### The Same Iterators


Typed arrays have the same three iterators as regular arrays, too. Those are the entries() method, the keys() method, and the values() method. That means you can use the spread operator and for-of loops with typed arrays just like you would with regular arrays. For example:

```js
let ints = new Int16Array([25, 50]),
    intsArray = [...ints];

console.log(intsArray instanceof Array);    // true
console.log(intsArray[0]);                  // 25
console.log(intsArray[1]);                  // 50
```

This code creates a new array called intsArray containing the same data as the typed array ints. As with other iterables, the spread operator makes converting typed arrays into regular arrays easy.

<br />

#### of() and from() Methods


Lastly, all typed arrays have static of() and from() methods that work like the Array.of() and Array.from() methods. The difference is that the methods on typed arrays return a typed array instead of a regular array. Here are some examples that use these methods to create typed arrays:

```js
let ints = Int16Array.of(25, 50),
    floats = Float32Array.from([1.5, 2.5]);

console.log(ints instanceof Int16Array);        // true
console.log(floats instanceof Float32Array);    // true

console.log(ints.length);       // 2
console.log(ints[0]);           // 25
console.log(ints[1]);           // 50

console.log(floats.length);     // 2
console.log(floats[0]);         // 1.5
console.log(floats[1]);         // 2.5
```

The of() and from() methods in this example are used to create an Int16Array and a Float32Array, respectively. These methods ensure that typed arrays can be created just as easily as regular arrays.

<br />

### <a id=""> 类型化数组与常规数组的区别(Differences Between Typed and Regular Arrays )</a>

The most importance difference between typed arrays and regular arrays is that typed arrays are not regular arrays. Typed arrays don’t inherit from Array and Array.isArray() returns false when passed a typed array. For example:

类型化数组和常规数组之间最重要的区别是类型化数组不是常规数组。类型化数组不会从Array继承，而当传入类型化数组时，Array. isarray()返回false。例如:

```js
let ints = new Int16Array([25, 50]);

console.log(ints instanceof Array);     // false
console.log(Array.isArray(ints));       // false
```

Since the ints variable is a typed array, it isn’t an instance of Array and cannot otherwise be identified as an array. This distinction is important because while typed arrays and regular arrays are similar, there are many ways in which typed arrays behave differently.

<br />

#### 行为差异(Behavioral Differences)

While regular arrays can grow and shrink as you interact with them, typed arrays always remain the same size. You cannot assign a value to a nonexistent numeric index in a typed array like you can with regular arrays, as typed arrays ignore the operation. Here’s an example:

当您与常规数组交互时，它们可以增长或收缩，而类型化数组始终保持相同的大小。不能像对常规数组那样对类型化数组中不存在的数字索引赋值，因为类型化数组会忽略该操作。这里有一个例子:

```js
let ints = new Int16Array([25, 50]);

console.log(ints.length);          // 2
console.log(ints[0]);              // 25
console.log(ints[1]);              // 50

ints[2] = 5;

console.log(ints.length);          // 2
console.log(ints[2]);              // undefined
```

Despite assigning 5 to the numeric index 2 in this example, the ints array does not grow at all. The length remains the same and the value is thrown away.

Typed arrays also have checks to ensure that only valid data types are used. Zero is used in place of any invalid values. For example:

```js
let ints = new Int16Array(["hi"]);

console.log(ints.length);       // 1
console.log(ints[0]);           // 0
```

This code attempts to use the string value "hi" in an Int16Array. Of course, strings are invalid data types in typed arrays, so the value is inserted as 0 instead. The length of the array is still one, and even though the ints[0] slot exists, it just contains 0.

All methods that modify values in a typed array enforce the same restriction. For example, if the function passed to map() returns an invalid value for the typed array, then 0 is used instead:

```js
let ints = new Int16Array([25, 50]),
    mapped = ints.map(v => "hi");

console.log(mapped.length);        // 2
console.log(mapped[0]);            // 0
console.log(mapped[1]);            // 0

console.log(mapped instanceof Int16Array);  // true
console.log(mapped instanceof Array);       // false
```

Since the string value "hi" isn’t a 16-bit integer, it’s replaced with 0 in the resulting array. Thanks to this error correction behavior, typed array methods don’t have to worry about throwing errors when invalid data is present, because there will never be invalid data in the array.

<br />

#### 缺少的方法（Missing Methods）

While typed arrays do have many of the same methods as regular arrays, they also lack several array methods. The following methods are not available on typed arrays:

虽然类型化数组确实具有许多与常规数组相同的方法，但它们也缺少一些数组方法。 以下方法在类型化数组上不可用：

* concat()
* pop()
* push()
* shift()
* splice()
* unshift()

Except for the concat() method, the methods in this list can change the size of an array. Typed arrays can’t change size, which is why these aren’t available for typed arrays. The concat() method isn’t available because the result of concatenating two typed arrays (especially if they deal with different data types) would be uncertain, and that would go against the reason for using typed arrays in the first place.

<br />

#### Additional Methods

Finally, typed arrays methods have two methods not present on regular arrays: the set() and subarray() methods. These two methods are opposites in that set() copies another array into an existing typed array, whereas subarray() extracts part of an existing typed array into a new typed array.

最后，类型化数组方法有两个常规数组不存在的方法：set() 和 subarray() 方法。 这两种方法是相反的， set() 将另一个数组复制到现有的类型化数组中，而 subarray() 将现有类型化数组的一部分提取到新的类型化数组中。

The set() method accepts an array (either typed or regular) and an optional offset at which to insert the data; if you pass nothing, the offset defaults to zero. The data from the array argument is copied into the destination typed array while ensuring only valid data types are used. Here’s an example:

set() 方法接受一个数组（类型化的或常规的）和一个可选的用于插入数据的偏移量； 如果不传递任何内容，则偏移量默认为零。 来自数组参数的数据被复制到目标类型化数组中，同时确保只使用有效的数据类型。 下面是一个例子：

```js
let ints = new Int16Array(4);

ints.set([25, 50]);
ints.set([75, 100], 2);

console.log(ints.toString());   // 25,50,75,100
```

This code creates an Int16Array with four elements. The first call to set() copies two values to the first and second elements in the array. The second call to set() uses an offset of 2 to indicate that the values should be placed in the array starting at the third element.

The subarray() method accepts an optional start and end index (the end index is exclusive, as in the slice() method) and returns a new typed array. You can also omit both arguments to create a clone of the typed array. For example:

```js
let ints = new Int16Array([25, 50, 75, 100]),
    subints1 = ints.subarray(),
    subints2 = ints.subarray(2),
    subints3 = ints.subarray(1, 3);

console.log(subints1.toString());   // 25,50,75,100
console.log(subints2.toString());   // 75,100
console.log(subints3.toString());   // 50,75
```

Three typed arrays are created from the original ints array in this example. The subints1 array is a clone of ints that contains the same information. Since the subints2 array copies data starting from index 2, it only contains the last two elements of the ints array (75 and 100). The subints3 array contains only the middle two elements of the ints array, as subarray() was called with both a start and an end index.

<br />

### <a id="Summary> Summary </a>

ECMAScript 6 continues the work of ECMAScript 5 by making arrays more useful. There are two more ways to create arrays: the Array.of() and Array.from() methods. The Array.from() method can also convert iterables and array-like objects into arrays. Both methods are inherited by derived array classes and use the Symbol.species property to determine what type of value should be returned (other inherited methods also use Symbol.species when returning an array).

ECMAScript 6延续了ECMAScript 5的工作，使数组更加易用。还有两种创建数组的方法:Array.of()和Array.from()方法。from()方法还可以将可迭代对象和类数组对象转换为数组。这两个方法都由派生数组类继承并使用Symbol。物种属性来确定应该返回什么类型的值(其他继承的方法也使用Symbol。返回数组时的物种)。

There are also several new methods on arrays. The fill() and copyWithin() methods allow you to alter array elements in-place. The find() and findIndex() methods are useful for finding the first element in an array that matches some criteria. The former returns the first element that fits the criteria, and the latter returns the element’s index.

还有一些关于数组的新方法。 fill() 和 copyWithin() 方法允许您就地更改数组元素。 find() 和 findIndex() 方法可用于查找数组中符合某些条件的第一个元素。 前者返回符合条件的第一个元素，后者返回元素的索引。

Typed arrays are not technically arrays, as they do not inherit from Array, but they do look and behave a lot like arrays. Typed arrays contain one of eight different numeric data types and are built upon ArrayBuffer objects that represent the underlying bits of a number or series of numbers. Typed arrays are a more efficient way of doing bitwise arithmetic because the values are not converted back and forth between formats, as is the case with the JavaScript number type.

从技术上讲，类型化数组不是数组，因为它们不继承Array，但它们的外观和行为非常像数组。类型化数组包含八种不同的数字数据类型中的一种，并构建于ArrayBuffer对象之上，这些对象表示数字或数字系列的底层位。类型化数组是逐位运算的更有效方法，因为值不会像JavaScript数字类型那样在格式之间来回转换。

<br />
