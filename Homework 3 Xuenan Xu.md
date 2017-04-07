# Homework 3

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

Given encryption function table:

|               |    @    |    %    |
| ------------- | ------- | ------- |
| K<sub>1</sub> | &alpha; | &beta;  |
| K<sub>2</sub> | &beta;  | &gamma; |
| K<sub>3</sub> | &gamma; | &delta; |

and meta probabilities:

_Pr[@] = 1/4, Pr[%] = 3/4_

_Pr[K<sub>1</sub>] = 1/2, Pr[K<sub>2</sub>] = Pr[K<sub>3</sub>] = 1/4_

We have:

_a) Pr[&alpha;] = Pr[@] * Pr[k<sub>1</sub>] = 1/8_

_b) Pr[&beta;] = Pr[@] * Pr[k<sub>2</sub>] + Pr[%] * Pr[k<sub>1</sub>] = 7/16_

_c) Pr[&gamma;] = Pr[@] * Pr[k<sub>3</sub>] + Pr[%] * Pr[k<sub>2</sub>] = 1/4_

_d) Pr[&delta;] = Pr[%] * Pr[k<sub>3</sub>] = 3/16_

_e) Pr[@ | &alpha;] = Pr[@ &cap; K<sub>1</sub>] / Pr[&alpha;] = (1/4 * 1/2) / (1/8) = 1_

_f) Pr[@ | &beta;] = Pr[@ &cap; K<sub>2</sub>] / Pr[&beta;] = (1/4 * 1/4) / (7/16) = 1/7_

_g) Pr[@ | &gamma;] = Pr[@ &cap; K<sub>3</sub>] / Pr[&gamma;] = (1/4 * 1/4) / (1/4) = 1/4_

_h) Pr[@ | &delta;] = 0_ because if we have &delta; as ciphertext, the plaintext can never be @

_i) Pr[% | &alpha;] = 0_ because if we have &alpha; as ciphertext, the plaintext can never be %

_j) Pr[% | &beta;] = Pr[% &cap; K<sub>1</sub>] / Pr[&beta;] = (3/4 * 1/2) / (7/16) = 6/7_

_k) Pr[% | &gamma;] = Pr[% &cap; K<sub>2</sub>] / Pr[&gamma;] = (3/4 * 1/4) / (1/4) = 3/4_

_l) Pr[% | &delta;] = Pr[% &cap; K<sub>3</sub>] / Pr[&delta;] = (3/4 * 1/4) / (3/16) = 1_

**2**

Given string:

`GPFXAZ RHLMDI FACLML LCMXKY JOHYCJ ZYHTBI
`

and according to description, it's the product of a permutation cipher with pattern:

`π = (2 5 1 4 6 3)`

and a vigenere with key:

`USETHISKEY`

So to decipher this string, we shall reverse the vigenere cipher first, using the following function:

```java
private static String vigenereDecipher(String cipheredText, String key) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < cipheredText.length(); i++)
        // Vigenere Cipher is "plaintext plus key", so we do in its reverse way,
        // "ciphertext minus key", by adding 26 to the result we can avoid the
        // appearance of negative number
        sb.append((char) ((cipheredText.charAt(i) - key.charAt(i % key.length()) + 26) % 26 + 65));
    return sb.toString();
}
```

Then we shall reverse the permutation process, which equals to another permutation with &pi;<sup>-1</sup>:

```java
private static String permutationDecipher(String cipheredText, int[] pattern) {
    int[] p = new int[pattern.length];
    // Compute the reverse of π
    for (int i = 0; i < pattern.length; i++)
        p[pattern[i] - 1] = i + 1;
    // Reverse the permutation process by permutate with the reverse of π
    return SimpleCiphers.permutationCipher(cipheredText, p);
}
```

The decipher process is demonstrated below:

```java
public static void main(String[] args) {
    String text = "GPFXAZRHLMDIFACLMLLSMXKYJOHYCJZYHTBI";
    String key = "USETHISKEY";
    int[] pattern = {2, 5, 1, 4, 6, 3};
    System.out.println(vigenereDecipher(permutationDecipher(text, pattern), key));
}
```

and the result is(after divided into words):

```
LOVE IS THE ONLY MEDICINE FOR A BROKEN HEART
```

**3**

Assume S<sub>1</sub> is a multiplicative cipher:

`m(x) = (k * x) mod 26`

and S<sub>2</sub> is a affine cipher:

`a(x) = (a * x + b) mod 26`

So the product of S<sub>1</sub> and S<sub>2</sub> is:

`e(x) = m(a(x)) = (k * ((a * x + b) mod 26)) mod 26 = (k * a * x + k * b) mod 26`

and the product of S<sub>2</sub> and S<sub>1</sub> is:

`e'(x) = a(m(x)) = (a * ((k * x) mod 26) + b) mod 26 = (k * a * x + b) mod 26`

Only when `b = k * b`, `e(x) = e'(x)`, which means the result is commutative. To let `b = k * b`, `b = 0` or `k = 1`.

+ Consider `b = 0`, makes `b = k * b` for any `k`, but in this case, `a(x) = (a * x) mod 26`, which is not an affine cipher, but a multiplicative cipher instead, so it does not apply to the result.
+ Consider `k = 1`, makes `b = k * b` for any `b`, but in this case, `m(x) = x mod 26`, which is not even a cipher, so it does not apply to the result as well.

To summarize, in the only 2 cases that may let the product of S<sub>1</sub> and S<sub>2</sub> be commutative, S<sub>1</sub> or S<sub>2</sub> has been another kind of cipher or not even being a cipher, which means that the product of any valid multiplicative and affine cipher can not be commutative.
