# Tic-tac-toe AI

We're going to write a computer AI that can't be beaten at
TicTacToe. Download the [TicTacToeAI Repo][ttt-repo] zip file
to get started. This has our TTT solution code, plus some stub classes for you
to fill out. Check that you can run the specs:

    bundle exec rspec spec/01_tic_tac_toe_node_spec.rb
    bundle exec rspec spec/02_super_computer_player_spec.rb

Of course, they should be failing right now. Call your TA over if you
have problems.

[ttt-repo]: https://github.com/appacademy-demos/TicTacToeAI/archive/master.zip

## Phase I: `TicTacToeNode`

Let's create a class `TicTacToeNode`. This will represent a TTT
game-state: it will store the current state of the `board` plus the
`next_mover_mark` of the player who's will move next.  Also, if given,
store the `prev_move_pos` (this will come in handy later).

This doesn't use the `TreeNode` you made earlier. We are making a
completely new class independent of the `TreeNode`.

Write a method `children` that returns nodes representing all the
potential game states one move after the current node. To create this
method, it will be necessary to iterate through all positions that are
`empty?` on the board object. For each empty position, create a node
by duping the board and putting a `next_mover_mark` in the
position. You'll want to alternate `next_mover_mark` so that next time
the other player gets to move. Also, set `prev_move_pos` to the
position you just marked, for reasons that will make sense when we use
it later.

Next, we want to characterize a node as either a
`#losing_node?(player)` or `#winning_node?(player)`. 
These methods are *not* optional, you will need them both.

A `#losing_node?` is described in the following cases:

* Base case: **the board is over** AND
    * If `winner` is the opponent, this is a losing node.
    * If `winner` is `nil` or us, this is not a losing node.
* Recursive case:
    * It is the player's turn, and all the children nodes are losers
      for the player (anywhere they move they still lose), OR
    * It is the opponent's turn, and one of the children nodes is a
      losing node for the player (assumes your opponent plays
      perfectly; they'll force you to lose if they can).

**NB: a draw (Board#tied?) is NOT a loss, if a node is a draw,
losing_node? should return false.**

Likewise, a winning node means either:

* Base case: **the board is over** AND
    * If `winner` is us, this is a winning node.
    * If `winner` is `nil` or the opponent, this is not a winning
      node.
* Recursive case:
    * It is the player's turn, and one of the children nodes is a
      winning node for the player (we'll be smart and take that move),
      OR
    * It is the opponent's turn, and all of the children nodes are
      winning nodes for the player (even TicTacToeKasparov can't beat
      you from here).

Notice that `winning_node?` and `losing_node?` are both defined
recursively. This is what makes them look at all the ways the game can
play out. For instance, a node can be a winning node even though we
won't win on the very next turn; it just requires that, assuming we
play perfectly, eventually we'll beat the opponent no matter what they
do.

## Phase II: `SuperComputerPlayer`

Write a subclass of `ComputerPlayer`; we'll override the `#move`
method to use our `TicTacToeNode`.

In the `#move` method, build a `TicTacToeNode` from the board stored
in the `game` passed in as an argument. Next, iterate through
the `children` of the node we just created. If any of the children
is a `winning_node?` for the mark passed in to the `#move` method,
`return` that node's `prev_move_pos` because that is the position
that causes a certain victory! I told you we would use that later!

If none of the `children` of the node we created are `winning_node?`s,
that's ok. We can just pick one that isn't a `losing_node?` and return
its `prev_move_pos`. That will prevent the opponent from ever winning,
and that's almost as good. To make that even more clear: if a winner
isn't found, pick one of the children of our node that returns `false`
to `losing_node?`.

Finally, `raise` an error if there are no non-losing nodes. In TTT, if
we play perfectly, we should always be able to force a draw.

Run your TTT game with the `SuperComputerPlayer` and weep tears of
shame because you can't beat a robot at tic tac toe.
