## markdown语法

1. 文件后缀为 *.md
	
## 常见语法

1. 基础语法不在阐述，这里只讲一下常见的可能会忘记的一些模板。
### 插入文件
 
语法：`[someText](相对路径)`

实例：
```text
	[点我查看首页说明](../../README.md)
``` 

### 插入代码块
语法：
```markdown
	```javascript
		function testA() {
		    console.log(document.getElementById('#app'))
		}
	```
```
实例：
```javascript
		function testA() {
		    console.log(document.getElementById('#app'))
		}
```

### 插入 UML类图
语法：
```text
	```uml
        A -> B
    ```
```
实例：
```uml
A -> B
```

### 插入表格
语法：
```markdown
	| 表头1 | 表头2  | 表头3 |
	| :--- | :--- | :--- |
	|  |  |  |
	|  |  |  |
```
全体靠左实例：

| 表头1 | 表头2  | 表头3 |
| :--- | :--- | :--- |
| 香蕉 | 芒果 | 西瓜 |
| 椰子 | 樱桃 | 哈密瓜 |


全体居中实例：

| 表头1 | 表头2  | 表头3 |
| :---:| :---: | :---:|
| 香蕉 | 芒果 | 西瓜 |
| 椰子 | 樱桃 | 哈密瓜 |

