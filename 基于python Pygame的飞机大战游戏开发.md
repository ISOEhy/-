# 基于python Pygame的飞机大战游戏开发

[TOC]

## **一、项目介绍**

### **1.1 简介**

基于python 的pygame库函数，开发了一个飞机大战的游戏，具体实现的功能，通过按键控制飞机实现上下左右的移动，游戏效果如下：

![image-20210605144207172](https://gitee.com/hyisoe/typroa-picgo/raw/master/20210605144207.png)

### **1.2 所用到的知识点**

1. Pygame图形
2. Pygame动画
3. Pygame文字
4. Pygame音频
5. Pygame事件

### **1.3 开发验环境**

1. Python 3.7 
2. Windows10 vscode 

### **1.2 代码获取**

本项目所用到的代码和相关资源文件已经长传至本人gitee，下载链接：

## **二、开发准备**

本次课程主要利用Pygame模块来进行开发，首先打开cmd终端，并使用 pip 命令来安装Pygame

```
pip install pygame
```

安装完成之后进入Python的交互界面，输入以下命令查看是否成功安装。

```python
import pygame
```

若无异常，如下图所示，则说明安装成功。

![image-20210605142837986](https://gitee.com/hyisoe/typroa-picgo/raw/master/20210605142845.png)

## **三、项目代码解析**

### 3.1 **HelloWorld**

首先开始我们第一个HelloWorld程序：

```python
# -*- coding: UTF-8 -*-
# helloworld.py
 
# 导入所需的模块
import pygame, sys
# 导入所有pygame.locals里的变量（比如下面大写的QUIT变量）
from pygame.locals import *
 
 
# 初始化pygame
pygame.init()
 
# 设置窗口的大小，单位为像素
screen = pygame.display.set_mode((500, 400))
 
# 设置窗口标题
pygame.display.set_caption('Hello World')
 
# 程序主循环
while True:
 
  # 获取事件
  for event in pygame.event.get():
    # 判断事件是否为退出事件
    if event.type == QUIT:
      # 退出pygame
      pygame.quit()
      # 退出系统
      sys.exit()
 
  # 绘制屏幕内容
  pygame.display.update()
```

效果图如下：

![image-20210605143142521](https://gitee.com/hyisoe/typroa-picgo/raw/master/20210605143142.png)

这里解释一下上面程序的运行方式

一个游戏循环（也可以称为主循环）就做下面这三件事：

1. 处理事件
2. 更新游戏状态
3. 绘制游戏状态到屏幕上

### 3.2 **飞机类**

定义飞机类，初始化属性。加载飞机图片，并获取飞机的坐标，将飞机放置在合适的位置上。飞机这个类有几个方法，首先就是上下左右移动，通过key_control 方法实现按键控制它的移动和子弹的发射，update方法更新飞机的位置，display方法将飞机显示在屏幕上。

要想实现通过按键控制游戏，就要用到pygame中的事件，Pygame里常用的事件如下表：

| 事件            | 产生途径             | 参数              |
| :-------------- | :------------------- | :---------------- |
| QUIT            | 用户按下关闭按钮     | none              |
| ACTIVEEVENT     | Pygame被激活或者隐藏 | gain, state       |
| KEYDOWN         | 键盘被按下           | unicode, key, mod |
| KEYUP           | 键盘被放开           | key, mod          |
| MOUSEMOTION     | 鼠标移动             | pos, rel, buttons |
| MOUSEBUTTONDOWN | 鼠标按下             | pos, button       |
| MOUSEBUTTONUP   | 鼠标放开             | pos, button       |
| VIDEORESIZE     | Pygame窗口缩放       | size, w, h        |

代码如下：

```python
class HeroPlane(pygame.sprite.Sprite):
    # 存放所有飞机的子弹的组
    bullets = pygame.sprite.Group()

    def __init__(self, screen):
        # 这个精灵的初始化方法 必须调用
        pygame.sprite.Sprite.__init__(self)
        # 图片
        self.image = pygame.image.load('./feiji/hero1.png')
        # 根据图片image获取矩形的对象
        self.rect = self.image.get_rect()  # rect属性：矩形
        self.rect.topleft = [Manager.bg_size[0] / 2 - 100 / 2, 600]  # 矩形左上角坐
        # 窗口
        self.screen = screen
        self.screen_rect = screen.get_rect()
        # Start each new ship at the bottom center of the screen.
        self.rect.centerx = self.screen_rect.centerx
        self.rect.bottom = self.screen_rect.bottom

        # Store a decimal value for the ship's center.
        self.centerx = float(self.rect.centerx)
        self.centery = float(self.rect.centery)
        # 速度
        self.speed = 15
        # pygame.sprite.Group()生成一个放精灵的对象 类似一个列表
        self.bullets = pygame.sprite.Group()

    def key_control(self):
        """
        按键的监听 用来改变飞机坐标
        """
        # 监听键盘事件  键盘一直按下
        key_pressed = pygame.key.get_pressed()  # 注意这种方式是能够检测到连续按下的，比之前的版本要新

        # 改变飞机坐标
        if (key_pressed[K_w] or key_pressed[K_UP])and self.rect.top> self.screen_rect.top:#判断飞机是否处于屏幕范围内
            self.rect.top -= self.speed
        if (key_pressed[K_s] or key_pressed[K_DOWN]) and self.rect.bottom <self.screen_rect.bottom:
            self.rect.bottom += self.speed
            print(self.rect.bottom,self.screen_rect.bottom)
        if (key_pressed[K_a] or key_pressed[K_LEFT])and  self.rect.left > 0:
            self.rect.left -= self.speed
        if (key_pressed[K_d] or key_pressed[K_RIGHT]) and self.rect.right < self.screen_rect.right:
            self.rect.right += self.speed
        if key_pressed[K_SPACE]:
            # 按下空格键 发射一枚子弹 把飞机的坐标传入子弹
            bullet = Bullet(self.screen, self.rect.left, self.rect.top)
            # 把子弹放到列表里
            self.bullets.add(bullet)
            # 存放所有飞机的子弹的组
            HeroPlane.bullets.add(bullet)

    # 调用飞机的更新方法，更新飞机的位置
    def update(self):
        self.key_control()
        self.display()

    def display(self):
        """显示飞机到窗口"""
        self.screen.blit(self.image, self.rect)
        # 更新子弹坐标
        self.bullets.update()

        # 把所有的子弹全部添加到屏幕
        self.bullets.draw(self.screen)

    @classmethod
    def clear_bullets(cls):
        # 清空子弹
        cls.bullets.empty()
```

飞机还有一个属性，就是子弹，我们可以将子弹也定义为一个类，包含了子弹的图片，子弹的位置，超出区域或者碰撞后消失。

```python
# 子弹类
# 属性  坐标 速度 图片
class Bullet(pygame.sprite.Sprite):

    def __init__(self, screen, x, y):
        pygame.sprite.Sprite.__init__(self)
        # 图片
        self.image = pygame.image.load('./feiji/bullet.png')
        self.rect = self.image.get_rect()  # rect属性：矩形
        self.rect.topleft = [x + 100 / 2 - 22 / 2, y - 25]  # 矩形左上角坐
        # 窗口
        self.screen = screen
        # 速度
        self.speed = 20

    def update(self):
        # 修改子弹坐标
        self.rect.top -= self.speed
        # 如果子弹移出屏幕上方，则销毁子弹对象
        if self.rect.top < -22:
            # 超出界面 干掉自己
            self.kill()
```

### **3.3 定义敌方飞机类**

敌方飞机和我方飞机本质是一样的，属性和方法类似，不同的是，敌方飞机是自动移动和自动开火的。敌机随机从屏幕左上方或右上方出现，在碰到左方的墙壁或者右边的墙壁后反向移动。同时，通过random模块实现自动开火，具体实现方法是，随机生成1-20的数，如果这个随机数为8就发射子弹，可以通过更改随机数范围降低游戏难度。

```python
class EnemyPlane(pygame.sprite.Sprite):
    """敌方飞机"""
    # 敌方所有子弹  类属性
    enemy_bullets = pygame.sprite.Group()

    def __init__(self, screen):
        pygame.sprite.Sprite.__init__(self)
        # 5.创建一个飞机的图片
        self.image = pygame.image.load('./feiji/enemy0.png')  # 51*39
        self.rect = self.image.get_rect()
        x = random.randrange(1, Manager.bg_size[0], 50)
        self.rect.topleft = [x, 0]

        # 飞机的速度
        self.speed = 10

        # 记录当前的窗口对象
        self.screen = screen
        # 装子弹的列表
        self.bullets = pygame.sprite.Group()
        # 飞机左右飞的方向 一开始向右
        self.direction = 'right'

    def display(self):
        """显示飞机到窗口"""
        self.screen.blit(self.image, self.rect)
        # 所有子弹更新坐标
        self.bullets.update()
        # 所有子弹贴到屏幕
        self.bullets.draw(self.screen)

    def update(self):
        self.auto_move()
        self.auto_fire()
        self.display()

    def auto_move(self):
        # 一开始 飞机在左上角 向右移动
        if self.direction == 'right':
            self.rect.right += self.speed
        elif self.direction == 'left':
            self.rect.right -= self.speed

        if self.rect.right > Manager.bg_size[0] - 51:
            # 如果移动到最右侧 把方向改成左
            self.direction = 'left'
        elif self.rect.right < 0:
            # 如果移动到最右左侧 把方向改成右
            self.direction = 'right'

        self.rect.bottom += self.speed

    def auto_fire(self):
        # 获取一个随机数
        random_num = random.randint(1, 20)
        # 降低概率发射子弹  当前是1/20的概率
        if random_num == 8:
            """自动开火 创建子弹对象 添加到列表里"""
            bullet = EnemyBullet(self.screen, self.rect.left, self.rect.top)
            self.bullets.add(bullet)
            # 把子弹添加到类属性的子弹组里
            EnemyPlane.enemy_bullets.add(bullet)

    @classmethod
    def clear_bullets(cls):
        # 清空子弹  empty是精灵组提供的方法 用来清空精灵组
        cls.enemy_bullets.empty()


# 敌方子弹类
# 属性  坐标 速度 图片
class EnemyBullet(pygame.sprite.Sprite):

    def __init__(self, screen, x, y):
        pygame.sprite.Sprite.__init__(self)
        # 坐标
        # 图片
        self.image = pygame.image.load('./feiji/bullet1.png')  # 9*21
        self.rect = self.image.get_rect()  # rect属性：矩形
        self.rect.topleft = [x + 50 / 2 - 8 / 2, y + 39]  # 矩形左上角坐
        # 窗口
        self.screen = screen
        # 速度
        self.speed = 15

    def update(self):
        # 修改子弹坐标
        self.rect.bottom += self.speed
        # 如果子弹移出屏幕上方，则销毁子弹对象
        if self.rect.top > Manager.bg_size[1]:
            self.kill()
```

### 3.4 **爆炸类-中弹或者是碰撞动画**

由于人类眼睛的特殊生理结构，当所看画面的帧率高于24的时候，就会认为是连贯的，此现象称之为 视觉暂留 。

帧率（Frame rate）是用于测量显示帧数的量度，所谓的测量单位为每秒显示帧数(Frames per Second，简称：FPS）一般来说30fps是可以接受的，但是将性能提升至60fps则可以明显提升交互感和逼真感，但是一般来说超过75fps一般就不容易察觉到有明显的流畅度提升了。在我们原有坐标系的基础上添加偏移量，再重新绘制，依次一张一张的循环绘制下去，就会得到我们想要的物体移动的效果。

Pygame实现动画主要用到的方法：

pygame.image.load(filename)加载一张图片

pygame.Surface.blit(source, dest, area=None, special_flags = 0)将图片绘制到屏幕相应坐标上（后面两个参数默认，可以不传）

pygame.time.Clock()获得pygame的时钟

pygame.time.Clock.tick(FPS)设置pygame时钟的间隔时间

以下为示例代码：

```python
class Bomb(object):
    # 初始化爆炸
    def __init__(self, screen, type):
        self.screen = screen
        if type == 'enemy':
            # 加载爆炸资源
            self.mImages = [
                pygame.image.load("./feiji/enemy0_down" + str(v) + ".png") for v in range(1, 5)]
        else:
            # 加载爆炸资源
            self.mImages = [pygame.image.load(
                "./feiji/hero_destroy" + str(v) + ".png") for v in range(1, 4)]

        self.mImages += self.mImages

        # 设置当前爆炸播放索引
        self.mIndex = 0
        # 爆炸位置
        self.mPos = [0, 0]
        # 是否可见
        self.mVisible = False

    # 设置位置
    def action(self, rect):
        # 触发爆炸方法draw
        # 爆炸的坐标
        self.mPos[0] = rect.left
        self.mPos[1] = rect.top
        # 打开爆炸的开关
        self.mVisible = True

    # 绘制爆炸
    def draw(self):
        if not self.mVisible:
            return
        self.screen.blit(self.mImages[self.mIndex], (self.mPos[0], self.mPos[1]))
        self.mIndex += 1
        if self.mIndex >= len(self.mImages):
            # 如果下标已经到最后 代表爆炸结束
            # 下标重置 mVisible重置
            self.mIndex = 0
            self.mVisible = False
```



### 3.5 **动态游戏背景**

游戏背景可以使得游戏更加吸引研究，而动态背景则让游戏更加生动，要实现背景的移动，可以通过两张图片拼接的方式，两张图片是特殊处理的，实现平滑过渡，在游戏开始后自动变换图片的位置就能达到游戏背景移动的效果。

```python
# 地图
class GameBackground(object):
    # 初始化地图
    def __init__(self, screen):
        self.mImage1 = pygame.image.load("./feiji/img_bg_level_2.jpg")
        self.mImage2 = pygame.image.load("./feiji/img_bg_level_2.jpg")
        # 窗口
        self.screen = screen
        # 辅助移动地图
        self.y1 = 0
        self.y2 = -Manager.bg_size[1]  # -768

    # 移动地图

    def move(self):
        self.y1 += 2
        self.y2 += 2
        if self.y1 >= Manager.bg_size[1]:
            self.y1 = 0
        if self.y2 >= 0:
            self.y2 = -Manager.bg_size[1]

    # 绘制地图
    def draw(self):
        self.screen.blit(self.mImage1, (0, self.y1))
        self.screen.blit(self.mImage2, (0, self.y2))
```

### 3.6 **绘制文字**

为了显示分数，那么我们就得在屏幕上绘制文字，Pygame提供了很方便的方法使用.ttf字体文件，这样我们就能很轻易的将文字绘制在屏幕上了。常用到的方法：

**pygame.font.Font(filename, size)**

filename：字体文件的文件名；

size：字体的高height，单位为像素；

**pygame.font.Font.render(text, antialias, color, background=None)**

text：要显示的文字；

antialias： 是否抗锯齿；

color：字体颜色；

background：背景颜色（可选参数）；

**.get_rect()**

获得一个对象的rect，以便于设置其坐标位置

以下为示例代码：

```python
    # 绘制文字  rgb 红绿蓝 0 - 255
    # 参1要绘制的文字 参2 x轴坐标 参3y轴坐标 参4文字大小 参5 文字颜色 参6 背景颜色
    def drawText(self, text, x, y, textHeight=30, fontColor=(255, 255, 255), backgroudColor=None):

        # 通过字体文件获得字体对象  参数1 字体文件 参数2 字体大小
        font_obj = pygame.font.Font('./feiji/baddf.ttf', textHeight)
        # 1文字  2是否抗锯齿 3文字颜色 4背景颜色
        text_obj = font_obj.render(text, True, fontColor, backgroudColor)  # 配置要显示的文字
        # 获得要显示的对象的rect
        text_rect = text_obj.get_rect()
        # 设置显示对象的坐标
        text_rect.topleft = (x, y)
        # 绘制字 到指定区域  参1是文字对象 参2 矩形对象
        self.screen.blit(text_obj, text_rect)
```

### **3.7 播放音频**

在Pygame里播放音频有两个方法，一个用来播放特效声音，一个用来播放背景音乐：

**pygame.mixer.Sound(filename)** ：该方法返回一个Sound对象，调用它的.play( )方法，即可播放较短的音频文件（比如玩家受到伤害、收集到金币等）；

**pygame.mixer.music.load(filename)**：该方法用来加载背景音乐，之后调用pygame.mixer.music.play( )方法就可以播放背景音乐（Pygame只允许加载一个背景音乐在同一个时刻）

以下为示例代码：

```python
class GameSound(object):
    def __init__(self):
        pygame.mixer.init()  # 音乐模块初始化
        pygame.mixer.music.load("./feiji/bg2.ogg")
        pygame.mixer.music.set_volume(0.5)  # 声音大小 一半

        self.__bomb = pygame.mixer.Sound("./feiji/bomb.wav")

    def playBackgroundMusic(self):
        pygame.mixer.music.play(-1)  # 开始播放背景音乐

    def playBombSound(self):
        pygame.mixer.Sound.play(self.__bomb)  # 爆炸音乐
```

### **3.8 飞机大战的主函数**

在主函数中，是实现整个游戏的骨架，上面的各个类都将在这里面调用

```python

class Manager(object):
    bg_size = (512, 768)
    # 创建敌机定时器的id
    create_enemy_id = 10
    # 游戏结束 倒计时的id
    game_over_id = 11
    # 游戏是否结束
    is_game_over = False
    # 倒计时时间
    over_time = 3

    def __init__(self):
        pygame.init()
        # 1创建一个窗口 Manager.bg_size 是宽高
        self.screen = pygame.display.set_mode(Manager.bg_size, 0, 32)
        pygame.display.set_caption("飞机大战")
        # 2创建一个背景图片
        #play_button = Button(self.screen, "Play")
        self.background = pygame.image.load('./feiji/background.png')
        self.map = GameBackground(self.screen)
        self.score = 0
        # 初始化一个装玩家精灵的group
        self.players = pygame.sprite.Group()
        # 初始化一个装敌机精灵的group
        self.enemys = pygame.sprite.Group()

        # 初始化一个玩家爆炸的对象
        self.player_bomb = Bomb(self.screen, 'player')
        # 初始化一个敌机爆炸的对象
        self.enemy_bomb = Bomb(self.screen, 'enemy')
        # 初始化一个声音播放的对象
        self.sound = GameSound()

    def exit(self):
        print('退出')
        # 执行pygame退出
        pygame.quit()
        # python程序的退出
        exit()

    def show_over_text(self):
        # 游戏结束 倒计时后重新开始
        self.drawText('gameover %d' % Manager.over_time, 100, Manager.bg_size[1] / 2,
                      textHeight=50, fontColor=[255, 0, 0])

    def game_over_timer(self):
        self.show_over_text()
        # 倒计时-1
        Manager.over_time -= 1
        if Manager.over_time == 0:
            # 参数2改为0 定时间停止
            pygame.time.set_timer(Manager.game_over_id, 0)
            # 倒计时后重新开始
            Manager.over_time = 3
            Manager.is_game_over = False
            self.start_game()

    def start_game(self):
        # 重新开始游戏 有些类属性要清空
        EnemyPlane.clear_bullets()
        HeroPlane.clear_bullets()
        manager = Manager()
        manager.main()

    def new_player(self):
        # 创建一个飞机的对象 添加到玩家的组
        player = HeroPlane(self.screen)
        self.players.add(player)

    def new_enemy(self):
        # 创建一个飞机的对象 添加到敌机的组
        enemy = EnemyPlane(self.screen)
        self.enemys.add(enemy)

    # 绘制文字  rgb 红绿蓝 0 - 255
    # 参1要绘制的文字 参2 x轴坐标 参3y轴坐标 参4文字大小 参5 文字颜色 参6 背景颜色
    def drawText(self, text, x, y, textHeight=30, fontColor=(255, 255, 255), backgroudColor=None):

        # 通过字体文件获得字体对象  参数1 字体文件 参数2 字体大小
        font_obj = pygame.font.Font('./feiji/baddf.ttf', textHeight)
        # 1文字  2是否抗锯齿 3文字颜色 4背景颜色
        text_obj = font_obj.render(text, True, fontColor, backgroudColor)  # 配置要显示的文字
        # 获得要显示的对象的rect
        text_rect = text_obj.get_rect()
        # 设置显示对象的坐标
        text_rect.topleft = (x, y)
        # 绘制字 到指定区域  参1是文字对象 参2 矩形对象
        self.screen.blit(text_obj, text_rect)

    def main(self):
        
        # 播放背景音乐
        self.sound.playBackgroundMusic()
        # 创建一个玩家
        self.new_player()
        # 开启创建敌机的定时器
        pygame.time.set_timer(Manager.create_enemy_id, 1000)
        while True:
            # 把背景图片贴到窗口里  可以盖住上一次的飞机
            # self.screen.blit(self.background, (0, 0))

            # 移动地图
            self.map.move()
            # 把地图画到窗口上
            self.map.draw()

            # 绘制文字
            self.drawText('Score:%d'%self.score, 0, 0)
            if Manager.is_game_over:
                # 判断游戏结束才显示结束文字
                self.show_over_text()

            # 获取事件 这样pygame才能正常响应  pygame.event.get()得到的是一个事件列表
            for event in pygame.event.get():
                # 判断事件类型如果是 点击pygame窗口的×号
                if event.type == QUIT:
                    self.exit()
                elif event.type == Manager.create_enemy_id:
                    # 创建一个敌机
                    self.new_enemy()
                # 定时器触发的事件
                elif event.type == Manager.game_over_id:
                    self.game_over_timer()
            #  调用爆炸的爆炸
            self.player_bomb.draw()
            self.enemy_bomb.draw()

            if self.players.sprites():
                # 判断一个精灵和一个精灵组之间的碰撞 返回被碰撞的敌方飞机
                # 玩家飞机和敌方子弹 参数一是玩家飞机 参数二是敌方所有飞机的所有的子弹
                isover = pygame.sprite.spritecollide(self.players.sprites()[0], EnemyPlane.enemy_bullets, True)
                if isover:
                    Manager.is_game_over = True  # 标识游戏结束
                    # pygame提供的定时器服务 参数一是标识定时器的唯一的id 参数2是间隔时间 毫秒
                    pygame.time.set_timer(Manager.game_over_id, 1000)  # 开启游戏倒计时
                    print('中弹')
                    self.player_bomb.action(self.players.sprites()[0].rect)
                    # 把玩家飞机 从精灵组移除
                    self.players.remove(self.players.sprites()[0])
                    # 爆炸的声音
                    self.sound.playBombSound()

                # 判断碰撞 只能判断两个精灵组之间的碰撞
                iscollide = pygame.sprite.groupcollide(self.players, self.enemys, True, True)

                if iscollide:
                    Manager.is_game_over = True  # 标识游戏结束
                    pygame.time.set_timer(Manager.game_over_id, 1000)  # 开启游戏倒计时
                    # [(x, (y))]
                    items = list(iscollide.items())[0]
                    print(items)  # (x, (y,))
                    x = items[0]
                    y = items[1][0]
                    # print(x.speed, y.speed)
                    # 玩家爆炸图片
                    self.player_bomb.action(x.rect)
                    # 敌机爆炸图片
                    self.enemy_bomb.action(y.rect)
                    # 爆炸的声音
                    self.sound.playBombSound()

                # 玩家所有子弹和所有敌机的碰撞
                is_enemy = pygame.sprite.groupcollide(HeroPlane.bullets, self.enemys, True, True)
                if is_enemy:
                    items = list(is_enemy.items())[0]
                    self.score+=100
                    y = items[1][0]
                    # # 敌机爆炸图片
                    self.enemy_bomb.action(y.rect)
                    # # 爆炸的声音
                    self.sound.playBombSound()

            # 玩家飞机和子弹的显示
            self.players.update()
            # 敌机和子弹的显示
            self.enemys.update()

            # 4刷新显示窗口内容
            pygame.display.update()
            # 取一个合适的值 循环时停一下  单位是秒 减少cpu的消耗
            time.sleep(0.03)


if __name__ == '__main__':
    manager = Manager()
    manager.main()

```

