# Leetcode-3283.-Maximum-Number-of-Moves-to-Kill-All-Pawns
Solution for waifujahat | C# | DP | 100% with explanation step by step

# Intuition
Capturing all pawns positioned on chessboard using a knights moves, with two players (Alice and Bob) alternating turns to maximize or minimize the total moves, since knights have specific movement patterns, the shortest path to any pawn must be calculated, which is essentially a search for the minimum steps in a grid, then becomes a variant of TSP, where I calculate paths between all points but with two players who alternately aim to either maximize or minimize the move count

# Approach

Since knights have fixed moves, I can calculate the minimum moves between any two points on a 50x50 grid using a BFS from each point to every other point, this produces a distance matrix where each entry represents the minimum moves from one position to another

I can DFS with memoization to handle the traveling-like problem, I keep track of captured pawns with a bitmask and for each state, I recursively choose the next position to visit, updating the bitmask and score, Alice aims to maximize the score, while Bob aims to minimize it, based on whose turn it is (tracked by counting bits in the bitmask)

Each player makes the optimal move given the state, with Alice trying to maximize the total move count and Bob trying to minimize it and and result of each recursive call is memoized to avoid redundant calculations

# Complexity
Time complexity
Every BFS call requires 𝑂(𝑚2) O(m~2), where m is the grids side length (in this case, 50), resulting in 𝑂(𝑛⋅𝑚2) O(n⋅m^2) overall and DFS yields O(n⋅2 n) since there are 2 𝑛 2 n possible subsets of pawns to visit and 𝑛 n possible beginning positions, so in general, the complexity is roughly O(n⋅(m 2 +2 n )) (𝑂(𝑛⋅(𝑚 2 + 2 𝑛))

# Space complexity
For every pawn, the memorization table for 2 n states is 𝑂( 𝑛⋅ 2 n) O(n⋅2 n) so the space complexity is 𝑂 (𝑛⋅ (𝑛 + 2 𝑛))

# Code Explanation

It starts by storing the knight's initial position and the positions of the pawns

Create a distance matrix to calculate how many moves it takes for the knight to move between its position and each pawns position using the ComputeMinMoves method

Then uses a recursive function Dfs to explore all possible moves and determine the best outcome based on whose turn it is (the knights player, Alice, or the opponent)

Use DFS to explore all combinations of moves

Check if all pawns have been captured (if the mask indicates that), and if so, it returns 0 (no more moves needed)

Use a bitmask to keep track of which pawns have been captured and decides whether to maximize or minimize the score based on whose turn it is

For each pawn that hasn’t been captured, it calculates the move count and recursively calls itself to continue exploring moves

The best score for each state is stored in a memoization table to avoid recomputing results for the same state

Calculate the minimum number of moves needed for the knight to get from one position (sx, sy) to another (tx, ty)

Use a Breadth First Search (BFS) approach, which explores all possible knight moves level by level

Keep track of visited positions to avoid revisiting them and uses a queue to manage the current positions being explored

If the knight reaches the target position, it returns the number of moves taken, if not then it returns a maximum value indicating that the target cannot be reached
