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

H[C] = -(Pr[1] * log<sub>2</sub>Pr[1] + Pr[2] * log<sub>2</sub>Pr[2] + Pr[3] * log<sub>2</sub>Pr[3]) = -(5/12 * log<sub>2</sub>5/12 + 1/3 * log<sub>2</sub>1/3 + 1/4 * log<sub>2</sub>1/4) = 3/4log<sub>2</sub>3 + 2/3log<sub>2</sub>4 - 5/12log<sub>2</sub>5

**3**

Tools:
```java
static String multiplicativeDecipher(String cipheredText, int key) {
    // Initialize multiplicative inverse map
    HashMap<Integer, Integer> inverse = new HashMap<>();
    inverse.put(1, 1);
    inverse.put(3, 9);
    inverse.put(5, 21);
    inverse.put(7, 15);
    inverse.put(9, 3);
    inverse.put(11, 19);
    inverse.put(15, 7);
    inverse.put(17, 23);
    inverse.put(19, 11);
    inverse.put(21, 5);
    inverse.put(23, 17);
    inverse.put(25, 25);
    // Convert string to chars
    char[] p = cipheredText.toCharArray();
    StringBuilder sb = new StringBuilder();
    // Multiply each char with the multiplicative inverse of key, then mod 26
    for (char c : p)
        sb.append((char) (((c - 'A') * inverse.get(key)) % 26 + 'A'));
    return sb.toString();
}
```
```java
static String permutationDecipher(String cipheredText, int[] pattern) {
    int[] p = new int[pattern.length];
    // Compute the reverse of π
    for (int i = 0; i < pattern.length; i++)
        p[pattern[i] - 1] = i + 1;
    // Reverse the permutation process by permutate with the reverse of π
    return SimpleCiphers.permutationCipher(cipheredText, p);
}
```
```java
static String autokeyDecipher(String cipheredText, int key) {
    char[] c = cipheredText.toCharArray();
    StringBuilder sb = new StringBuilder();
    // For each ciphered char, its corresponding plain char is itself minus the key (mod 26)
    for (char p : c) {
        // The new key is the plain text just decrypted
        key = (p - 'A' - key + 26) % 26;
        sb.append((char) (key + 'A'));
    }
    return sb.toString();
}
```

Decipher of the described cryptosystem:
```java
public static void main(String[] args) {
    String cipheredText = "RXJNJPOEHLZGFPBV";
    String decipherS2 = multiplicativeDecipher(cipheredText, 5);
    System.out.println(decipherS2);
    int[] pattern = {2, 3, 1, 4};
    String decipherS3 = permutationDecipher(decipherS2, pattern);
    System.out.println(decipherS3);
    String plainText = autokeyDecipher(decipherS3, 5);
    System.out.println(plainText);
}
```

Result:
```
CRYPTOPRODUCTIVE
```

**4**

Code:
```java
public static void main(String args[]) {
    int[] result = new int[18];
    // Initialization
    result[0] = 1;
    result[1] = 1;
    result[2] = 0;
    result[3] = 1;
    result[4] = 0;
    result[5] = 1;
    for (int i = 6; i < result.length; i++)
        // Another form of the linear recurrence formula
        result[i] = (result[i - 6] + result[i - 4]) % 2;
    for (int r : result)
        System.out.print(r);
}
```
Result:
```
110101100011101101
```

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
