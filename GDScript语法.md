[语法教程视频链接](https://www.bilibili.com/video/BV1Wz42127wh?spm_id_from=333.788.videopod.sections&vd_source=f2f0c4b25442f945a04f9cdb159461a4)

### Hello World

- 创建空白测试场景（Main）
- 为场景添加默认脚本-可以看到脚本中有保函 ready 的函数，<u>此函数证明在首次进入场景树时会被立即调用，所以此函数下可放置那些我们希望游戏开始立即执行的函数</u>
- 将什么都不执行的 pass，替换成打印消息

```gdscript
extends Node

# ready函数，首次进入场景树立即执行
func _ready():
  print("Hello World!")
```

### Syntax-语法

- 语句以换行符结束，不用在末尾添加分号
- 使用缩进制表符来构建代码结构，如上面所示代码，表示 print 属于 ready 函数，去除制表符则会出现错误
- 区分大小写，除用到的命令外，定义常量用大写，定义变量用小写

### Modifying Nodes-修改节点

#### 1.0

- 创建一个 label 标签，并做基础设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734572319627-9ab18b3f-3523-4d5e-93a3-ef48c7668378.png)

- 回到 Main 脚本中，拖动 label 到 ready 函数下做节点引入，引入后根据想要调整的属性进行赋值

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734573240108-4b9597fb-0a6c-4413-bfbf-20c613c47262.png)

#### 2.0

- 普通节点引入变量及确认节点绝对路径

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734680474000-4a4afca8-8db9-451f-a862-345a0017cc12.png)

- `$`的作用等同于`get_node("")`，该路径为父节点相对路径
- 导出节点并可在检查器中进行设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734680726100-d518ab29-a287-4f34-871a-b65dd9abc66c.png)

### Input-输入

- 项目设置输入映射中做输入动作设置

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734573876150-8e53c0e5-8136-451c-aace-e21ae78649db.png)

- 回到脚本中创建输入方法：

```gdscript
extends Node

# 进入场景树立即执行
func _ready() -> void:
	$Label.text = "Hello World!"
	$Label.modulate = Color.CRIMSON

# 创建输入方法，所有方法以_开头
func _input(event: InputEvent) -> void:
  # 判断预计判断是否执行按键行为的函数
	if event.is_action_pressed("change_color"):
		$Label.modulate = Color.GREEN

	if event.is_action_released("change_color"):
		$Label.modulate = Color.CRIMSON
```

### Variables-变量

变量是储存数据的容器，比如可以用来储存玩家（player）的各项数值，诸如：名称（name）、生命值（health）、伤害（attack）、存活状态（is_alive)等

#### 1.0

- 创建为生命值的变量，初始值为 100
- 设置每次点击空格执行生命值减 20 的操作
- 打印生命值数字

```gdscript
# 声明变量生命值为100
var health = 100

# 创建输入方法
func _input(event: InputEvent) -> void:
  # 判断按键操作
	if event.is_action_pressed("change_color"):
    # 调整生命值数值
		health -= 20
		print(health)
```

#### 2.0

声明变量的时候要考虑到作用域（Scope），若在 if 语句中声明变量，则该变量只能在 if 语句中使用

- 为保证声明变量能在脚本任意位置使用，创建独立于任何函数和方法之外的全局变量
- 声明变量不需要考虑数值类型，直接赋值和再次赋值也不会发生错误
- 数值类型（Date Types）：

布尔值（bool）：true&false

整数（int）：3008

浮点数（float）：带有小数的数字 2.89

字符串（string）：文本

数值类型间的相互转化被称为数值转换：`var pi = 3.9   print(int(pi)）`打印的值为 3

- <u>常见的数据结构是 Vector2 和 Vector3</u>，可直接储存浮点数，常用于表示坐标：

```gdscript
entends Node

func _ready() -> void:
  var vec2 = Vector2(0.0,0.0)
  var vec3 = Vector3(0.0,0.0,0.0)
# 最常用表示位置，并改变其中一个分量

var position = Vector3(3,-10,5)
position.x += 2
print(position)
# 输出结果为（5,-10,5)
```

- GDScript 中的变量为动态类型，创建变量方便但容易出错，可调整为静态类型，只需在声明变量时做好限制数值类型即可

```gdscript
# 动态类型变量
var a = 10
var b = true
# 重新赋值可直接改变变量数值类型
  a = true
# 可创建变量时直接锁定数值类型
var a: int = 10
# 即设定变量a仅能作为整数类型，无法变成其他数值类型
# 或简写使用推断类型
var a := 10
```

- 若在声明的变量前加上`@export`，则可以在节点的检查器处设置它：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734578448664-56d9e9e3-26d7-4f29-8cc0-c4009563aa65.png)

- 想定义一个不可改变的值，可用到常量`const SPEED = 60`

### If-statements-if 语句

if 语句是所有语法的粘合剂，if 作为一个条件，只有满足这个条件，里面的语法才能被执行

- 创建一个 if 语句判断玩家生命值是否降为 0，降为 0 输出“YOU DIED！”

```gdscript
var health = 100

func _input(event: InputEvent) -> void:
	if event.is_action_pressed("change_color"):
		health -= 20
		print(health)

    # if语句判断生命值是否降为0
    if health <= 0:
      health = 0
      print("YOU DIE!")
```

- 更多比较方法：

等于：X == Y

不等于：X != Y

大于、小于、大于等于、小于等于等

- 可以再一个 if 语句中添加多个比较条件：X ==Y and Y > Z（两个条件都要满足）

or 两个条件只需满足其中一个；

- if 语句即可变现真发生的，也可以表现除此以外的假发生情况，用 else 创建，++同时可使用 elif 来表示 else if 来串联两个 if 语句

```gdscript
# 创建一个判断玩家状态的语句，生命值小于50打印受伤，生命值等于0打印死亡，否则打印健康
var health = 100

func _input(event:InputEvent) -> void:
  if event.is_action_pressed("change_color"):
    health -= 20
    print(health)

# 根据不同血量判断不同状态
    if health <= 0:
      health = 0
      print("YOU DIED!")
    elif health <50:
      print("YOU ARE HURT!)
    else:
        print("YOU ARE GOOD!")
```

### function-函数

- 内置函数：函数前带`_`例如，`_input`；`_ready`等，这类函数不由我们调用，仅由引擎调用
- 自建函数：我们可自己创建函数并引用，例如玩家的跳跃、死亡、射击、重生等

```gdscript
func jump():
func die():
func shoot():
func respawn():
```

- 函数相当于一个工具，我们需要输入参数，函数会返回结果供我们调用

```gdscript
extends Node
# 5.在此函数内调用下面的函数
func _ready() -> void:
  var result = add(3 , 5)
  # 可复用多次计算
  result = add(result , 5)
  # 6.打印结果为13
  print(result)

# 1.创建一个函数，用于两个数计算，并返回结果
# 2.借助num1:int限制参数的数值类型为整数;使用->来限制返回值数值类型
func add(num1:int , num2:int) -> int:
  # 3.声明一个用于该函数的变量，用来储存数据，计算相加结果
  var result = num1 + num2
  return result
  # 4.为了能让上面函数调用result，使用return而不是print，否则只能打印而不能调用
```

### random number-随机数字

- randf 会给出一个 0~1 之间的随机数，可用于给代码分配概率，例如给玩家获取道具的稀有度

```gdscript
var roll = randf()
if roll <= 0.8:
  print("你找到了一件普通物品")
else:
  print("你找到了一件稀有物品")
```

- 可以使用 randf_range（浮点数）和 randi_range（整数）来获取一个在最大值和最小值之间的随机浮点数和整数，比如给角色分配一个随机高度：

```gdscript
var height = randi_range(140 , 210)
  print("你的角色身高是：" + str(height) + " cm高")
```

### Arrays&loops-数组&循环

- 数组应用在需要一个变量来储存布置一个东西，可能是想存储一整列元素
- 声明变量时使用`[]`包裹，数据类型可混组：`var items = ["books" , 3 , 6]`
- 同样可限制数组数值类型
- 访问数组数值用到索引，数组中第一个数据为“0”，同样可使用索引进行赋值、删除、新增

```gdscript
entends Node
# 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Books" , "Cards" , "6"]
# 对数组中第三个元素重新赋值
items[2] = "Potion"
# 删除数组第一个元素，新增一个元素
items.remove_at(0)
items.append("Staff")
# 现在数组变为["Cards" , "Potion" , "Staff"]
# 打印最后一个元素,输出Staff
print(items[2])
```

- 数组会变得很长且难以手动遍历，用到 <u>Loops-循环</u>

循环允许我们重复代码多次，每次有不同变化，适合遍历数组中的所有数据

使用`for`语句：

```gdscript
entends Node
# 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Cards" , "Potion" , "Staff"]
# 利用循环打印所有数组中的元素
for item in items:
  print(item)
# 结果将输出所有数组内容
```

可设定数组数据长度按条件打印内容：

```gdscript
entends Node
# 声明变量为数组，且数据类型为字符串
var items :Array[String] = ["Cards" , "Potion" , "Staff"]

for item in items:
  if item.length() > 5:
    print(item)
# 输出结果为Potion，因为只有这个满足字符长度大于6
```

用`while`语句创建条件循环，例如，我设定一个杯子，想杯子里不断加水，直到杯子半满后循环结束，打印杯子已经半满了：

```gdscript
entends Node

func _ready() -> void:

  # 创建杯子变量，默认含水量为0
  var glass := 0.0
  # 创建循环条件，并设置未达标持续增加随机浮点数
  while glass < 0.5:
      glass += randf_range(0.01 , 0.2)
      print(glass)
  # 循环结束打印杯子半满
  print("杯子半满了")
```

    输出结果如下：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734659868523-c9ed2820-316e-4b55-83cc-8f15faf945ab.png)

还可使用`break：跳出循环执行循环后的指令`和`continue：跳到循环的下一次迭代`来设置条件变化

```gdscript
entends Node

func _ready() -> void:

  var glass := 0.0
  while glass < 0.5:
      glass += randf_range(0.01 , 0.2)

  # 加上条件跳出循环
      if glass > 0.2:
          break
      print(glass)

  print("杯子半满了")
```

结果如下：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734660125350-6761c321-51b0-4ba1-91c0-0cd3d417a776.png)

### Dictionaries-字典

- 使用索引访问数组元素会比较麻烦，可用到字典
- 字典分为：key（键）和 value（值），键是我们要查找的元素，值是元素的定义
- 字典用`{}`包含并表示，括号内放入键对值，例如创建三个不同等级的玩家：

```gdscript
# 创建数组并在数组中添加三个玩家角色，配备对应等级
var players{
  "Dog": 1,
  "Cat": 10,
  "Bird": 100,
}
# 修改已有键对值，添加新的键对值
players["Cat"] = 30
players["Mouse"] = 999
# 用循环语句打印所有字典内容
for username in players:
  print(username + ": " + str(players[username]))
```

输出结果为：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734674358263-fc2c9631-8cee-4941-a73d-f93e75e08211.png)

- 字典内可同样嵌套字典，调整字典内角色具备等级和血量属性：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734674676609-39a56627-2094-48ab-9aa5-5360b43bb228.png)

### Enums-枚举

- 给游戏定义标签和状态的便捷方式，如游戏中有很多单位，要定义敌对中立友军等标签：

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734678614968-5b5e72fa-7bfc-487d-8bf9-eb842130f052.png)

- 可以导出标记变量，直接在检查器处进行修改

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734678775062-31a7789a-684c-446d-a455-e7e89346f3fd.png)

- 枚举的工作原理实际上给每个标签定义了常量，等同于索引，同样可进行修改：

```gdscript
enum Alignment {Ally, Neutral, Enemy}
# 下面这段代码等同于上面的枚举：
const Ally = 0
const Neutral =1
const Enemy = 2
# 此时打印其中一个会输出其常量
print(Alignment.Enemy)
# 结果为2

# 在枚举中直接设置标签常量
enum Alignment {Ally = 1, Neutral = 0, Enemy = -1}
# 此时打印Enemy输出为-1
```

### match-匹配

- 匹配和完美的适用于枚举语句，可以迅速为某个状态添加功能
- 允许根据变量值执行不同代码

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734679635426-4fe5ebd8-dbdc-4068-8636-2ea2300b7b5d.png)

### Signals-信号

- 信号是节点间相互传递的消息，我们用它来通知某些事件已经发生，Godot 内置了许多信号
- 可使用 signal 创建自己的信号，通过条件下`信号名称.emit()`发出信号，需要接收信号的地方引入信号并编辑执行代买
- 信号在分离游戏不同部分的时候非常有用，或成为 Decoupling（解耦合）

例如我们的玩家伴随游戏升级，用户的 UI、玩家状态、技能解锁等系统都要发生变化，在角色中通过代码修改很容易错乱，可以使用信号解决

创建一个玩家虽时间升级系统，利用计时器，每一秒上涨 5 经验值，若到 20 升级并打印“DING！”

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734919487924-572c6520-0b1a-4fd8-8848-e68787bf37ca.png)

- 也可以通过代码链接信号<font style="color:#DF2A3F;">（了解即可）</font>

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734920207771-dcf565d8-f709-4276-b1cf-e4f76cfc80af.png)

### Get/Set

- get 和 set 用于变量改变时可以添加代码：限制某个值在一个范围内，或发出一个信号，让代码其他部分知道变量已更改，<u>常用的例子就是玩家生命值</u>

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734922517436-d0bbee69-183c-4a10-83a6-77dc2b4f6a3e.png)

以上 set 示例执行顺序，ready 函数中生命值为负数，在 set 中限制了 0~100，因此会被执行为 0，之后发出 health_changed 的信号，信号触发函数执行打印当前生命值为 0

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734923367685-5942dbc9-0563-45d4-963a-18b21438ec99.png)

### Classes-类

- 类包含我们需要去交互的变量和逻辑：

比如我们要创建多个玩家角色，要用到类，类中包含的变量包括玩家名称、生命值、对话内容等；逻辑包括死亡、攻击、谈话等

- 用类去创建实例：实例是类的具体版本，赋予了变量具体数值和具体逻辑

比如我创建了一个 Knight（骑士）角色，生命值有 150，谈话是“对不住了大小姐”

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734924957313-4698d530-1d81-4c74-ba99-1a673c7e762c.png)

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734925044015-e93b2051-16a4-4421-a1eb-589f44d7160f.png)

### Inner Classes-内部类

- 作为一个类中的类，用于存放一些变量及函数
- 可作为字典的良好替代方案，防止执行时发生错误不能及时识别
- 比如我们要在任务类下创建装备，包括护甲 10 点和重量 5 点，在脚本中添加变量新建实例建立胸甲和腿甲，调用打印装备信息

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734936115423-bdd7f534-4852-47b1-b562-d5ecb042c8d0.png)

### Inheritance-继承

- Godot 中所有节点都是一个类，我们也可以通过脚本构建自己的类；而 Godot 的构架方式就是通过继承；
- 继承指从一个类派生出另一个类的能力
- 脚本中的`extends Node2D`即指该脚本继承自 Node2D，则所有 Node2D 下的变量和函数都可在该脚本中使用；
- 可通过新建节点时的节点架构树查看从属管理，且我们自己创建的类也会出现在架构树中

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734936502454-96bdcf3a-4e93-4164-8413-5cca64cc9a37.png)

### Composition-组合

[关于组合更多的应用实例和讲解](https://www.bilibili.com/video/BV1Ns421w7nT/?share_source=copy_web&spm_id_from=333.788.comment.all.click&vd_source=7bca0171cc21043a855b81c081be807a)
- 可将同类型代码进行组合优化代码结构
- 可将代码组合到一个易于修改的地方
- 利用组合创建组件，将攻击、健康、碰撞盒创建不同的组件，在场景不同元素中分别设置他们

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734937581854-17a943c2-bd8e-4b0f-8866-f6a313973a11.png)

### Call Down & Signal Up-向下调用&向上发信号

- 节点间通讯的经验法则
- 每个场景都是节点树，而节点树的起点成为根节点，根据节点树的上下关系，两个节点具有父子关系

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734936869909-4fb19d92-1561-4f0d-93e1-37288ed0a3ca.png)

- 上方节点可以调用其下方节点的函数，反之则不行；而下方节点要通过发送信号来传达某些事件已经发生，上方节点可以选择性链接这些信号

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734936968454-52d2d9d8-e86a-4456-bbbe-f3c6f4d04307.png)

- 如果在同级的兄弟节点间需要通讯，共同的父节点负责在 ready 函数中将一个节点中的信号连接到另一个节点中的函数

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734937076583-e0c71d69-f6f2-4f90-9de0-41fbf26daaf7.png)
