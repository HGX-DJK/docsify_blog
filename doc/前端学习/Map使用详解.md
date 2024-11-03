
**Map** 是 JavaScript 中的一种数据结构，它允许我们以键值对的形式存储数据。与普通的 JavaScript 对象相比，Map 对象有一些显著的优势，比如键可以是任何类型的值（包括对象和函数），而不仅仅是字符串或符号。此外，Map 维护键值对的插入顺序，并且其迭代性能更好。

**1. 创建一个 Map**

```js
const myMap = new Map();
```

你还可以在创建 Map 的同时，使用一个数组初始化它。这个数组包含一组键值对的数组。

```js
const myMap = new Map([
  ['key1', 'value1'],
  ['key2', 'value2'],
  ['key3', 'value3']
]);
```

**2. 添加元素**

使用 set 方法可以向 Map 中添加一个键值对。

```js
myMap.set('key1', 'value1');
myMap.set('key2', 'value2');

// 键可以是任何类型的值
const objKey = { id: 1 };
myMap.set(objKey, 'value3');

```

**3. 读取元素**

使用 get 方法通过键读取对应的值。

```js
const value1 = myMap.get('key1');
console.log(value1); // 输出: 'value1'

const value2 = myMap.get(objKey);
console.log(value2); // 输出: 'value3'
```

**4. 检查键是否存在**

使用 has 方法检查 Map 中是否存在某个键。

```js
console.log(myMap.has('key1')); // 输出: true
console.log(myMap.has('key4')); // 输出: false
```

**5. 删除元素**

使用 delete 方法可以删除指定的键值对

```js
myMap.delete('key2');
console.log(myMap.has('key2')); // 输出: false
```

**6. 获取 Map 的大小**

使用 size 属性可以获取 Map 中键值对的数量。

```js
console.log(myMap.size); // 输出: 2
```

**7. 清空 Map**

```js
myMap.clear();
console.log(myMap.size); // 输出: 0
```

**8. 遍历 Map**

Map 提供了几种遍历方式，包括 **keys()、values()、entries()** 方法和 **forEach** 方法。

* keys(): 返回一个包含 Map 中所有键的迭代器。

```js
for (const key of myMap.keys()) {
  console.log(key);
}
```

获取所有的key的方法：
// 使用 Array.from() 将键迭代器转换为数组
const keysArray = Array.from(myMap.keys());

* values(): 返回一个包含 Map 中所有值的迭代器。

```js
for (const value of myMap.values()) {
  console.log(value);
}
```

获取所有的value的方法：
// 使用 Array.from() 将键迭代器转换为数组
const valuesArray = Array.from(myMap.values());

* entries(): 返回一个包含 Map 中所有键值对的迭代器。每个键值对表示为 [key, value] 的数组。

```js
for (const [key, value] of myMap.entries()) {
  console.log(`${key}: ${value}`);
}
```

* forEach(): 对 Map 中的每个键值对执行指定的函数。

```js
myMap.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});
```

**9. Map 与对象的区别**

* **键的类型**: Map 的键可以是任何值类型，包括对象、函数和原始类型，而对象的键只能是字符串或符号。

* **迭代顺序**: Map 按照插入顺序迭代键值对，而对象的键顺序可能不保证。

* **性能**: Map 的查找和删除操作在大多数情况下比对象更快，特别是当键不是字符串时。

**总结**
**Map** 是一种非常强大的数据结构，适用于需要频繁增删改查操作的场景，尤其是当键类型不是字符串时。与普通对象相比，它提供了更灵活和高效的键值存储方式。