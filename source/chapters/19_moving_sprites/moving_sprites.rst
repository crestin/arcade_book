
Moving Sprites 移动精力
==============

How do we get sprites to move?

怎样才能使精灵动起来？

To customize our sprite's behavior, we need to subclass the ``Sprite`` class with our own child class. This is easy:

想要定义精灵的行为，我们只需要创建 ``Sprite`` 的子类，这很容易做到：

.. code-block:: Python

    class Coin(arcade.Sprite):

We need to provide each sprite with a ``update`` method. The ``update`` method is automatically called to update the sprite's position.

我们需要为精灵提供一个 ``update`` 方法，``update`` 方法会自动被调用，更新精灵的位置。

.. code-block:: Python

    class Coin(arcade.Sprite):

        def update(self):
            # Code to move goes here

Wait! We have a new class called Coin, but we aren't using it. Find in our
original code this line:

我们有个一名为 Coin 的新类，但我们没有使用。找到我们的原代码行：

.. code-block:: Python

    coin = arcade.Sprite("coin_01.png", COIN_SPRITE_SCALING)

See how it is creating an instance of ``Sprite``? We want to create an instance of our new ``Coin`` class instead:

看看它是如何创建 ``Sprite`` 实例的？我们想创建一个新 ``Coin`` 类的实例：

.. code-block:: Python

    coin = Coin("coin_01.png", COIN_SPRITE_SCALING)

Now, how do we get the coin to move?

现在，如何移动 coin 呢？

Moving Sprites Down 向下移动精灵
-------------------

To get the sprites to "fall" down the screen, we need to make their y location
smaller. This is easy. Over-ride ``update`` in the sprite and subtract from
y each frame:

想要在屏幕上达到精灵“坠落”的效果，我们只需要使它们 y轴 方向的数值逐渐减小即可。这很容易实现，重写精灵的 ``update``方法，使在每一帧上都减少一点：

.. code-block:: Python

    class Coin(arcade.Sprite):

        def update(self):
            self.center_y -= 1

Next, create an instance of the ``Coin`` class instead of a ``Sprite`` class.

接下来，创建一个类 ``Coin`` 的实例，而不是类 ``Sprite`` 的。

.. literalinclude:: sprite_sample_move_down.py
    :caption: Sprite Sample Move Down
    :language: python
    :emphasize-lines: 15-18, 64
    :linenos:

This causes the coins to move down. But once they move off the screen they
keep going into negative-coordinate land. We can't see them any more. Sad.

硬币向下移动。但是，一旦它们离开屏幕，就会继续进入负坐标区域。令人沮丧的是我们再也看不到他们了。

.. figure:: coins_down_1.gif

    Coins moving down

Resetting to the Top
^^^^^^^^^^^^^^^^^^^^

We can get around this by resetting the coins up to the top. Here's how its
done:



.. code-block:: Python

    class Coin(arcade.Sprite):

        def update(self):
            self.center_y -= 1

            # See if we went off-screen
            if self.center_y < 0:
                self.center_y = SCREEN_HEIGHT

But this isn't perfect. Because if your eyes are fast, you can see the coin
'pop' in and out of existence at the edge. It doesn't smoothly slide off. This is
because we move it when the *center* of the coin is at the edge. Not the top of
the coin has slid off.

There are a couple ways we can do this. Here's one. We'll check at -20 instead
of 0. As long as the coin radius is 20 or less, we are good.

.. code-block:: Python

    class Coin(arcade.Sprite):

        def update(self):
            self.center_y -= 1

            # See if we went off-screen
            if self.center_y < -20:
                self.center_y = SCREEN_HEIGHT + 20

There's another way. In addition to ``center_y``, sprites have other
members that are useful in these cases. They are ``top``, ``bottom``,
``left`` and ``right``. So we can do this:

.. code-block:: Python

    class Coin(arcade.Sprite):

        def update(self):
            self.center_y -= 1

            # See if we went off-screen
            if self.top < 0:
                self.bottom = SCREEN_HEIGHT

Doing this allows the coins to smoothly slide on and off the screen. But since
they reappear at the top, we get repeating patters. See the image below:

.. figure:: pattern.gif

    Coins repeating in a pattern

Instead we can randomize it a bit:

.. code-block:: Python

    def update(self):

        # Move the coin
        self.center_y -= 1

        # See if the coin has fallen off the bottom of the screen.
        # If so, reset it.
        if self.top < 0:
            # Reset the coin to a random spot above the screen
            self.center_y = random.randrange(SCREEN_HEIGHT + 20,
                                             SCREEN_HEIGHT + 100)
            self.center_x = random.randrange(SCREEN_WIDTH)

Never Ending Coins
^^^^^^^^^^^^^^^^^^

This works, but when we we collect all the coins we are done. What if it was
a never-ending set of coins? Instead of "killing" the coin, let's reset it to
the top of the screen.

.. code-block:: Python

    def update(self, delta_time):
        """ Movement and game logic """

        self.coin_list.update()

        hit_list = arcade.check_for_collision_with_list(self.player_sprite, self.coin_list)

        for coin in hit_list:
            self.score += 1

            # Reset the coin to a random spot above the screen
            coin.center_y = random.randrange(SCREEN_HEIGHT + 20,
                                             SCREEN_HEIGHT + 100)
            coin.center_x = random.randrange(SCREEN_WIDTH)

We can even take that common code, and move it to a method. Here's a full example:

.. literalinclude:: sprite_sample_move_down_full.py
    :caption: Full Move Down Sprite Sample
    :language: python
    :linenos:
    :emphasize-lines: 15-36

Bouncing Coins
--------------

Instead of always adding one to the y-coordinate and have the sprites move
down, we can keep a vector by using ``change_x`` and ``change_y``. By
using these, we can have the sprite bounce around the screen:

.. figure:: sprites_bouncing.gif

    Coins bouncing around

.. literalinclude:: sprite_sample_move_bouncing.py
    :caption: sprites_sample_move_bouncing.py
    :language: python
    :emphasize-lines: 21-22, 24-41, 92-93
    :linenos:

TODO: Put in some text about spawning a sprite too close to the edge. Also make a refer to it from the final
project.


Take what you've learned from the example above, and see if you can replicate
this:

.. figure:: Test_Pattern.gif

    Test Pattern

Coins Moving In Circles
-----------------------

.. figure:: sprites_circle.gif

    Coins moving in a circle

.. literalinclude:: sprites_circle.py
    :caption: sprites_circle.py
    :language: python
    :linenos:

Rotating Sprites
----------------

Sprites can easily be rotated by setting their ``angle`` attribute. Angles are
in degrees. So the following will flip the player upside down:

.. code-block:: Python

    self.player_sprite.angle = 180

If you put this in the coin's ``update`` method, it would cause the coins to
spin:

.. code-block:: Python

    self.angle += 1

    # If we rotate past 360, reset it back a turn.
    if self.angle > 359:
        self.angle -= 360

