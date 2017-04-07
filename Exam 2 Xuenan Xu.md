# Exam 2

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

**a)** Pr[2] = Pr[a] * Pr[K<sub>2</sub>] + Pr[b] * Pr[K<sub>1</sub>] + Pr[c] * Pr[K<sub>3</sub>] = 1/2 * 1/6 + 1/3 * 2/3 + 1/6 * 1/6 = 1/3

**b)** Pr[3] = Pr[a] * Pr[K<sub>3</sub>] + Pr[b] * Pr[K<sub>2</sub>] + Pr[c] * Pr[K<sub>1</sub>] = 1/2 * 1/6 + 1/3 * 1/6 + 1/6 * 2/3 = 1/4

**c)** Pr[b|3] = Pr[b] * Pr[K<sub>2</sub>] / Pr[3] = 1/3 * 1/6 / 1/4 = 2/9;

**d)** Pr[c|2] = Pr[c] * Pr[K<sub>3</sub>] / Pr[2] = 1/6 * 1/6 / 1/3 = 1/12;

**e)** H[K] = -(Pr[k1] * log<sub>2</sub>Pr[k1] + Pr[k2] * log<sub>2</sub>Pr[k2] + Pr[k3] * log<sub>2</sub>Pr[k3]) = -(2/3 * log<sub>2</sub>2/3 + 1/6 * log<sub>2</sub>1/6 + 1/6 * log<sub>2</sub>1/6) = 1/3log<sub>2</sub>3 - 1

**2**

Tool:
```java
static String autokeyCipher(String plainText, int key) {
    char[] p = plainText.toCharArray();
    StringBuilder sb = new StringBuilder();
    // For each plain char, its corresponding ciphered char is itself plus the key (mod 26)
    for (char c : p) {
        sb.append((char) ((c - 'A' + key) % 26 + 'A'));
        // The new key is the current plain text
        key = c - 'A';
    }
    return sb.toString();
}
```

Usage:
```java
public static void main(String[] args) {
    String plaintext = "AUTOKEYCIPHER";
    System.out.println(autokeyCipher(plaintext, 10));
}
```

Result:
```
KUNHYOCAKXWLV
```

**3**

Tools:
```java
static String permutationCipher(String text, int[] pattern) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i <= text.length() / pattern.length; i++) {
        // Each time of inner loop is of length of the pattern
        for (int j : pattern) {
            // Compute the offset of current char
            int offset = i * pattern.length + j - 1;
            // Add to result
            if (offset < text.length())
                sb.append(text.charAt(offset));
        }
    }
    return sb.toString();
}
```

```java
static String vigenereCipher(String text, String key) {
    StringBuilder sb = new StringBuilder();
    int keyLength = key.length();
    int[] equivalent = new int[keyLength];
    // Convert key into Z26
    for (int i = 0; i < key.length(); i++)
        equivalent[i] = key.charAt(i) - 'A';
    // Add key to plain text
    for (int i = 0; i < text.length(); i++)
        sb.append((char) ((text.charAt(i) - 'A' + equivalent[i % keyLength]) % 26 + 'A'));
    return sb.toString();
}
```

Usage:
```java
public static void main(String[] args) {
    String plaintext = "STREAMCIPHER";
    int[] pattern = {2, 3, 1, 4};
    String key = "USETHISKEY";
    // Permutation first, then vigenere
    System.out.println(vigenereCipher(permutationCipher(plaintext, pattern), key));
}
```

Result:
```
NJWXTKSSLCJJ
```

**4**

**a)** Degree is m = 5

**b)** Period is 2<sup>m</sup>  - 1= 2<sup>5</sup> - 1= 31

**c)** First 5 bit is `11010`

**d)**

Code:
```java
public static void main(String args[]) {
    int[] result = new int[15];
    // Initialization
    result[0] = 1;
    result[1] = 1;
    result[2] = 0;
    result[3] = 1;
    result[4] = 0;
    for (int i = 5; i < result.length; i++)
        // Another form of the linear recurrence formula
        result[i] = (result[i - 5] + result[i - 4]) % 2;
    for (int r : result)
        System.out.print(r);
}
```

Result:
```
110100111010011
```
