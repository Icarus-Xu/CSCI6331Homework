# Midterm Exam

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

Frequency distribution:

| P | C | S | U | R | V | G | H | D | E | Z | M | O | K | X | N | L | I | F | T |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 2 | 8 | 4 | 6 | 3 | 4 | 2 | 1 | 1 | 6 | 2 | 2 | 3 | 1 | 2 | 1 | 1 | 1 | 1 |

+ First we notice there is a word `H`, and the only character that forms a word itself is `a`;
+ `S` has a noticeable high frequency than other characters, I guess it's `e`;
+ Three-character word `VCS` ends with `e`, the most frequent word of this is `the`, that is `V` is `t` and `h` stands for `C`;
+ `RV` ends with `t`, so we know that `R` is `i`;
+ `RV RG H` has been like `it i* a`, so `G` could be `s`;
+ `GHOS` is `sa*e` now, so `O` shall be `m`;
+ `DESGVRZU` looks like `**esti**`, the first word came to my mind is `question`, so `D` is `q`, `E` is `u`, `Z` is `o` and `U` is `n`;
+ `ZM` is a two-character word going after `question` and starts with `o`, the best match shall be `question of`, that is `f` represents `M`;
+ `OZUSK` is now `mone*`, so `K` has a great possibility of `y`;
+ I would split `SXSNKLZIK` into two parts, `SXSNK` which looks like `e*e*y` and `LZIK` which looks like `*o*y`, so `SXSNK` shall be `every` and thus `LZIK` could be `body`;
+ `PCSU` is a four-character word like `*hen`, the most frequent ones are `when` and `then`, so in this case `P` shall be `w`;
+ `NSFRTRZU` contains the remaining two undecided characters, which are `F` and `T`, I did some word search because I couldn't recognize the pattern at first, and the first result is `religion` that quite makes sense;

Mapping:

| P | C | S | U | R | V | G | H | D | E | Z | M | O | K | X | N | L | I | F | T |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| W | H | E | N | I | T | S | A | Q | U | O | F | M | Y | V | R | B | D | L | G |

Result:

`When it is a question of money, everybody is of the same religion. -Voltaire`

**2**

**a)** Pr[1] = Pr[a] * Pr[K<sub>1</sub>] + Pr[b] * Pr[K<sub>3</sub>] + Pr[c] * Pr[K<sub>2</sub>] = 1/2 * 2/3 + 1/3 * 1/6 + 1/6 * 1/6 = 5/12

**b)** Pr[b|1] = Pr[b] * Pr[K<sub>3</sub>] / Pr[1] = 1/3 * 1/6 / 5/12 = 2/15;

**c)** Pr[c|1] = Pr[c] * Pr[K<sub>2</sub>] / Pr[1] = 1/6 * 1/6 / 5/12 = 1/15;

**d)**

Pr[2] = Pr[a] * Pr[K<sub>2</sub>] + Pr[b] * Pr[K<sub>1</sub>] + Pr[c] * Pr[K<sub>3</sub>] = 1/2 * 1/6 + 1/3 * 2/3 + 1/6 * 1/6 = 1/3

Pr[3] = 1 - Pr[1] - Pr[2] = 1/4

H[C] = -(Pr[1] * log<sub>2</sub>Pr[1] + Pr[2] * log<sub>2</sub>Pr[2] + Pr[3] * log<sub>2</sub>Pr[3]) = -(5/12 * log<sub>2</sub>5/12 + 1/3 * log<sub>2</sub>1/3 + 1/4 * log<sub>2</sub>1/4) = 3/4log<sub>2</sub>3 + 2/3log<sub>2</sub>4 - 5/12log<sub>2</sub>5 = 1.5546

**3**

According to the cipher process e<sub>2</sub>(&pi;(e<sub>1</sub>(M))), we shall use the multiplicative decipher with key `5` to decrypt the string first, and permutate the result with &pi; = (2 3 1 4), finally decipher the result with autokey decipher with key `5`.

1. First, use the multiplicative decipher to decrypt the string **RXJNJPOEHLZGFPBV**
    * The reverse of `5` is `21`
    * Starting with R, we calculate `R * 21 mod 26`, and we have `T`
    * Repeat this process, and we get **TPHNHDIGRXFWBDVZ**
2. Second, use the permutation decipher with &pi; = (2 3 1 4) to decrypt the result
    * Take the first 4 characters **TPHN**, put the 2nd char at 1st, 3rd char at 2nd, 1st char at 3rd and 4th at 4th, we have **HTPN**
    * Repeat this process, and we get **HTPNIHDGFRXWVBDZ**
3. Finally, use the autokey decipher to decrypt the result
    * Take the first character **H**, the key is 5, so we calculate `H - 5 mod 26` and get `C`, and use `C` as the new key
    * Repeat this process, we have **CRYPTOPRODUCTIVE** as final result

**4**

1. The formula given can be converted into **z<sub>i</sub> = z<sub>i - 6</sub> + z<sub>i - 4</sub> mod 2**
2. We have initial stream **110101**
    * When we calculate the 7th number, we take the sum of 1st and 3rd number, mod 2, and get **1**
    * Repeat this process, we have result **110101100011101101**

**5**

**a)**

| Key Length Bits  | 128 | 192 | 256 |
|------------------|-----|-----|-----|
| Key Length Bytes | 16  | 24  | 32  |
| # Rounds         | 10  | 12  | 14  |

**b)**

| r0 | r1 | r2 | r3 |
|----|----|----|----|
| 12 | 67 | AB | EF |
| 23 | 78 | BC | 1A |
| 45 | 89 | CD | 2B |
| 56 | 9A | DE | 3C |

**c)**

| r0 | r1 | r2 | r3 |
|----|----|----|----|
| 12 | 67 | AB | EF |
| 78 | BC | 1A | 23 |
| CD | 2B | 45 | 89 |
| 3C | 56 | 9A | DE |

**d)**

| r0 | r1 | r2 | r3 |
|----|----|----|----|
| C9 | 85 | 62 | DF |
| BC | 65 | A2 | 26 |
| BD | F1 | 6E | A7 |
| EB | B1 | B8 | 1D |

**e)**

| r0 | r1 | r2 | r3 |
|----|----|----|----|
| 85 | 62 | DF | C9 |
| 65 | A2 | 26 | BC |
| F1 | 6E | A7 | BD |
| B1 | B8 | 1D | EB |
