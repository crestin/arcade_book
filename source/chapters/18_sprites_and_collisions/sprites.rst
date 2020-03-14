.. _sprites:

Sprites And Collisions 精灵与碰撞
======================

Our games need support for handling objects that collide. Balls bouncing off
paddles, laser beams hitting aliens, or our favorite character collecting a
coin. All these examples require collision detection.

我们的游戏需要处理对象之间的碰撞。用球拍击球，激光束击中外星人，又或是我们掌控的角色收集金币，所有这些都需要侦测到碰撞事件。

The Arcade library has support for sprites. A sprite is a two-dimensional image that is part of the larger graphical scene. Typically a sprite will be some kind of object in the scene that will be interacted with like a car, frog, or little plumber guy.

Arcade 库中的精灵支持碰撞侦测。在一个大的平面图形场景中，精灵就是其中的一个二维图形。通常精灵是场景中的某种对象，它可以与其它精灵发互相交互，比如汽车、青蛙或者一个小马里奥。

.. image:: sprite.png
	:width: 250px

Originally, video game consoles had built-in hardware support for sprites. Now this specialized hardware support is no longer needed, but we still use the term "sprite." The `history of sprites`_ is interesting, if you want to read up more about it.

最初，视频游戏机内置了对精灵的硬件支持。现在不再需要这种专门的硬件支持，但我们仍然使用 “sprite” 一词。“sprite 的历史”很有趣，如果您想了解更多有关它的信息，你可以访问：https://en.wikipedia.org/wiki/Sprite 。

.. _history of sprites: https://en.wikipedia.org/wiki/Sprite_(computer_graphics)

Finding Images for Sprites 看看精灵的图像
--------------------------

There are several image formats that computers use:

这是一些计算机经常使用的图片格式：

* ``.bmp`` - Bitmap. This is an uncompressed image that normally uses three bytes to represent each dot in the image.
  These files can be very large. Because there are so many better options, this format is not used often. It is,
  however, the simplest format to use.
* ``.png`` - Great patent-free format for line art and clip art. Not great for photos. Can't hold animations.
* ``.gif`` - Great format for line art and clip art. Has had issues with patents (now expired). Can do animations.
* ``.jpg`` - Great file format for photos. Terrible for clip-art. Don't use for sprites.
* ``.svg`` - File format for storing line-art images that can scale to any resolution. Not compatible with the
  "arcade" library.

If you use Google's `advanced image search`_ you can find images that are "icon" sized, and either png or gif file format.

如果使用谷歌的图片搜索功能，你可以找到更多图标大小的图片，并且是png或gif格式的。

.. _advanced image search: https://www.google.com/advanced_image_search

There's also a great source for images from `kenney.nl`_. He has a lot of free and cheap game image assets.
That's where the following images come from that we will use in our examples:

kenney.nl 是一个发现图片的好地方，上边有很多免费的或者很便宜的图片素材。在我们的游戏中将会使用来自这里的图片：

.. _kenney.nl: http://kenney.nl/

.. figure:: character.png

    character.png

.. figure:: coin_01.png

    coin_01.png

Where to Save Images 放置图片
^^^^^^^^^^^^^^^^^^^^

Where should you save them? If you load your sprite with the code
below, the computer will look for the ``character.png`` image in the same
directory as your Python file. Save the image anywhere else, and it won't
be found.

把图片应该放在哪里呢？在下边的代码中在加载精灵的图片时，计算机会寻找与 Python 文件相同的目录，放在其它地方就会找不到了。

How to Reference Images 怎样引用图片
^^^^^^^^^^^^^^^^^^^^^^^

If you create your own game that you publish, you need to:

如果创建并发布你的游戏，你需要：

* Create your own images 设计自己的图形
* Hire someone to create your images 聘请别人来帮你设计
* Buy your own images with a license to use them in your own game 购买图形版权
* Find images that are public domain or licensed for public use 使用开放授权课免费使用的图形

If you are just creating a game for class that won't be used in public, then right before you load the image leave a comment with the source. I'll show this in a bit.

如果你只是在创建一个游戏类，并不打算发布，可以在你的源码中注明，稍后我会将到。

.. attention::
    Do not list "Google" as a source. That's like using "The Library" as a source in your report. Find
    the source of the image that Google is pointing to.


Basic Sprites and Collisions 基础的精灵与碰撞
----------------------------

Let's step through an example program that uses sprites. This example shows how to create a screen of sprites that are coins, and collect them using a sprite that is a character image controlled by the mouse as shown in the figure below. The program keeps "score" on how many coins have been collected. The code for this example may be found at:

让我们看一个使用精灵的示例程序。展示了如何创建满屏的金币，并使用一个由鼠标控制的图像精灵角色来收集它们，如下图所示。这个程序记录了收集到的金币数量。此示例的代码可以在以下位置找到：

http://arcade.academy/examples/sprite_collect_coins.html

In this chapter, we'll step through that example.

我么将逐步讲解示例。

.. figure:: collect_coins.gif

	Example Sprite Game

Getting the Application Started 启动游戏
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The first few lines of our program start off like other games we've done. We
import a couple libraries. Set a couple constants for the size of the screen,
and a couple new constants that we will use to scale our graphics.

像其它程序一样，在开始的部分导入两个库，然后设置屏幕的尺寸常量，这里我们使用了两个图形缩放比例的常量。

The example below should have nothing new, it just creates a window and sets a
background color. We'll add in the new stuff soon.

下边的代码没有什么新内容，仅仅创建了一个窗口并设置了背景色。很快我们就会加点新东西。

.. literalinclude:: sprite_sample_start.py
    :caption: Sprite Sample Start
    :language: python
    :linenos:


The Constructor 构造函数
^^^^^^^^^^^^^^^

What's next? We need to add our attributes to the ``MyGame`` class.
We add our attributes to the ``__init__`` method. Here is our
code with the expanded ``__init__``:

接着我们添加一些属性到 ``MyGame`` 类的 ``__init__`` 方法中：

.. literalinclude:: sprite_sample_expanded_init.py
    :caption: Expanded Init
    :language: python
    :emphasize-lines: 23-34
    :linenos:

The variables we are creating:

创建的变量有：

* ``player_list``:  When working with sprites, we normally put them into
  lists. Other game engines might call these sprite groups, or sprite layers.
  Our game will have one list for the player, and one list for the coins.
  Even if there is only one sprite, we should still put it in a list because
  there is a lot of code in ``SpriteList`` to optimize drawing.
* ``coin_list``: This is a list of all the coins. We will be checking if the
  player touches any sprite in this list.
* ``player_sprite``: This points to our player's sprite. It is the sprite
  we will move.
* ``score``: This keeps track of our score.

We use a command built into the parent ``Window`` class called
``set_mouse_visible`` to make the mouse not visible. Finally we set the
background color.

我们调用了父类 ``Window`` 中 ``set_mouse_visible`` 方法，传递参数为 False 以设置鼠标不可见。最后还设置了背景色。

The `setup` Function  `setup` 方法
^^^^^^^^^^^^^^^^^^^^

Next up, we will create a ``setup`` method. This will create our sprites and get
our game set up. We do this in a different method than ``__init__`` so that
if we ever want to restart the game, we can just call ``setup`` again.

我们会在 ``setup`` 方法中设置游戏并创建精灵。在 ``__init__`` 以外这样做，是为了可以在游戏的时候，如果重新开始只需要调用 ``setup`` 即可。

The ``setup`` method is not called automatically.Therefore in the example below, note we have added thecode that calls the ``setup`` function near the end: ``window.setup()``.

``setup`` 方法不会被自动调用。所以我们在下边的代码末尾处使用 ``window.setup()`` 调用。

.. literalinclude:: sprite_sample_player.py
    :caption: Sprite Sample With Player
    :language: python
    :emphasize-lines: 36-51, 56-64, 70
    :linenos:


How does the code above work?

以上代码是如何运行的？

First, we need some lists to hold our sprites. We could do use a list like
this:

首先，我们需要一个列表放置我们的精灵：

.. code-block:: Python

    coin_list = []

But wait! ``coin_list`` is an instance variable that's part of our class.
we need to prepend it with ``self.``.

``coin_list`` 是一个实例变量，所以我们需要加上 ``self.``。

.. code-block:: Python

    self.coin_list = []

However, the Arcade library has a class especially for handling sprite lists.
This class is called ``SpriteList``.
For more information, check out the SpriteList_ documentation.
So instead of creating an empty list with
``[]``, we will create a new instance of the ``SpriteList`` class:

但是，Arcade库有一个类，专门用于处理sprite列表。这个类称为SpriteList。有关更多信息，请查看SpriteList文档。因此，我们将创建SpriteList类的新实例，而不是使用[]创建空列表：

.. _SpriteList: http://arcade.academy/arcade.html#arcade.sprite.SpriteList

.. code-block:: Python

    self.coin_list = SpriteList()

Except that doesn't work. Why? ``SpriteList`` is in the Arcade library. We
need to prepend any reference to things in the Arcade library with ``arcade``
of course, so now we have:

直接使用 SpriteList() 会出错，因为它是定义在 Arcade库 中的。所以我们需要加上引用才可以：

.. code-block:: Python

    self.coin_list = arcade.SpriteList()

We need a separate list for just coins. This list won't have the player. We also
need to reset our score to 0.

除了一个空的金币列表，我们还需要将得分初始化为 0。

.. code-block:: Python

    self.coin_list = arcade.SpriteList()

    self.score = 0

Now we need to create our sprites. The name of the class that represents sprites
is called ``Sprite``. You can read more about it by looking at the Sprite_
documentation.
The Sprite constructor takes two parameters. A path to the image we will be
using, and how big to scale it.

现在我们使用 ``Sprite`` 类来创建精灵，相关信息可以查看文档。Sprite 构造函数需要两个参数。一个是图片路径，一个是缩放值。

For class, please source the image right before you load it. If you drew your own image, please note that as well.

在加载之前，请确保图像资源没有问题。

.. _Sprite: http://arcade.academy/arcade.html#arcade.sprite.Sprite


.. code-block:: Python

    # Character image from kenney.nl
    self.player_sprite = arcade.Sprite("character.png", SPRITE_SCALING_PLAYER)


How do we draw all our sprites? Really easy. Just call the ``draw`` method that
exists for us in the ``SpriteList`` class. We just need to do this for each of
our sprite lists.

如何显示全部的精灵？这确实很容易就能做到，只需要调用精灵列表 ``SpriteList`` 的 ``draw`` 方法。就像下边这样：

.. code-block:: Python

    def on_draw(self):

            arcade.start_render()

            # Draw all the sprite lists.
            self.coin_list.draw()
            self.player_list.draw()

Wait. We don't have many sprites. There are no coins, and we have just the player.
Let's add a ``for`` loop to our program and create a bunch of coins:

除了玩家精灵外，我们还没有其它精灵。需要使用 ``for`` 循环创建一批金币出来。

.. literalinclude:: sprite_sample_coins.py
    :caption: Sprite Sample With Player And Coins
    :language: python
    :emphasize-lines: 54-65
    :linenos:

Drawing The Score 显示成绩
^^^^^^^^^^^^^^^^^

In addition to drawing the sprites, let's go ahead and
put the score on the screen:

除了绘制精灵，我们还要讲成绩显示在屏幕的顶部：

.. code-block:: Python

    # Put the text on the screen.
    output = "Score: " + str(self.score)
    arcade.draw_text(output, 10, 20, arcade.color.WHITE, 14)

Rather than do that ``"Score: " + str(self.score)`` it is possible to do
print formatting if you are using Python 3.6 or later. We'll talk more about
print formatting later, but that code would look like:

在 Python 3.6 之后的版本中，可以更好地格式化字符串，之后我们会详细讨论，就像下边这样使用：

.. code-block:: Python

    # Put the text on the screen.
    output = f"Score: {self.score}"
    arcade.draw_text(output, 10, 20, arcade.color.WHITE, 14)

There are three standards for how to format strings in Python, so that whole
subject is a bit confusing.

有三种标准的方法用来格式化字符串，这往往容易让人感到迷惑。

The On Mouse Motion Method 鼠标动作方法
^^^^^^^^^^^^^^^^^^^^^^^^^^

Moving the player sprite with the mouse is easy. All sprites have instance
variables ``center_x`` and ``center_y``. Just change those values to the mouse's
x and y location to move the sprite.

使用鼠标移动玩家控制的精灵很容一就能做到。所有的精灵都会有实例化参数  ``center_x`` 和 ``center_y``，只需要提供鼠标的 x 和 y 坐标给精灵即可。

.. code-block:: Python

    def on_mouse_motion(self, x, y, dx, dy):

        self.player_sprite.center_x = x
        self.player_sprite.center_y = y


Now, our whole program looks like:

现在，我们的程序看起来像下边这样：

.. literalinclude:: sprite_sample_with_mouse_motion.py
    :caption: Sprite Sample With Mouse Motion And Score
    :language: python
    :emphasize-lines: 73-75, 77-82
    :linenos:

The Update Method 更新（Update）方法
^^^^^^^^^^^^^^^^^

Our ``update`` method needs to do three things:

我们的 ``update`` 方法需要完成三件事：

1. Update each of the sprites 更新每一个精灵的状态
2. Check to see if the player is touching any coins 侦测玩家精灵与其它精灵是否发生碰撞
3. Remove any coins colliding with the player, and update the score. 移除发生碰撞的金币，然后更新成绩

Each sprite has its own ``update`` method. This allows sprites to move and
animate its images. Right now, our sprite does not have this method. But we
will soon. Rather than call the ``update`` method of each sprite we have,
there is an ``update`` method in each sprite list that will call ``update``
on each sprite in the list. Therefore, just calling ``update`` with our
``coin_list`` will cause all coin sprites to update.

每个精灵都有自己的 ``update`` 方法。这会使精灵产生移动或是动画效果。我们没有调用每个精灵的 ``update`` 方法，而是在每个精灵列表中都有一个 ``update`` 方法，它将调用列表中每个精灵的 update。因此，只要用我们的硬币列表调用 update，就会导致所有硬币精灵更新。

.. code-block:: Python

    self.coin_list.update()

How do we detect what coins are touching the player? We call the
``check_for_collision_with_list`` method. Pass it in our player sprite,
along with a list of all the coins. That function will return a list of
all colliding sprites. If no sprites collide, the list will be empty.

我们如何检测玩家收集了多少硬币？我们调用 ``check_for_collision_with_list`` 方法，把玩家和所有的硬币精灵传给它。该函数将返回所有碰撞精灵的列表。如果没有精灵碰撞，列表将为空。

.. code-block:: Python

    # Generate a list of all sprites that collided with the player.
    coins_hit_list = arcade.check_for_collision_with_list(self.player_sprite, self.coin_list)

What do we do with this ``hit_list`` we get back? We loop through it. We add one
to the score for each sprite hit.

我们可以便利返回的 ``hit_list`` 列表，然后没个收集到（发生碰撞）的金币就加 1 分。

We also need to get rid of the sprite. The sprite class has a method called
``kill``. This method will remove the sprite from existence.

我们还需要清除掉精灵。sprite 类有一个名为 kill 的方法。此方法将从现有的精灵列表中移除该精灵。

.. code-block:: Python

    # Loop through each colliding sprite, remove it, and add to the score.
    for coin in coins_hit_list:
        coin.remove_from_sprite_lists()
        self.score += 1

Here's the whole ``update`` method put together:

现在将 ``update`` 方法一起加入进去：

.. literalinclude:: sprite_sample_with_update.py
    :caption: Sprite Sample With Update Method
    :language: python
    :emphasize-lines: 84-98
    :linenos:
