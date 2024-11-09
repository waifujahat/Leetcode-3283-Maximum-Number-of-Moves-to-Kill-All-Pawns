# Leetcode-3283-Maximum-Number-of-Moves-to-Kill-All-Pawns in C#
Solution for waifujahat | C# | DP | 100% with explanation step by step

![Screenshot 2024-11-05 161721.png](https://assets.leetcode.com/users/images/c9371f4f-7c54-46d3-a578-cff889964225_1730798755.64481.png)

---

# Intuition
Capturing all pawns positioned on chessboard using a knights moves, with two players (Alice and Bob) alternating turns to maximize or minimize the total moves, since knights have specific movement patterns, the shortest path to any pawn must be calculated, which is essentially a search for the minimum steps in a grid, then becomes a variant of TSP, where I calculate paths between all points but with two players who alternately aim to either maximize or minimize the move count

---

# Approach

1. Since knights have fixed moves, I can calculate the minimum moves between any two points on a 50x50 grid using a BFS from each point to every other point, this produces a distance matrix where each entry represents the minimum moves from one position to another

2. I can DFS with memoization to handle the traveling-like problem, I keep track of captured pawns with a bitmask and for each state, I recursively choose the next position to visit, updating the bitmask and score, Alice aims to maximize the score, while Bob aims to minimize it, based on whose turn it is (tracked by counting bits in the bitmask)

3. Each player makes the optimal move given the state, with Alice trying to maximize the total move count and Bob trying to minimize it and and result of each recursive call is memoized to avoid redundant calculations

---

# Code Explanation 

1. It starts by storing the knight's initial position and the positions of the pawns

2. Create a distance matrix to calculate how many moves it takes for the knight to move between its position and each pawns position using the ComputeMinMoves method

3. Then uses a recursive function Dfs to explore all possible moves and determine the best outcome based on whose turn it is (the knights player, Alice, or the opponent)

4. Use DFS to explore all combinations of moves

5. Check if all pawns have been captured (if the mask indicates that), and if so, it returns 0 (no more moves needed)

6. Use a bitmask to keep track of which pawns have been captured and decides whether to maximize or minimize the score based on whose turn it is

7. For each pawn that hasn’t been captured, it calculates the move count and recursively calls itself to continue exploring moves

8. The best score for each state is stored in a memoization table to avoid recomputing results for the same state

9. Calculate the minimum number of moves needed for the knight to get from one position (sx, sy) to another (tx, ty)

10. Use a Breadth First Search (BFS) approach, which explores all possible knight moves level by level

11. Keep track of visited positions to avoid revisiting them and uses a queue to manage the current positions being explored

12. If the knight reaches the target position, it returns the number of moves taken, if not then it returns a maximum value indicating that the target cannot be reached

# Code
```csharp []
public class Solution {
    public int MaxMoves(int kx, int ky, int[][] positions) {
        
        int n = positions.Length;
        var posList = new List<(int, int)> { (kx, ky) };
        foreach (var pos in positions) posList.Add((pos[0], pos[1]));

        // Distance matrix to store minimum moves between positions
        var distances = new int[n + 1, n + 1];
        for (int i = 0; i <= n; i++)
        {
            for (int j = 0; j <= n; j++)
            {
                distances[i, j] = (i == j) ? 0 : ComputeMinMoves(posList[i].Item1, posList[i].Item2, posList[j].Item1, posList[j].Item2);
            }
        }

        // Memoization table for storing previously computed results
        var memo = new int[1 << n, n + 1];
        for (int i = 0; i < memo.GetLength(0); i++)
        {
            for (int j = 0; j < memo.GetLength(1); j++)
            {
                memo[i, j] = -1;
            }
        }

        // DFS function to calculate best score based on current mask and position
        int Dfs(int mask, int currentPos)
        {
            if (mask == (1 << n) - 1) 
            return 0;

            if (memo[mask, currentPos] != -1) 
            return memo[mask, currentPos];

            bool isAlice = (System.Numerics.BitOperations.PopCount((uint)mask) % 2 == 0);
            int bestScore = isAlice ? 0 : int.MaxValue;

            // Loop through each pawn position
            for (int i = 0; i < n; i++)
            {
                if ((mask & (1 << i)) == 0)
                {
                    int nextPos = i + 1;
                    int moveCount = distances[currentPos, nextPos];

                    if (moveCount == int.MaxValue) 
                    continue;

                    // Calculate score and recursively call DFS for the next state
                    int score = moveCount + Dfs(mask | (1 << i), nextPos);
                    bestScore = isAlice ? Math.Max(bestScore, score) : Math.Min(bestScore, score);
                }
            }
            return memo[mask, currentPos] = bestScore;
        }
        return Dfs(0, 0);
    }

    // Knights possible movements on the chessboard using array declaration
    private static (int, int)[] KnightMoves = new (int, int)[]
    {
        (2, 1), (1, 2), (-1, 2), (-2, 1),
        (-2, -1), (-1, -2), (1, -2), (2, -1)
    };

    // Compute minimum moves from (sx, sy) to (tx, ty) using BFS
    private int ComputeMinMoves(int sx, int sy, int tx, int ty)
    {
        if (sx == tx && sy == ty) 
        return 0;

        var visited = new bool[50, 50];
        var queue = new Queue<(int, int)>();
        queue.Enqueue((sx, sy));
        visited[sx, sy] = true;
        int moves = 0;

        // Perform BFS to find the minimum moves
        while (queue.Count > 0)
        {
            int size = queue.Count;
            moves++;
            for (int i = 0; i < size; i++)
            {
                var (x, y) = queue.Dequeue();
                foreach (var (dx, dy) in KnightMoves)
                {
                    int nx = x + dx, ny = y + dy;

                    if (nx == tx && ny == ty) 
                    return moves;

                    // Enqueue valid positions that haven't been visited yet
                    if (nx >= 0 && nx < 50 && ny >= 0 && ny < 50 && !visited[nx, ny])
                    {
                        visited[nx, ny] = true;
                        queue.Enqueue((nx, ny));
                    }
                }
            }
        }
        return int.MaxValue;
    }
}
