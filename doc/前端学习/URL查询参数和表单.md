## URL查询参数和表单

URLSearchParams 和 FormData 是在 JavaScript 中处理 URL 查询参数和表单数据的两个不同的对象。

### 1.1 URLSearchParams

URLSearchParams 对象用于处理 URL 查询参数部分。它提供了一组方法来添加、删除和检索参数。
通常，你可以通过实例化 URLSearchParams 对象，然后使用其方法来构建 URL 查询参数。

1、**用途**： 主要用于构建 URL 查询字符串，方便将数据附加到 URL。<br/>
2、**构造方法**： 直接通过传递一个查询字符串或一个包含查询参数的对象来创建。<br/>
3、**数据格式**： 包含键值对，每个键值对对应一个查询参数。<br/>
4、**文件上传**： 不适用于文件上传，主要用于处理文本数据。<br/>

**例子**

```js
let params = new URLSearchParams();
params.append('key1', 'value1');
params.append('key2', 'value2');
console.log(params.toString()); // 输出 "key1=value1&key2=value2"
```

### 1.2 FormData

FormData 对象用于构建表单数据。它通常与 HTML 表单元素一起使用，用于收集用户输入的数据。
可以通过将表单元素传递给 FormData 构造函数来创建 FormData 对象，也可以通过 append 方法添加键值对。

1、**用途**： 主要用于构建表单数据对象，以便通过 AJAX 请求将其发送到服务器。<br/>
2、**构造方法**： 可以通过将一个表单元素或手动创建的 FormData 实例传递给构造函数来创建。<br/>
3、**数据格式**： 可以包含键值对，每个键对应一个表单字段，值是用户输入的数据。<br/>
4、**文件上传**： FormData 可以用于上传文件，通过 append 方法添加文件字段。<br/>

**例子**

```js
let formData = new FormData();
formData.append('username', 'john_doe');
formData.append('email', 'john_doe@example.com');
```

### 1.3 进行数据请求时

**FormData**:

* Content-Type: multipart/form-data; boundary=---------------------------[boundary]
* FormData 通常用于上传文件或包含二进制数据的请求，因此请求头中的 Content-Type 是 multipart/form-data。

**URLSearchParams** ：

* Content-Type: application/x-www-form-urlencoded;charset=UTF-8  <br/>
* URLSearchParams 主要用于发送表单数据，因此请求头中的 Content-Type 是 application/x-www-form-urlencoded。这是常用于 HTML 表单提交的一种格式。

### 1.4 区别

#### 1.4.1 Use Case

URLSearchParams 主要用于处理 URL 查询参数，而 FormData 用于构建和处理表单数据。

#### 1.4.2 Data Format

URLSearchParams 保存键值对，主要用于构建 URL 查询参数字符串。
FormData 可以包含键值对，也可以包含文件等二进制数据，用于模拟表单提交。

#### 1.4.3 Usage

URLSearchParams 适用于处理简单的键值对，通常在处理 AJAX 请求时用于构建 URL 查询参数。
FormData 更适合处理表单数据，特别是包含文件上传的表单。

### 1.5 总结
   * FormData 主要用于处理表单数据，特别是包含文件上传的表单。
   * URLSearchParams 主要用于构建 URL 查询字符串，方便将数据附加到 URL。<br/>
  

在实际应用中，你可能会根据具体的需求选择使用其中的一个。如果你需要上传文件，或者通过 AJAX 请求发送表单数据，使用 FormData 更为合适。如果你只是需要构建查询字符串，方便将数据附加到 URL，那么使用 URLSearchParams 更为简单方便。
