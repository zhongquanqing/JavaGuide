## markdown模板

## 分割线
------------------------------

利用`-------------------` 
可以在分隔处画出优雅的分割线

## 常用标题
标题下列说明文字，默认即可，无需额外处理样式。

## 强调内容
如果需要一段比较重要的内容需要强调 语法为 `!> 强调内容：警告色`

!> 一段比较重要的内容

## 普通提示
一般性的建议内容，可以采用下列 `?> 普通建议：绿色`

?> 一段普通的提示内容

## github的任务列表
```markdown
- [ ] foo
- bar
- [x] baz
- [] bam <~ not working
  - [ ] bim
  - [ ] lim
```
## 使用表格

| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |

```markdown
| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| 左侧冒号代表左侧显示 |  两侧都有冒号代表居中 | 右侧冒号代表局右显示 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |
```

当然markdown完全支持html语法，所以你也可以使用html标签来实现
```markdown
<table>
  <tr>
    <th>Tables</th>
    <th>Are</th>
    <th>Cool</th>
  </tr>
  <tr>
    <td>col 1 is</td>
    <td>left-aligned</td>
    <td>$1600</td>
  </tr>
  <tr>
    <td>col 2 is</td>
    <td>centered</td>
    <td>$12</td>
  </tr>
  <tr>
    <td>col 3 is</td>
    <td>right-aligned</td>
    <td>$1</td>
  </tr>
</table>
```


## 引入图片

`![文档logo](public/icon/logo.png)`

语法格式为： `![图片标题](括号内部写相对路径)`

![文档logo](public/icon/logo.png)
#### 引入图片缩放

?> todo 待处理

![文档logo](public/icon/logo.png 'size=40')

## 引入链接（引用）

## 忽略编译链接
?> todo 带完善

## 如何部署文档？
[查看详细](./笔记说明/部署说明/readme.md)
