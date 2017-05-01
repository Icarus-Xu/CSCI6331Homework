# Homework 6 & 7

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

a)

| y<sup>2</sup> | 1 | 4 | 9 | 16 | 25 | 36 | 49 | 64 | 81 |
|---------------|---|---|---|----|----|----|----|----|----|
|    mod 19     | 1 | 4 | 9 | 16 |  6 | 17 | 11 |  7 |  5 |

| y<sup>2</sup> | 100 | 121 | 144 | 169 | 196 | 225 | 256 | 289 | 324 |
|---------------|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|    mod 19     |  5  |  7  |  11 |  17 |  6  |  16 |  9  |  4  |  1  |

So the quadratic residues of Z<sub>19</sub> is 1, 4, 9, 16, 6, 17, 11, 7 and 5.

b)

* 5<sup>(7 - 1)/2</sup> = 6 mod p, so 5 is not a quadratic residue of 7
* 4<sup>(8 - 1)/2</sup> = 0 mod p, so 4 is not a quadratic residue of 8
* 6<sup>(15 - 1)/2</sup> = 6 mod 16, so 6 is a quadratic residue of 15
* 13<sup>(17 - 1)/2</sup> = 1 mod 17, so 13 is a quadratic residue of 17

**2**

* (2/45) = (2/3)<sup>2</sup>(2/5) = -1
* (109/385) = (385/109) = (58/109) = (2/109)(29/109) = -(109/29) = -(22/29) = -(2/29)(11/29) = (29/11) = (7/11) = (11/7) = (4/7) = (2/7)<sup>2</sup> = 1
* (1009/2307) = (2307/1009) = (289/1009) = (1009/289) = (142/289) = (71/289) = (289/71) = (5/71) = (71/5) = (1/5) = 1
* (2663/3299) = (3299/2663) = (636/2663) = (159/2663) = (2663/159) = (119/159) = (159/119) = (40/119) = (2/119)<sup>3</sup>(5/119) = (119/5) = (4/5) = (2/5)<sup>2</sup> = 1

**3**

a)

| a | 0 | 1 | 2 | 3 | 4 |
|---|---|---|---|---|---|
| 0 | 1 | 1 | 1 | 1 | 1 |
| 1 | 0 | 1 | 2 | 3 | 4 |
| 2 | 0 | 1 | 4 | 9 | 16 |
| 3 | 0 | 1 | 8 | 27 | 64 |
| 4 | 0 | 1 | 16 | 81 | 256 |

b)

| a | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| 0 | 1 | 1 | 1 | 1 | 1 | 1 |
| 1 | 0 | 1 | 2 | 3 | 4 | 5 |
| 2 | 0 | 1 | 4 | 9 | 16 | 25 |
| 3 | 0 | 1 | 8 | 27 | 64 | 125 |
| 4 | 0 | 1 | 16 | 81 | 256 | 625 |
| 5 | 0 | 1 | 32 | 243 | 1024 | 3125 |

**4**

|    element    | 1 | 2 | 3 | 4 | 5 | 6 |
|---------------|---|---|---|---|---|---|
| power 1 mod 7 | 1 | 2 | 3 | 4 | 5 | 6 |
| power 2 mod 7 | 1 | 4 | 2 | 2 | 4 | 1 |
| power 3 mod 7 | 1 | 1 | 6 | 1 | 6 | 6 |
| power 4 mod 7 | 1 | 2 | 4 | 4 | 2 | 1 |
| power 5 mod 7 | 1 | 4 | 5 | 2 | 3 | 6 |
| power 6 mod 7 | 1 | 1 | 1 | 1 | 1 | 1 |

So the primitive elements of Z<sub>7</sub> are 3 and 5

**5**

By looking at the chart in Problem 4, we see that the power of 4 does not generate all of the elements of Z<sub>7</sub>, so 4 is not a primitive element of Z<sub>7</sub>

**6**

* p = 23, &alpha; = 6, a = 6, k = 3
* &beta; = &alpha;<sup>a</sup> mod p = 6<sup>6</sup> mod 23 = 12
* (y<sub>1</sub>, y<sub>2</sub>) = (&alpha;<sup>a</sup> mod p, x&beta;<sup>k</sup> mod p) = (6<sup>3</sup> mod 23, 10 * 12<sup>3</sup> mod 23) = (9, 7)

**7**

* (y<sub>1</sub>, y<sub>2</sub>) = (6, 9), a = 8, k = 11
* x = y<sub>2</sub>(y<sub>1</sub><sup>a</sup>)<sup>-1</sup> mod p = 9 * (6<sup>8</sup>)<sup>-1</sup> mod 11 = 9 * 4 mod 11 = 3
