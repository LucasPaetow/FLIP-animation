# Javascript animations

## Introduction

Animations are great. They guide the user focus and can make a site feel snappy and fast. But if done incorrectly, they will do the opposite: they will make the site feel sluggish and janky.

### Janky?

When the browser needs to change the appearance of an element, it needs to recalculate every element affected by the change.
When a lot of Elements are affected and need to be recalculated, the browser has to work longer for the calculations. If these calculations exceed the time the screen takes to refresh the screen, it skips a frame.

An example: Most devices run at 60 frames per second. So the recalculation per frame can’t take longer than roughly 10ms (1sec/60 => 16.66ms - housekeeping from the browser). Otherwise the animation is not smooth and “stutters”

## How to to do it then?

There are 2 ways to make animations feel smooth and keep them at 60 FPS / jank-free:

### the CSS way

Every change to the DOM triggers the calculation of the “critical render path” to bring the pixel updates to the screen. This involves up to 3 steps:

- Layout / Reflow
  In this step, the browser starts calculating the dimensions for each element, starting from the document root. This results in the box-model

- Paint
  This step is about creating layers and filling them with pixels. Including but not limited to text, colors, images, borders and shadows.

- Compositing
  Here the browser will sent the layers to the GPU to finally draw them in the correct order onto the screen. This happens on a different thread.

The more of these steps are involved, the more work the browser has to do. Since the `transforms and opacity`
Only require changes of the compositing step, they are very efficient.

#### How? With a FLIP

You might think, these transforms may only really work for small visual changes (e.g. a button press) but they can also animate seemingly heavy layout changes like expanding a card.

Instead of scaling the card to the full display size, you change the card to its final form and scale it down to the previous size without animation, afterwards you animate the difference (which is now a scale operation)

This process involves 4 steps and therefore coined the term FLIP:

- First: get the dimensions of the starting element
- Last: change the layout and get its dimensions
- Invert: Transform the Element from its last form to the starting form
- Play: Visually animate the difference

---

CODEEXAMPLE
https://codesandbox.io/s/flamboyant-babbage-5ssln

---

#### Things to consider

- This works well with shapes that stay the way they are. Like animating a small rectangle in a bigger one or moving an element around the screen. Even small changes might look off, e.g. border-radius might change on a different size
- It gets complicated fast, if multiple elements are affected, since it has to be done for each element (modern frameworks might help reduce the complexity)