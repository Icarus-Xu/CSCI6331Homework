# Exam 2

##### by Xuenan Xu GWID G26980825
&nbsp;

**1**

**a)** Pr[2] = Pr[a] * Pr[K<sub>2</sub>] + Pr[b] * Pr[K<sub>1</sub>] + Pr[c] * Pr[K<sub>3</sub>] = 1/2 * 1/6 + 1/3 * 2/3 + 1/6 * 1/6 = 1/3

**b)** Pr[3] = Pr[a] * Pr[K<sub>3</sub>] + Pr[b] * Pr[K<sub>2</sub>] + Pr[c] * Pr[K<sub>1</sub>] = 1/2 * 1/6 + 1/3 * 1/6 + 1/6 * 2/3 = 1/4

**c)** Pr[b|3] = Pr[b] * Pr[K<sub>2</sub>] / Pr[3] = 1/3 * 1/6 / 1/4 = 2/9;

**d)** Pr[c|2] = Pr[c] * Pr[K<sub>3</sub>] / Pr[2] = 1/6 * 1/6 / 1/3 = 1/12;

**e)** H[K] = -(Pr[k1] * log<sub>2</sub>Pr[k1] + Pr[k2] * log<sub>2</sub>Pr[k2] + Pr[k3] * log<sub>2</sub>Pr[k3]) = -(2/3 * log<sub>2</sub>2/3 + 1/6 * log<sub>2</sub>1/6 + 1/6 * log<sub>2</sub>1/6) = log<sub>2</sub>3 - 1/3 = 1.2516

**2**

* Take the first character `A`, the key is 10, so we calculate `A + 10 mod 26` and got `K`, and use `A` as the new key
* Take the second character `U`, the key is `A` which equals to `0`, so we calculate `U + 0 mod 26 = U` and use `U` as the new key
* Repeat this process, we have `KUNHYOCAKXWLV` as result

**3**

Given the formula e<sub>1</sub>(&pi;(M)), we shall permutate the string with &pi; = (2 3 1 4) first and then use the vigenere cipher with key `USETHISKEY` on the result.

1. Permutation
    * Take the first 4 characters `STRE`
    * Put the 2nd char to 1st, 3rd char to 2nd, 1st char to 3rd and 4th char to 4th
    * We get `TRSE`
    * Repeat this process we have `TRSEMCAIHEPR` as result
2. Vigenere cipher
    * The length of key is 10, so we take the first 10 characters of previous result `TRSEMCAIHE`
    * Add the character of the key to the corresponding position of the result, for example, for the 1st position, we calculate `T + U mod 26` and get `N`, then for the 2nd position, we calculate `R + S mod 26` and get `J`
    * Repeat this process on the 10 taken characters, we have `NJWXTKSSLC`
    * Repeat the whole process above on the previous result string, we got `NJWXTKSSLCJJ` as final result

**4**

**a)** Degree is m = 5

**b)** Period is 2<sup>m</sup>  - 1= 2<sup>5</sup> - 1= 31

**c)** First 5 bit is `11010`

**d)**

1. The formula given can be converted into **z<sub>i</sub> = z<sub>i - 5</sub> + z<sub>i - 4</sub> mod 2**
2. We have initial stream **11010**
    * When we calculate the 6th number, we take the sum of 1st and 2nd number, mod 2, and get **0**
    * Repeat this process, we have result **110100111010011**
