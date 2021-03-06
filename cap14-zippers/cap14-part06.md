Watch your step
===============

So far, while walking through our data structures, whether they were binarytrees, lists or file systems, we didn't really care if we took a step too farand fell off. For instance, our [code]goLeft[/code] function takesa zipper of a binary tree and moves the focus to its left sub-tree:

But what if the tree we're stepping off from is an empty tree? That is, what ifit's not a [code]Node[/code], but an [code]Empty[/code]?In this case, we'd get a runtime error because the pattern match would fail andwe have made no pattern to handle an empty tree, which doesn't have anysub-trees at all. So far, we just assumed that we'd never try to focus on theleft sub-tree of an empty tree as its left sub-tree doesn't exist at all. Butgoing to the left sub-tree of an empty tree doesn't make much sense, and so farwe've just conveniently ignored this.

Or what if we were already at the root of some tree and didn't have anybreadcrumbs but still tried to move up? The same thing would happen. It seemsthat when using zippers, any step could be our last (cue ominous music). Inother words, any move can result in a success, but it can also result in afailure. Does that remind you of something? Of course, monads! Morespecifically, the [code]Maybe[/code] monad which adds a context ofpossible failure to normal values.

So let's use the [code]Maybe[/code] monad to add a context ofpossible failure to our movements. We're going to take the functions that workon our binary tree zipper and we're going to make them into monadic functions.First, let's take care of possible failure in [code]goLeft[/code]and [code]goRight[/code]. So far, the failure of functions thatcould fail was always reflected in their result, and this time is nodifferent. So here are [code]goLeft[/code] and[code]goRight[/code] with an added possibility of failure:

Cool, now if we try to take a step to the left of an empty tree, we get a[code]Nothing[/code]!

Looks good! How about going up? The problem before happened if we tried to go up butwe didn't have any more breadcrumbs, which meant that we were already in theroot of the tree. This is the [code]goUp[/code] function thatthrows an error if we don't keep within the bounds of our tree:

Now let's modify it to fail gracefully:

If we have breadcrumbs, everything is okay and we return a successful new focus,but if we don't, then we return a failure.

Before, these functions took zippers and returned zippers, which meant that wecould chain them like this to walk around:

But now, instead of returning [code]Zipper a[/code], they return [code]Maybe (Zipper a)[/code], so chaining functions like thiswon't work. We had a similar problem when wewere <a href="a-fistful-of-monads#walk-the-line">dealing with our tightropewalker</a> in the chapter about monads. He also walked one step at a time andeach of his steps could result in failure because a bunch of birds could land onone side of his balancing pole and make him fall.

Now, the joke's on us becausewe're the ones doing the walking, and we're traversing a labyrinth of our owndevising. Luckily, we can learn from the tightrope walker and just do what hedid, which is to exchange normalfunction application for using [code]&gt;&gt;=[/code], which takesa value with a context (in our case, the [code]Maybe (Zipper a)[/code], which has a context of possible failure) and feeds it into a function whilemaking sure that the context is taken care of. So just like our tightrope walker, we're going to trade in all our [code]-:[/code] operators for[code]&gt;&gt;=[/code]. Alright, we can chain our functions again!Watch:

We used [code]return[/code] to put a zipper in a [code]Just[/code] and then used [code]&gt;&gt;=[/code] tofeed that to our [code]goRight[/code] function. First, we made atree that has on its left an empty sub-tree and on its right a node that has twoempty sub-trees. When we try to go right once, the result is a success, becausethe operation makes sense. Going right twice is okay too; we end up with thefocus on an empty sub-tree. But going right three times wouldn't make sense,because we can't go to the right of an empty sub-tree, which is why the result is a [code]Nothing[/code].

Now we've equipped our trees with a safety-net that will catch us shouldwe fall off. Wow, I nailed this metaphor.

Our file system also has a lot of cases where an operation could fail, such astrying to focus on a file or folder that doesn't exist. As an exercise, you canequip our file system with functions that fail gracefully by using the [code]Maybe[/code] monad.