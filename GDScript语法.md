### Hello World
+ 创建空白测试场景（Main）
+ 为场景添加默认脚本-可以看到脚本中有保函ready的函数，<u>此函数证明在首次进入场景树时会被立即调用，所以此函数下可放置那些我们希望游戏开始立即执行的函数</u>
+ 将什么都不执行的pass，替换成打印消息

```javascript
extends Node

// ready函数，首次进入场景树立即执行
func _ready():
  print("Hello World!")
```

### Syntax-语法
+ 语句以换行符结束，不用在末尾添加分号
+ 使用缩进制表符来构建代码结构，如上面所示代码，表示print属于ready函数，去除制表符则会出现错误
+ 区分大小写，除用到的命令外，定义常量用大写，定义变量用小写

### Modifying Nodes-修改节点
+ 1.0
    - 创建一个label标签，并做基础设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734572319627-9ab18b3f-3523-4d5e-93a3-ef48c7668378.png)

    - 回到Main脚本中，拖动label到ready函数下做节点引入，引入后根据想要调整的属性进行赋值

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734573240108-4b9597fb-0a6c-4413-bfbf-20c613c47262.png)

+ 2.0
    - 普通节点引入变量及确认节点绝对路径

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734680474000-4a4afca8-8db9-451f-a862-345a0017cc12.png)

    - `$`的作用等同于`get_node("")`，该路径为父节点相对路径
    - 导出节点并可在检查器中进行设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734680726100-d518ab29-a287-4f34-871a-b65dd9abc66c.png)

### Input-输入
+ 项目设置输入映射中做输入动作设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734573876150-8e53c0e5-8136-451c-aace-e21ae78649db.png)

+ 回到脚本中创建输入方法：

```javascript
extends Node

// 进入场景树立即执行
func _ready() -> void:
	$Label.text = "Hello World!"
	$Label.modulate = Color.CRIMSON

// 创建输入方法，所有方法以_开头
func _input(event: InputEvent) -> void:
  // 判断预计判断是否执行按键行为的函数
	if event.is_action_pressed("change_color"):
		$Label.modulate = Color.GREEN
		
	if event.is_action_released("change_color"):
		$Label.modulate = Color.CRIMSON
```

### Variables-变量
变量是储存数据的容器，比如可以用来储存玩家（player）的各项数值，诸如：名称（name）、生命值（health）、伤害（attack）、存活状态（is_alive)等

+ 1.0
    - 创建为生命值的变量，初始值为100
    - 设置每次点击空格执行生命值减20的操作
    - 打印生命值数字

```javascript
// 声明变量生命值为100
var health = 100

// 创建输入方法
func _input(event: InputEvent) -> void:
  // 判断按键操作
	if event.is_action_pressed("change_color"):
    // 调整生命值数值
		health -= 20
		print(health)
```

+ 2.0

声明变量的时候要考虑到作用域（Scope），若在if语句中声明变量，则该变量只能在if语句中使用

    - 为保证声明变量能在脚本任意位置使用，创建独立于任何函数和方法之外的全局变量
    - 声明变量不需要考虑数值类型，直接赋值和再次赋值也不会发生错误
    - 数值类型（Date Types）：
        * 布尔值（bool）：true&false
        * 整数（int）：3008
        * 浮点数（float）：带有小数的数字 2.89
        * 字符串（string）：文本
        * 数值类型间的相互转化被称为数值转换：`var pi = 3.9   print(int(pi)）`打印的值为3
    - <u>常见的数据结构是Vector2和Vector3</u>，可直接储存浮点数，常用于表示坐标：

```javascript
entends Node

func _ready() -> void:
  var vec2 = Vector2(0.0,0.0) 
  var vec3 = Vector3(0.0,0.0,0.0)
// 最常用表示位置，并改变其中一个分量

var position = Vector3(3,-10,5)
position.x += 2
print(position)
// 输出结果为（5,-10,5)
```

    - GDScript中的变量为动态类型，创建变量方便但容易出错，可调整为静态类型，只需在声明变量时做好限制数值类型即可

```javascript
// 动态类型变量
var a = 10
var b = true
// 重新赋值可直接改变变量数值类型
  a = true
// 可创建变量时直接锁定数值类型
var a: int = 10
// 即设定变量a仅能作为整数类型，无法变成其他数值类型
// 或简写使用推断类型
var a := 10
```

    - 若在声明的变量前加上`@export`，则可以在节点的检查器处设置它：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734578448664-56d9e9e3-26d7-4f29-8cc0-c4009563aa65.png)

    - 想定义一个不可改变的值，可用到常量`const SPEED = 60`

### If-statements-if语句
if语句是所有语法的粘合剂，if作为一个条件，只有满足这个条件，里面的语法才能被执行

+ 创建一个if语句判断玩家生命值是否降为0，降为0输出“YOU DIED！”

```javascript
var health = 100

func _input(event: InputEvent) -> void:
	if event.is_action_pressed("change_color"):
		health -= 20
		print(health)

    // if语句判断生命值是否降为0
    if health <= 0:
      health = 0
      print("YOU DIE!")
```

+ 更多比较方法：
    - 等于：X == Y
    - 不等于：X != Y
    - 大于、小于、大于等于、小于等于等
+ 可以再一个if语句中添加多个比较条件：X ==Y and Y > Z（两个条件都要满足）

or 两个条件只需满足其中一个；

+ if语句即可变现真发生的，也可以表现除此以外的假发生情况，用else创建，++同时可使用elif来表示else if 来串联两个if语句

```javascript
// 创建一个判断玩家状态的语句，生命值小于50打印受伤，生命值等于0打印死亡，否则打印健康
var health = 100

func _input(event:InputEvent) -> void:
  if event.is_action_pressed("change_color"):
    health -= 20
    print(health)

// 根据不同血量判断不同状态
    if health <= 0:
      health = 0
      print("YOU DIED!")
    elif health <50:
      print("YOU ARE HURT!)
    else:
        print("YOU ARE GOOD!")
```

### function-函数
+ 内置函数：函数前带`_`例如，`_input`；`_ready`等，这类函数不由我们调用，仅由引擎调用
+ 自建函数：我们可自己创建函数并引用，例如玩家的跳跃、死亡、射击、重生等

```javascript
func jump():
func die():
func shoot():
func respawn():
```

+ 函数相当于一个工具，我们需要输入参数，函数会返回结果供我们调用

```javascript
extends Node
// 5.在此函数内调用下面的函数
func _ready() -> void:
  var result = add(3 , 5)
  // 可复用多次计算
  result = add(result , 5)
  // 6.打印结果为13
  print(result)

// 1.创建一个函数，用于两个数计算，并返回结果
// 2.借助num1:int限制参数的数值类型为整数;使用->来限制返回值数值类型
func add(num1:int , num2:int) -> int:
  // 3.声明一个用于该函数的变量，用来储存数据，计算相加结果
  var result = num1 + num2
  return result
  // 4.为了能让上面函数调用result，使用return而不是print，否则只能打印而不能调用
```

### random number-随机数字
+ randf会给出一个0~1之间的随机数，可用于给代码分配概率，例如给玩家获取道具的稀有度

```javascript
var roll = randf()
if roll <= 0.8:
  print("你找到了一件普通物品")
else:
  print("你找到了一件稀有物品")
```

+ 可以使用randf_range（浮点数）和randi_range（整数）来获取一个在最大值和最小值之间的随机浮点数和整数，比如给角色分配一个随机高度：

```javascript
var height = randi_range(140 , 210)
  print("你的角色身高是：" + str(height) + " cm高")
```

### Arrays&loops-数组&循环
+ 数组应用在需要一个变量来储存布置一个东西，可能是想存储一整列元素
+ 声明变量时使用`[]`包裹，数据类型可混组：`var items = ["books" , 3 , 6]`
+ 同样可限制数组数值类型
+ 访问数组数值用到索引，数组中第一个数据为“0”，同样可使用索引进行赋值、删除、新增

```javascript
entends Node
// 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Books" , "Cards" , "6"]
// 对数组中第三个元素重新赋值
items[2] = "Potion"
// 删除数组第一个元素，新增一个元素
items.remove_at(0)
items.append("Staff")
// 现在数组变为["Cards" , "Potion" , "Staff"]
// 打印最后一个元素,输出Staff
print(items[2])
```

+ 数组会变得很长且难以手动遍历，用到 <u>Loops-循环</u>
    - 循环允许我们重复代码多次，每次有不同变化，适合遍历数组中的所有数据
    - 使用`for`语句：

```javascript
entends Node
// 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Cards" , "Potion" , "Staff"]
// 利用循环打印所有数组中的元素
for item in items:
  print(item)
// 结果将输出所有数组内容
```

    - 可设定数组数据长度按条件打印内容：

```javascript
entends Node
// 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Cards" , "Potion" , "Staff"]

for item in items:
  if item.length() > 5:
    print(item)
// 输出结果为Potion，因为只有这个满足字符长度大于6
```

    - 用`while`语句创建条件循环，例如，我设定一个杯子，想杯子里不断加水，直到杯子半满后循环结束，打印杯子已经半满了：

```javascript
entends Node

func _ready() -> void:

  // 创建杯子变量，默认含水量为0
  var glass := 0.0
  // 创建循环条件，并设置未达标持续增加随机浮点数
  while glass < 0.5:
      glass += randf_range(0.01 , 0.2)
      print(glass)
  // 循环结束打印杯子半满
  print("杯子半满了")
```

	输出结果如下：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734659868523-c9ed2820-316e-4b55-83cc-8f15faf945ab.png)

    - 还可使用`break：跳出循环执行循环后的指令`和`continue：跳到循环的下一次迭代`来设置条件变化

```javascript
entends Node

func _ready() -> void:

  var glass := 0.0
  while glass < 0.5:
      glass += randf_range(0.01 , 0.2)

  // 加上条件跳出循环
      if glass > 0.2:
          break
      print(glass)

  print("杯子半满了")
```

结果如下：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734660125350-6761c321-51b0-4ba1-91c0-0cd3d417a776.png)

### Dictionaries-字典
+ 使用索引访问数组元素会比较麻烦，可用到字典
+ 字典分为：key（键）和value（值），键是我们要查找的元素，值是元素的定义
+ 字典用`{}`包含并表示，括号内放入键对值，例如创建三个不同等级的玩家：

```javascript
// 创建数组并在数组中添加三个玩家角色，配备对应等级
var players{
  "Dog": 1,
  "Cat": 10,
  "Bird": 100,
}
// 修改已有键对值，添加新的键对值
players["Cat"] = 30
players["Mouse"] = 999
// 用循环语句打印所有字典内容
for username in players:
  print(username + ": " + str(players[username]))
```

输出结果为：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734674358263-fc2c9631-8cee-4941-a73d-f93e75e08211.png)

+ 字典内可同样嵌套字典，调整字典内角色具备等级和血量属性：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734674676609-39a56627-2094-48ab-9aa5-5360b43bb228.png)

### Enums-枚举
+ 给游戏定义标签和状态的便捷方式，如游戏中有很多单位，要定义敌对中立友军等标签：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734678614968-5b5e72fa-7bfc-487d-8bf9-eb842130f052.png)

+ 可以导出标记变量，直接在检查器处进行修改

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734678775062-31a7789a-684c-446d-a455-e7e89346f3fd.png)

+ 枚举的工作原理实际上给每个标签定义了常量，等同于索引，同样可进行修改：

```javascript
enum Alignment {Ally, Neutral, Enemy}
// 下面这段代码等同于上面的枚举：
const Ally = 0
const Neutral =1
const Enemy = 2
// 此时打印其中一个会输出其常量
print(Alignment.Enemy)
// 结果为2

// 在枚举中直接设置标签常量
enum Alignment {Ally = 1, Neutral = 0, Enemy = -1}
// 此时打印Enemy输出为-1
```

### match-匹配
+ 匹配和完美的适用于枚举语句，可以迅速为某个状态添加功能
+ 允许根据变量值执行不同代码

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734679635426-4fe5ebd8-dbdc-4068-8636-2ea2300b7b5d.png)

### Signals-信号




