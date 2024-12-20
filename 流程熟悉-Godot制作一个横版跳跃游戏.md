## 介绍
+ Godot是一个以节点为单位做游戏搭建的开源工具，游戏中的场景、角色、道具等通过节点组合起来
+ 每添加一个可复用的元素，可设置为不同属性的场景，增加元素在游戏中的复用性；
+ 语言使用GDScript，易学，性能不是过高的游戏制作都能适配；
+ 学习资源链接如下：
    - [课程视频链接](https://www.bilibili.com/video/BV1fs421N7TD?spm_id_from=333.788.videopod.episodes&vd_source=f2f0c4b25442f945a04f9cdb159461a4)
    - [游戏资源下载链接](https://brackeysgames.itch.io/brackeys-platformer-bundle)

## 文件结构创建
+ assets：用来保存游戏资产，一般包括fonts（字体）、Music（音乐）、Sounds（音效）和sprite（精灵）
+ scenes：用来储存游戏中的所有场景；
+ scripts：用来储存游戏中的所有脚本

## 游戏制作流程
### 新建项目
打开Godot，点击左上角创建，选择文件夹并为项目起名称

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734312671875-cb28fd9d-ac24-4425-a936-6667a68debe5.png)

### 创建游戏目录
分别创建assets、scenes、scripts文件夹，将准备好的游戏资产放到assets文件夹下；

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734312850500-e8c7b9a8-5402-485e-83dd-d85b3846c1ef.png)

### 创建游戏主场景
左上角创建2D场景-修改名称为Game-保存到scenes文件夹-点击运行选择此场景为主场景

注：主场景可在“项目设置-运行-主场景”中进行修改

### 创建人物场景
+ 设置人物Z索引为更高的值，确保玩家位于最上方

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734317063640-b968c7fb-c9a5-4f28-b2da-ca87c434b3d7.png)

+ 给玩家创建脚本，使用初始化移动脚本，脚本内容如下：

```gdscript
extends CharacterBody2D

# 声明移动速度和跳跃速度常量
const SPEED = 130.0
const JUMP_VELOCITY = -300.0

# 引用物理引擎，将物理引擎独立于游戏引擎，固定60帧率
func _physics_process(delta: float) -> void:
# 添加重力引擎，并设置如果玩家没有位于地板上，则受重力影响下落
	if not is_on_floor():
		velocity += get_gravity() * delta

# 设置跳跃，按下跳跃键，玩家位于地板上进行跳跃
	if Input.is_action_just_pressed("ui_accept") and is_on_floor():
		velocity.y = JUMP_VELOCITY

# 设置方向键输入，改变玩家移动方向
# 最好用自定义游戏操作，替换UI操作
	var direction := Input.get_axis("ui_left", "ui_right")
	if direction:
		velocity.x = direction * SPEED
	else:
		velocity.x = move_toward(velocity.x, 0, SPEED)

	move_and_slide()

```

+ 可使用动作系统重新绑定按键：项目-项目设置-输入映射-添加动作及绑定按键

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734415354182-3321d29c-33fe-4973-af5d-ee5c0a381ca6.png)

+ 完善角色动作变现细节：
    - 根据移动方向翻转：脚本中引入玩家animated_sprite声明，在获取移动方向之后，添加用于判断并改变方向的if语句，利用动画中的flip_h属性改变方向，代码如下：

```gdscript
# 引入玩家动画声明
@onready var animated_sprite: AnimatedSprite2D = $AnimatedSprite2D

# 判断玩家方向并调整玩家朝向翻转
if direction > 0:
  animated_sprite.flip_h = false
elif direction < 0:
  animated_sprite.flip_h = true
```

- 为玩家添加更多动画：player场景动画编辑器中添加跳跃、奔跑、死亡动画；

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734417033755-8e74364b-a430-409f-b3e4-b40a23f57939.png)

- 在脚本中设置动画触发事件：

```gdscript
# 引入玩家动画声明
@onready var animated_sprite: AnimatedSprite2D = $AnimatedSprite2D

# 判断玩家方向并调整玩家朝向翻转
if direction > 0:
  animated_sprite.flip_h = false
elif direction < 0:
  animated_sprite.flip_h = true

# 判断玩家是否处于地面上，是则通过方向播放闲置或奔跑动画，否则触发跳跃动画；
if is_on_floor():
  if direction == 0:
    animated_sprite.play("idle")
  else:
    animated_sprite.play("run")
else:
  animated_sprite.plya("jump")
```

### 拾取物品创建
+ 使用Area2D精灵：不会与其他物体发生碰撞，但会检测其他精灵进入；
+ 添加动画精灵AnimatedSprite2D，用帧动画制作；
+ 添加碰撞形状CollisionShape2D;
+ 添加拾取脚本，创建初始脚本；

```gdscript
extends Area2D


# ready函数在节点进入场景树的时候被调用，在这里放置希望立即执行的代码
func _ready() -> void:
  pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
  func _process(delta: float) -> void:
    pass

```

+ 使用信号来操作发生事件触发代码

点击拾取物场景，点击节点，选择物体进入节点，双击绑定拾取物节点

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734320078902-98ba1ae5-e03a-4211-90c9-8bec50d93437.png)

代码变更为：

```gdscript
extends Area2D

# 绑定节点触发信号
func _on_body_entered(body: Node2D) -> void:
# 设置触发事件
  print("+1 coin!")
# 设置收集并清除拾取物
  queue_free()
```

### 死亡机制
+ 设置摄像机在玩家掉落时不跟随

点击摄像机-选择右侧摄像机限制（limit）-调整底部像素大小（可使用标尺工具测量要设置的像素范围）

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734338300139-53358f66-b61d-474c-864c-c101a21de294.png)

+ 创建可复用死亡地带（killzone）
    - 用来检测玩家是否进入危险区域并死亡重启游戏
    - 可用于敌人、危险领域、尖刺陷阱等
    - 创建Area2D场景，检测碰撞体是否进入的状态-并把层级遮盖设置为第2层，检测处在第二层的玩家-不设置任何形状，以便于在复用时用于多种元素；

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734338732859-4c409255-fcf9-49bc-b2d3-58b2aa3b4df8.png)

 - 把killzone场景添加到主场景中，并设置碰撞形状为worldboundaryshape2D，移动放于地图最下方，避免偶然碰触

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734338916795-4d801e43-113b-427a-a619-9999436fddfc.png)

 - 给死亡地带设置脚本-引用信号（同样使用body_enter），打印“You die!”测试
 - 加入计时器，设置时间延迟，并设置单次触发

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734339575786-03997dda-06fa-4155-8b11-dcec88cd851d.png)

 - 编写脚本，引入计时器（直接拖动上代码上方，按住control释放），下方打印后执行计时器，之后使用信号，执行计时器后重启游戏；

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734339975617-ce1202a8-1643-4690-b511-09428c636f68.png)

```gdscript
extends Area2D
# 引入计时器的变量声明
@onready var timer: Timer = $Timer
# 玩家进入死亡地带后，触发_on_body_entered函数
func _on_body_entered(body: Node2D) -> void:
# 打印“You died!”信息
  print("You died!")
# 执行计时器
  timer.start()
# 计时器结束，触发_on_timer_timeout函数
func _on_timer_timeout() -> void:
# 执行重启游戏操作
  get_tree().reload_current_scene()
```

 - 设置玩家死亡慢放及碰撞体消除掉落地图：

```gdscript
extends Area2D
@onready var timer: Timer = $Timer

func _on_body_entered(body: Node2D) -> void:
  print("You died!")
# 设置玩家进入死亡区域后，时间尺度慢放
  Engine.time_scale = 0.5
# 设置玩家移除碰撞体并掉落地图：获取玩家变量，访问碰撞体节点，并移除
  body.get_node("CollisionShape2D").queue_free()
  timer.start()

func _on_timer_timeout() -> void:
# 计时器结束后，时间尺度恢复，避免新开始的游戏0.5倍速
  Engine.time_scale = 1.0
  get_tree().reload_current_scene()
```

### 创建敌人
+ 创建基础空白场景-添加动画精灵-添加复用的危险区域场景-添加碰撞形状-将敌人的Z索引设置为3，方便在场景中正确显示
+ 脚本控制敌人移动
    - 使用process函数，不同于ready函数每局游戏开始前进触发一次，process在每一帧都会进行识别触发，适合用在随时间变化的元素上；比如移动敌人，<u>确保每一帧都微小的移动敌人即可</u>
    - 代码尝试

```gdscript
extends Node2D
# 声明常量SPEED作为移动速度
const SPEED = 60
# Called every frame. 'delta' is the elapsed time since the previous frame.
  func _process(delta: float) -> void:
# 使用delta补偿帧率波动导致的移动速度不固定
    position.x += SPEED * delta
```

上述脚本可控制敌人匀速移动，但无法触发碰撞转向循环移动，代码调整如下：

```gdscript
extends Node2D

const SPEED = 30
# 声明变量初始值为1，向右侧移动
var direction = 1
# 添加raycast射线识别敌人左右是否发生碰撞，并应用变量
# 引用敌人动画变量
@onready var ray_cast_right: RayCast2D = $RayCastRight
@onready var ray_cast_left: RayCast2D = $RayCastLeft
@onready var animated_sprite: AnimatedSprite2D = $AnimatedSprite2D

func _process(delta: float) -> void:
# if语句判断若左右碰撞，调整方向，并调整动画flip_h属性，用布尔值换方向
  if ray_cast_right.is_colliding():
    direction = -1
    animated_sprite.flip_h = true
  if ray_cast_left.is_colliding():
    direction = 1
    animated_sprite.flip_h = false
# 标准的移动语法
  position.x += direction * SPEED * delta

```

### 计分系统
+ 创建计分系统可用普通的节点（Node），不发生碰撞，不识别物体进入，不涉及位置变化；
+ 可直接明明为GameManager，作为唯一的游戏管理器使用
+ 给该节点创建脚本，用来定义分数及计分函数

```gdscript
extends Node

# 定义计分分数变量
var score = 0

# 创建函数，用来做拾取金币时计分
func add_point():
  // 设置函数执行方式
  score += 1
  // 打印数值
  print(score)
```

+ 在拾取物脚本中引入变量，并调整脚本内容执行计分函数

```gdscript
extends Area2D

# 引入GameManager节点；节点路径可右键节点选择唯一名称简化路径，防止出错，仅用于唯一场景的唯一管理器，不在同一场景不生效
@onready var game_manager: Node = %GameManager

# 绑定节点触发信号
func _on_body_entered(body: Node2D) -> void:
# 执行脚本
  game_manager.add_point()
# 设置收集并清除拾取物
  queue_free()
```

+ 为计分系统设置文字显示
    - 使用label节点，放置在GameManager节点下
    - 在脚本中引入label节点

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734419957847-56c0829c-d80f-4e90-a35d-0331b0d8e3db.png)

 - 改写脚本内容，文字替换

```gdscript
extends Node

# 定义计分分数变量
var score = 0
# 引入label节点
@onready var score_label: Label = $Score_Label

# 创建函数，用来做拾取金币时计分
func add_point():
  // 设置函数执行方式
  score += 1
  // 调整输出文字
  score_label.text = "You collected " + "str(score)" + " coins"
```

### 游戏背景音及音效
+ 背景音乐
    - 添加新节点AudioStreamPlayer2D，将背景音乐拖入检查器中，在下方调试器选择音频，新建Music总线，勾选自动播放及总线选择Music，调整音量

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734421655257-d34c9351-d010-45d2-9154-f77150b3fcde.png)

 - 为了让音频作为自动加载的场景，随着人物死亡也不会重新加载，将音乐节点拖入scenes文件夹储存为新场景，在项目设置自动加载中选择并设置音乐场景

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734421766102-28ff6da0-b263-4e3e-a059-1b891af0569b.png)

+ 游戏音效
    - 在拾取物场景中添加音频流播放器AudioStreamPlayer2D，拖入拾取音效，将音频线程设置为SFX
    - 为避免通过脚本设置音乐播放效果，出现错乱情况，或拾取后音效停止前拾取物不清楚导致的重复拾取，采用动画播放器方式设置拾取物消失及音乐播放动画
    - 在拾取物场景中创建AnimationPlayer节点，点击该节点创建动画，命名为pickup
    - 回到AnimatedSprite2D节点，在动画里面将Visibility属性里的Visible设置关键帧，属性调整为不启用（要将轨道选择器保持pickup选中，目的设置在拾取时拾取物不显示）
    - 同样对碰撞体进行设置，音效设置播放关键帧，将关键帧设置在第0秒处

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734423124036-c1ad7ffc-bd5e-4730-84f0-e626b3f2f293.png)

 - 点击添加轨道，选择方法调用轨道，选中拾取物节点，在第1秒处右键插入函数queue_free

![](https://cdn.nlark.com/yuque/0/2024/png/50617026/1734423204400-3d682912-3a70-4b96-8250-36ff3e35ccdc.png)

 - 在脚本中执行动画播放

```gdscript
extends Area2D
# 引入音频播放器
@onready var animation_player: AnimationPlayer = $AnimationPlayer
# 绑定节点触发信号
func _on_body_entered(body: Node2D) -> void:
# 执行脚本
  game_manager.add_point()
# 设置音频播放器执行
  animation_player.play("pickup")
```



