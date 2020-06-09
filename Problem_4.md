---
layout: page
---

# Problem 4

On a N x N grid of cells, each cell (x, y) with 0 <= x < N and 0 <= y < N has a lamp.

Initially, some number of lamps are on.  `lamps[i]` tells us the location of the i-th lamp that is on.  Each lamp that is on illuminates every square on its x-axis, y-axis, and both diagonals (similar to a Queen in chess).

For the i-th query `queries[i]` = (x, y), the answer to the query is 1 if the cell (x, y) is illuminated, else 0.

After each query (x, y) \[in the order given by queries], we turn off any lamps that are at cell (x, y) or are adjacent 8-directionally (ie., share a corner or edge with cell (x, y).)

Return an array of answers.  Each value `answer[i]` should be equal to the answer of the i-th query `queries[i]`.

Example:
Input: N = 5, lamps = [[0,0],[4,4]], queries = [[1,1],[1,0]]
Output: [1,0]
Explanation: 
Before performing the first query we have both lamps [0,0] and [4,4] on.
The grid representing which cells are lit looks like this, where [0,0] is the top left corner, and [4,4] is the bottom right corner:

```
1 1 1 1 1
1 1 0 0 1
1 0 1 0 1
1 0 0 1 1
1 1 1 1 1
```

Then the query at [1, 1] returns 1 because the cell is lit.  After this query, the lamp at [0, 0] turns off, and the grid now looks like this:

```
1 0 0 0 1
0 1 0 0 1
0 0 1 0 1
0 0 0 1 1
1 1 1 1 1
```

Before performing the second query we have only the lamp [4,4] on.  Now the query at [1,0] returns 0, because the cell is no longer lit.

Note:

```java
1 <= N <= 10^9
0 <= lamps.length <= 20000
0 <= queries.length <= 20000
lamps[i].length == queries[i].length == 2
```

[https://leetcode.com/problems/grid-illumination/](https://leetcode.com/problems/grid-illumination/)

### Python solution by @FractalBach

```python
from typing import List

LAMP_CHAR = 'Ｏ'
LIT_CHAR = '＊'
EMPTY_CHAR = '＿'
# fixed width space = '　'


class Solution:
	def gridIllumination(self, N: int, lamps: List[List[int]], queries: List[List[int]]) -> List[int]:
		self.N = N
		self.lampCols = {}
		self.lampRows = {}
		self.lampUpDiagonals = {}
		self.lampDownDiagonals = {}
		self.queries = {}
		results = []
		for x, y in lamps:
			self.addLamp(x, y)
		for x, y in queries:
			results += [self.query(x, y)]
		return results


	def query(self, x, y):
		ret = self.isIlluminated(x, y)
		self.removeEightLamps(x, y)
		if ret:
			return 1
		return 0


	def addLamp(self, x, y):
		self.lampCols[x] = y
		self.lampRows[y] = True
		self.lampUpDiagonals[y + x] = True
		self.lampDownDiagonals[y - x] = True


	def isLampAt(self, x, y):
		ly = self.lampCols.get(x)
		return ly == y and y is not None


	def removeLamp(self, x, y):
		if not self.isLampAt(x, y):
			return
		self.lampCols.pop(x, None)
		self.lampRows.pop(y, None)
		self.lampUpDiagonals.pop(y + x, None)
		self.lampDownDiagonals.pop(y - x, None)


	def isIlluminated(self, x, y):
		return \
		(
			   x in self.lampCols
			or y in self.lampRows
			or y + x in self.lampUpDiagonals
			or y - x in self.lampDownDiagonals
		)


	def removeEightLamps(self, x, y):
		for k in range(x-1, x+2):
			for j in range(y-1, y+2):
				self.removeLamp(k, j)


	def dangerousPrint(self):
		if self.N > 70:
			print("you don't want to print this grid.")
			return
		for y in range(N):
			s = ""
			for x in range(N):
				if self.isLampAt(x, y):
					s += LAMP_CHAR
				elif self.isIlluminated(x, y):
					s += LIT_CHAR
				else:
					s += EMPTY_CHAR
			print(s)


if __name__ == "__main__":
	N = 20
	LAMPS = [[5,10], [15,5]]
	QUERIES = [[4,9], [5,5]]
	s = Solution()
	result = s.gridIllumination(N, LAMPS, QUERIES)
	s.dangerousPrint()
	print(f"result = {result}")
```

### Python solution by @kishan

```python
class Point():
    def __init__(self, coordinates):
        self.x, self.y = coordinates

    def __eq__(this, that):
        return this.x == that.x and this.y == that.y


class Line():
    def __init__(self, a, b):
        self.a = Point(a)
        self.b = Point(b)

    def collinear(self, c):
        # http://bit-player.org/wp-content/extras/bph-publications/BeautifulCode-2007-Hayes.pdf
        return (self.b.x - self.a.x) * (c.y - self.a.y) == \
               (c.x - self.a.x) * (self.b.y - self.a.y)


class LampLayer():
    def __init__(self, n, l):
        self.n = n
        self.l = Point(l)

    def check_3x3(self, c):
        return \
           self.l == Point((c.x-1, c.y-1)) or self.l == Point((c.x, c.y-1)) or self.l == Point((c.x+1, c.y-1)) or \
           self.l == Point((c.x-1, c.y)) or self.l == Point((c.x, c.y)) or self.l == Point((c.x+1, c.y)) or \
           self.l == Point((c.x-1, c.y+1)) or self.l == Point((c.x, c.y+1)) or self.l == Point((c.x+1, c.y+1))

    def check_horizontal(self, c):
        return self.l.y == c.y

    def check_vertical(self, c):
        return self.l.x == c.x

    def check_diagonals(self, c):
        negative_diagonal_min = (self.l.x-self.n, self.l.y-self.n)
        negative_diagonal_max = (self.l.x+self.n, self.l.y+self.n)
        negative_diagonal = Line(negative_diagonal_min, negative_diagonal_max)

        positive_diagonal_min = (self.l.x+self.n, self.l.y-self.n)
        positive_diagonal_max = (self.l.x-self.n, self.l.y+self.n)
        positive_diagonal = Line(positive_diagonal_min, positive_diagonal_max)

        return negative_diagonal.collinear(c) or negative_diagonal.collinear(c)


class LampGrid():
    def __init__(self, n, lamps):
        self.n = n
        self.layers = self.generate_lamp_layers(lamps)

    def generate_lamp_layers(self, lamps):
        layers = {}
        for lamp in lamps:
            layers[tuple(lamp)] = LampLayer(self.n, lamp)
        return layers

    def query(self, query_coordinates):
        lamp_on = 0
        c = Point(query_coordinates)

        expired_lamps = list()
        for lamp_key, layer in self.layers.items():
            if layer.check_horizontal(c) or \
                layer.check_vertical(c) or \
                layer.check_diagonals(c):
                lamp_on = 1

            if layer.check_3x3(c):
                expired_lamps.append(lamp_key)

        for lamp_key in expired_lamps:
            del self.layers[lamp_key]

        return lamp_on


def batch_query(n, lamps, queries):
    """
    >>> batch_query(n = 5, lamps = [[0,0],[4,4]], queries = [[1,1],[1,0]])
    [1, 0]
    >>> batch_query(n = 5, lamps = [[0,0],[2,0],[4,0]], queries = [[2,0],[2,0]])
    [1, 1]
    """
    grid = LampGrid(n, lamps)
    output = list()
    for c in queries:
        result = grid.query(c)
        output.append(result)
    return output
```
