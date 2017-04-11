# Homework 5

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

a) &Phi;(19) = 18

b) &Phi;(1073) = &Phi;(29 * 37) = 28 * 36 = 1008

c) &Phi;(30030) = &Phi;(2 * 3 * 5 * 7 * 11 * 13) = 1 * 2 * 4 * 6 * 10 * 12 = 5760

d) &Phi;(2436) = &Phi;(2 * 2 * 3 * 7 * 29) = 1 * 1 * 2 * 6 * 28 = 336

**2**

* We have `pq = 14647` and `(p - 1)(q - 1) = 14400`.
* According to `pq = 14647`, `p = *7` and `q = *1` or `p = *9` and `q = *3` so that their product ends with `7`. But when `p = *9` and `q = *3`, `p - 1 = *8` and `q - 1 = *2` so that `(p - 1)(q - 1) = *6` which contradict with `(p - 1)(q - 1) = 14400`, so we have `p = *7` and `q = *1`.
* We now look at `p - 1 = *6` and `q - 1 = *0`, so `q - 1` must be a multiplicative of `100` as we have 2 `10`s as factors of 14400 and `p - 1 = *6` will never have a `10` as its factor. Now we need to divide the remaining `144` to one number ends with `6` and another any number, the 2 possibilities are `6 * 24` and `36 * 4` which means `p - 1 = 6` and `q - 1 = 2400` or `p - 1 = 36` and `q - 1 = 400`.
* In the case of `p - 1 = 6` and `q - 1 = 2400`, `q = 2401 = 7 * 7 * 7 * 7`, not a prime number. So `p - 1 = 36` and `q - 1 = 400`, that is `p = 37` and `q = 401`.

**3**

* `BESTWISHES` = 0104 1819 2208 1807 0418
* &Phi;(2669) = &Phi;(17 * 157) = 16 * 156 = 2496
* I wrote a calculator to calculate A<sup>B</sup> mod C when A, B, C are relatively large numbers

```java
private static int calculator(int A, int B, int C) {
    int result = A % C;
    for (int i = 1; i < B; i++)
        result = result * A % C;
    return result;
}
```

* Use converted string and the given `b = 3` and `n = 2669` as parameters of the calculator

```java
public static void main(String args[]) {
    System.out.println(calculator(104, 3, 2669));
    System.out.println(calculator(1819, 3, 2669));
    System.out.println(calculator(2208, 3, 2669));
    System.out.println(calculator(1807, 3, 2669));
    System.out.println(calculator(418, 3, 2669));
}
```

* We have

```
1215
1224
1471
23
116
```

* So the ciphered message shall be `1215 1224 1471 0023 0116`

**4**

* &Phi;(2747) = &Phi;(41 * 67) = 2640
* We need to calculate the multiplicative inverse of 13, with b = 13 and &Phi;(n) = 2640

| LOOP |  a0  |  b0  |  t0  |  t  |  q  |  r  | temp |
|------|------|------|------|-----|-----|-----|------|
|  0   | 2640 |  13  |  0   |  1  | 203 |  1  | -203 |
|  1   |  13  |  1   |  1   |2437 | 13  |  0  | null |

* So `a = 2437`

* Use the calculator in Problem 3 on the given string

```java
public static void main(String args[]) {
    System.out.println(calculator(2206, 2437, 2747));
    System.out.println(calculator(755, 2437, 2747));
    System.out.println(calculator(436, 2437, 2747));
    System.out.println(calculator(1165, 2437, 2747));
    System.out.println(calculator(1737, 2437, 2747));
}
```

* We have

```
617
404
1908
1306
1823
```

* By looking at the chart we got `GREETINGSX`
