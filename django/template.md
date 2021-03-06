# django 内建标签
## autoescape
控制当前自动转义的行为，有 on 和 off 两个选项

```html
{% autoescape on %}
    {{ body }}
{% endautoescape %}
```
## block
定义一个子模板可以覆盖的块，在模板中（上一节）有使用示例

## comment
注释，{% comment %} 和 {% endcomment %}之间的内容被解释为注释

## crsf_token
一个防止 CSRF 攻击（跨站点请求伪造）的标签

## cycle
循环给出的字符串或者变量，可以混用
```html
{% for o in some_list %}
    <tr class="{% cycle 'row1' rowvalue2 'row3' %}">
        ...
    </tr>
{% endfor %}
```
值得注意的是，这里的变量的值默认不是自动转义的，要么你相信你的变量，要么你就是用强制转义的方法，
```html
{% for o in some_list %}
    <tr class="{% filter force_escape %}{% cycle rowvalue1 rowvalue2 %}{% endfilter %}">
        ...
    </tr>
{% endfor %}
```
在某些情况下，你可能想循环外部引用循环的下一个值，这时你需要用 as 给 cycle 标签一个名字，这个名字代表的是当前循环的值，但你可以在 cycle 标签里面是用这个变量来获得循环的下一个值
```html
<tr>
    <td class="{% cycle 'row1' 'row2' as rowcolors %}">...</td>
    <td class="{{ rowcolors }}">...</td>
</tr>
<tr>
    <td class="{% cycle rowcolors %}">...</td>
    <td class="{{ rowcolors }}">...</td>
</tr>
```
渲染的结果是
```html
<tr>
    <td class="row1">...</td>
    <td class="row1">...</td>
</tr>
<tr>
    <td class="row2">...</td>
    <td class="row2">...</td>
</tr>
```
但是 cycle 标签一旦定义，默认就会用循环的第一个值，当你仅仅是想定义一个循环，而不想打印循环的值的时候（比如你在父模板定义变量以方便继承），你可以是用 cycle 的 silent 参数（必须保证 silent 是 cycle 的最后一个参数，并且 silent 也具有继承的特点，尽管第二行的 cycle 没有 silent 参数，但由于 rowcoclors 是前面定义的且包含 silent 参数的，第二个 cycle 也具有 silent 循环的特点
```html
{% cycle 'row1' 'row2' as rowcolors silent %}
{% cycle rowcolors %}
```
输出所有的调试信息，包括当前上下文和导入的模块

## extends
- 表示说当前模板继承了一个父模板
- 接受一个包含父模板名字的变量或者字符串常量

## filter
通过可用的过滤器过滤内容，过滤器之间还可以相互（调用）
```html
{% filter force_escape|lower %}
    This text will be HTML-escaped, and will appear in all lowercase.
{% endfilter %}
```
## firstof
返回列表中第一个可用（非 False）的变量或者字符串，注意的是 firstof 中的变量非自动转义
```html
{% firstof var1 var2 var3 "fallback value" %}
```
## for
for 循环，可以在后面加入 reversed 参数遍历逆序的列表
```html
{% for obj in list reversed %
```
你还可以根据列表的数据来写 for 语句，例如对于字典类型的数据
```html
{% for key, value in data.items %}
    {{ key }}: {{ value }}
{% endfor %}
for:
- for 循环还有一系列有用的变量
- forloop.counter	当前循环的索引，从 1 开始
- forloop.counter0	当前循环的索引，从 0 开始
- forloop.revcounter	当前循环的索引（从后面算起），从 1 开始
- forloop.revcounter0	当前循环的索引（从后面算起），从 0 开始
- forloop.first	如果这是第一次循环返回真
- forloop.last	如果这是最后一次循环返回真
- forloop.parentloop	如果是嵌套循环，指的是外一层循环
## for...empty
如果 for 循环的参数 - 列表为空，将执行 empty 里面的内容
```html
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% empty %}
    <li>Sorry, no athlete in this list!</li>
{% endfor %}
<ul>
```

## if
条件语句
```html
{% if athlete_list %}
    Number of athletes: {{ athlete_list|length }}
{% elif athlete_in_locker_room_list %}
    Athletes should be out of the locker room soon!
{% else %}
    No athletes.
{% endif %}
```
## 布尔操作符
- 在 if 标签里面可以使用 and，or, not, in, not in 布尔操作符
- ==,!=,<,>,<=,>=,in,not in 等操作符
	{% if var >= 90 %}
- 这些操作符就不一一详细说了，一目了然
- 在 if 标签里面，这些操作符可以做成复杂的表达式

## ifchange
- 检测一个值在循环的最后有没有改变
- 所以这个标签实在循环里面是用的，有两个用法：
	- 没有接受参数时，比较的是 ifchange 标签里面的内容相比以前是否有变化，有变化时生效
	- 接受一个或以上各参数的时候，如果有一个或者以上的参数发生变化时，有变化时生效
- ifchange 可以有 else 标签
```html
{% for match in matches %}
    <div style="background-color:
        {% ifchanged match.ballot_id %}
            {% cycle "red" "blue" %}
        {% else %}
            grey
        {% endifchanged %}
    ">{{ match }}</div>
{% endfor %}
```
## ifequal
- 仅当两个参数相等的时候输出块的内容，可以配合 else 输出

```html
{% ifequal user.username "adrian" %}
    ...
{% endifequal %}
```
## ifnotequal
跟 ifequal 类似

## include
- 加载一个模板并用当前上下文（include 该模板的模板的上下文）渲染它，接受一个变量或者字符串参数
- 当然你也可以在 include 的时候传递一些参数进来
```html
{% include "name_snippet.html" with person="Jane" greeting="Hello" %}
```
如果你只想接受传递的参数，不接受当前模板的上下文时，你可以是用 only 参数

```html
{% include "name_snippet.html" with greeting="Hi" only %}
```
## load
加载一个自定义的模板标签集合，见单独的一节讲解

## now
显示当前的时间日期，接受格式化字符串的参数
```python
It is {% now "jS F Y H:i" %}
```
参数有已经定义好的一些参考参数： DATE_FORMAT（月日年）, DATETIME_FORMAT（月日年时）,SHORT_DATE_FORMAT（月 / 日 / 年） or SHORT_DATETIME_FORMAT（月 / 日 / 年 / 时）
- regroup
通过共同的属性对一个列表的相似对象重新分组，加入你有一个城市（city）的列表如下
```python
cities = [
    {'name': 'Mumbai', 'population': '19,000,000', 'country': 'India'},
    {'name': 'Calcutta', 'population': '15,000,000', 'country': 'India'},
    {'name': 'New York', 'population': '20,000,000', 'country': 'USA'},
    {'name': 'Chicago', 'population': '7,000,000', 'country': 'USA'},
    {'name': 'Tokyo', 'population': '33,000,000', 'country': 'Japan'},
]
```
你想按照国家 country 这个属性来重新分组已得到下面的结果，那么你可以这么做

- India
	- Mumbai: 19,000,000
	- Calcutta: 15,000,000
- USA
	- New York: 20,000,000
	- Chicago: 7,000,000
- Japan
	- Tokyo: 33,000,000
```html
{% regroup cities by country as country_list %}
<ul>
{% for country in country_list %}
    <li>{{ country.grouper }}
    <ul>
        {% for item in country.list %}
          <li>{{ item.name }}: {{ item.population }}</li>
        {% endfor %}
    </ul>
    </li>
{% endfor %}
</ul>
```
值得注意的是，regroup 并不会重新排序，所以，请确保 city 在 regroup 之前已经按 country 排好序，否则将得不到你预期想要的结果，如果不确定可以用 dictsort 过滤器排序

```html
{% regroup cities|dictsort:"country" by country as country_list %}
```
## spaceless
移除 html 标签之间的空格，注意是标签之间的空格，标签与内容之间的空格不会被删除

```html
{% spaceless %}
    <p>
        <a href="foo/">Foo</a>
    </p>
{% endspaceless %}
```
结果是

```html
<p><a href="foo/">Foo</a></p>
```
## ssi
在页面上输出给定文件的内容
```html
{% ssi /home/html/ljworld.com/includes/right_generic.html %}
```
使用 parsed 参数可以使得输入的内容可以作为一个模板从而可以使用当前模板的上下文
```html
{% ssi /home/html/ljworld.com/includes/right_generic.html parsed %}
```
## url
返回一个绝对路径的引用（没有域名的 url），接受的第一个参数是一个视图函数的名字，然后从 urls 配置文件里面找到那个视图函数对应的 url，

## widthratio
这个标签计算给定值与最大值的比率，然后把这个比率与一个常数相乘，返回最终的结果

```html
<img src="bar.gif" height="10" width="{% widthratio this_value max_value 100 %}" />
```
## with
用更简单的变量名缓存复杂的变量名

```html
{% with total=business.employees.count %}
    {{ total }} employee{{ total|pluralize }}
{% endwith %}
```
尽管初衷是这样，但你不必都是如此，哈哈

```html
{% with alpha=1 beta=2 %}
    ...
{% endwith %}
```
