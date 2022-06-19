---
layout: ../../../layouts/SimpleLayout.astro
---

# Alpha-beta pruning on paper: Intro

## Why on a paper?

Algorithms mostly are for computer. However, to check if you understand the algorithm,
my university often uses written tests. This means you have to replicate it by hand.

Take the well-known Dijikstra's algorithm for example. In my university, we were taught this
in a Discrete Math course, with no implementation in code. What we were given
was jsut a way to do this algorithm on paper.

![dijikstra](./dijikstra.png)

(*picture taken from my course material*)

This worked well, I understood the algorithm and can pretty much implement it any time I want.

In contrast to this, my *Introduction to AI course*, specifically the **alpha-beta pruning algorithm**,
doesn't have such thing.

### Minimax

In game playing, you want to maximize your chances of winning while minimizing your opponent's.
The name [Minimax](https://en.wikipedia.org/wiki/Minimax) comes from this. You view the game
as a tree of states, each node corresponds to a number from -infinity to infinity, and each
branch corresponds to one action taken, brings you from one state to another state. Assuming
you and your opponent always take the best path, this algorithm calculate what is the result
you will end up with.

![minimax](./minimax.png)

(*example taken from my course materials*)

Take the above game tree for example. At `A`, you want to pick the move that maximize your advantage.
At `B`, `C` or `D`, your opponent want to pick the move that minimize your advantage. The last level
is already evaluated and contains the value of your advantage of the situation.

Although `F = 9` is the best for you, if you choose to move to `B`, your opponent will just move
to `F` and leave you with `-6`, which is the worst.

The minimax algorithm evaluates from bottom to top:
- At `B`, your opponent will definitely pick `F = -6`, makes `B = -6`;
- At `C`, your opponent will definitely pick `I = -2`, makes `C = -2`;
- At `D`, your opponent will definitely pick `J = -4`, makes `D = -4`;
- At `A`, among `B`, `C`, `D`, you will have to pick `C = -2`, makes `A = -2`.
This is the best you can do in this situation.

In other words, at `A`, you'll have to move to `C`, which forces your opponent to move to `I = -2`. 

This algorithm alone is nothing much though. You just need to start from the leaves, taking min
and max until you reach the root node. What matters is the
[alpha-beta pruning variant](https://en.wikipedia.org/wiki/Alpha%E2%80%93beta_pruning) of this
algorithm. 

### Alpha-beta pruning

![alpha beta tree](https://upload.wikimedia.org/wikipedia/commons/9/91/AB_pruning.svg)

(*picture taken from wikipedia*)

Basically, you don't have to traverse the whole tree. Keeping track of information about the best
branch you checked though allows you to leave out bad branches early without exploring those.

You keep track of `alpha`, which is the best value you can have at any time, and `beta`, which
is the best value your opponent can have. `alpha` is initialized as `-infinity` and
`beta` is initialized as `infinity` (your opponent wants negative number).

All you need to check is if `alpha >= beta` at any time, which means this is too good for you
and too bad for your opponent. This won't happen in the game, your opponent just
won't choose this path (like `F = 9` on the example above).

Below is a simplementation of the algorithm provided in my course material, written in TypeScript:

```ts
// functions you have to implement for your game
interface Position {
  gameEnded(): boolean;
  evaluate(): number;
  nextPositions(): Array<Position>;
  deepEnough(depth: number): boolean;
}

function player(position: Position, depth: number, a: number, b: number) {

  // stop exploring if game ended or deep enough
  if (position.deepEnough(depth) || position.gameEnded()) {
    return { value: position.evaluate(), path: position }
  }

  let bestPath;
  for (const nextpos of position.nextPositions()) {
    // see the game in your opponent view
    result = opponent(nextpos, depth + 1, a, b)
    
    // select the move which improve the situation
    // i.e, maximize a
    if (result.value > a) {
      a = result.value
      // append nextPosition to beginning of result.path
      // this is called "destructuring"
      bestPath = [nextPosition, ...result.path]
    }
    
    // if this happens, you know that this position (not nextPosition)
    // isn't likely to happen
    if (a >= b) break;
  }

  return {
    value: a
    path: bestpath
  }
}

// basically the same as above, only a few changes
function opponent(position: Position, depth: number, a: number, b: number) {

  if (position.deepEnough(depth) || position.gameEnded()) {
    return { value: position.evaluate(), path: [position] }
  }

  let bestpath;
  for (const nextPosition of position.nextPositions()) {
    // here 👇
    result = player(nextPosition, depth + 1, a, b)
    
    // here 👇
    if (result.value < b) {
      b = result.value
      bestPath = [nextPosition, ...result.path]
    }
    
    // surprisingly, this doesn't change.
    // occasionally, you'll see b <= a
    if (a >= b) break;
  }

  return { value: a, path: bestpath }
}

// run the algorithm
player(startPosition, 0, -Infinity, +Infinity)
```

Back to the post. The algorithm above is easily understandable, but once I tried to do it
by hand, I did not as much pruning as the computer does. In other words, I misunderstanded
the algorithm wrong somehow... Or is it?

### I am bad at keeping track and updating state

When I first try to replicate the algorithm on paper, I tried to draw the tree and
use an eraser to erase and update the value of alpha, beta at each node (or even worse,
strikethrough old value).

One reason is probably this. Computers are good at flipping and updating bits, but not me
and my paper. Unless I have some erasers that can erase anything out of existence, updating
things on the paper doesn't feel right to me.

Take solving an equation for example. Although all you did was updating the form of the
equations (factoring, decomposing, simplifing, ...), you barely had to use an eraser to
update at all. Or take the Dijikstra's example above, nothing is erased.

What I learned is to do something on the paper, try not to erase stuff.

### I am bad at working with non-linear structures

Working with trees on paper is a nightmare. I remember grasping the concept of auto balancing
trees, but I could not imagine implement the algorithm correctly in one try. One reason is
I could not imagine how I would do it by hand. Trees on paper is already bad enough, not to
mention the swapping (updating states!) on paper...

In contrast, tables are more suitable than trees on a paper. That is why although Dijikstra's
algorithm works with graph, the paper version is still human friendly. It is just
adding entries to a table.

## What this series is about

To sum up, in order to understand the alpha-beta pruning algorithm, I want to find a version
of it that is human friendly and works on paper. In fact, I have already "invented" one.

I could just record a video trying to explain how you would do it on paper, but I went the
artistic way: make a visualization of it, on the web. This series is the process of me doing
this.
