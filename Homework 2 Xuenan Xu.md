# Homework 1

##### by Xuenan Xu GWID G26980825
&nbsp;

I have to say that DES is a quite complicate encrypt method, which involved a lot of calculation and randomization, and by studying how DES works I finally understood that there is no 'smart' way to crack a DES code because every key will generate 16 different subkeys and with the exclusive-or process, the connection between data like duplicate or frequency is hidden because the encryption works not only on the plain text, but also depends on previous result of the encryption loop, so I guess the only way to crack that is to try all possible keys and get a proper result.

I wrote a program to implement the DES process, demonstrated below, the result of the problems will be at the end of the document. To shorten the document, I deleted all tracking outputs in the code, but they will still be in the final outputs.

## Tool Functions

**Permutation**

```java
private static int[] permutateText(int[] source, int[] table, int length) {
    int[] permutationResult = new int[length];
    for (int i = 0; i < length; i++) {
        permutationResult[i] = source[table[i] - 1];
    }
    return permutationResult;
}
```

**Exclusive Or**

```java
private static int[] exclusiveOr(int[] array_1, int[] array_2, int length) {
    int[] result = new int[length];
    for (int i = 0; i < length; i++)
        result[i] = (array_1[i] + array_2[i]) % 2;
    return result;
}
```

**Shift array**

```java
private static int[] keyShift(int[] key, int offset) {
    int[] newKey = new int[56];
    for (int i = 0; i < 28 - offset; i++) {
        newKey[i] = key[i + offset];
        newKey[28 + i] = key[28 + i + offset];
    }
    for (int i = 28 - offset; i < 28; i++) {
        newKey[i] = key[i + offset - 28];
        newKey[28 + i] = key[i + offset];
    }
    return newKey;
}
```

## Encrypt Process Components

**Key calculation**

```java
private static int[][] getKeys(int[] K0) {
    int[][] keySet = new int[16][48];
    int[] tempKey = new int[56];
    for (int i = 0; i < 16; i++) {
        // Step 2.1: Divide and shift
        tempKey = keyShift(i == 0 ? K0 : tempKey,
                (i == 0 || i == 1 || i == 8 || i == 15) ? 1 : 2);
        // Step 2.2: Compress to 48 bit
        int[] newKey = permutateText(tempKey, PC_2, PC_2.length);
        System.arraycopy(newKey, 0, keySet[i], 0, 48);
    }
    return keySet;
}
```

**Function f**

```java
private static int[] f(int[] R, int[] Ki) {
    int[] extendedR = permutateText(R, EXTEND, EXTEND.length);
    int[] exclusiveOrResult = exclusiveOr(extendedR, Ki, extendedR.length);

    // Step 4.3: Permutate result of Step 3.3 with S_BOX
    int[] permutationResult = new int[32];
    int temp;
    for (int i = 0; i < 8; i++) {
        int row = exclusiveOrResult[i * 6] * 2 + exclusiveOrResult[i * 6 + 5];
        int column = exclusiveOrResult[i * 6 + 1] * 8 +
                    exclusiveOrResult[i * 6 + 2] * 4 +
                    exclusiveOrResult[i * 6 + 3] * 2 +
                    exclusiveOrResult[i * 6 + 4];
        temp = S_BOX[i][row][column];
        for (int j = 0; j < 4; j++) {
            permutationResult[4 * (i + 1) - j - 1] = temp % 2;
            temp /= 2;
        }
    }

    return permutationResult;
}
```

## Encryption process

```java
public static void main(String[] args) {
    // Step 1: Permutate plain text with IP
    int[] plainTextPermutationResult = permutateText(PLAIN_TEXT, IP, IP.length);

    // Step 2: Get Ki
    int[] keyPermutationResult = permutateText(KEY, PC_1, PC_1.length);
    int[][] keys = getKeys(keyPermutationResult);
    int[] L = new int[32];
    int[] R = new int[32];
    int[] temp;

    // Step 3: Initialize L and R
    System.arraycopy(plainTextPermutationResult, 0, L, 0, 32);
    System.arraycopy(plainTextPermutationResult, 32, R, 0, 32);

    // Step 4: Encryption Loop
    for (int i = 0; i < 16; i++) {
        // Step 4.4: L EXCLUSIVE_OR result of Step 4.3
        temp = exclusiveOr(L, f(R, keys[i]), L.length);
        // Step 4.5: Assign R to L, and assign result of Step 4.4 to R
        System.arraycopy(R, 0, L, 0, 32);
        System.arraycopy(temp, 0, R, 0, 32);
    }
    // Step 5: Last permutation with IP reverse
    int[] cipheredText = new int[64];
    System.arraycopy(R, 0, cipheredText, 0, 32);
    System.arraycopy(L, 0, cipheredText, 32, 32);

    cipheredText = permutateText(cipheredText, IP_1, IP_1.length);

    for (int element : cipheredText) System.out.print(element);
}
```

> This program is fully functional, which means that by simply change the limitation of the loop, the program can handle any number of rounds of the encryption process.

## Required Result of 4 Loops
```
Step 1: Permutate plain text with IP
X: 0101011001001011110110111010110100110110010110111101011011001011
Step 2: Get Ki
Step 3: Initialize L and R
L: 01010110010010111101101110101101
R: 00110110010110111101011011001011

Step 4: Loop with f

****************Round 1****************

K: 101111110000010011110110111100001111011000100010

Step 4.1: Extend R (32bit) to 48bit
E: 100110101100001011110111111010101101011001010110

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001001 011100 011000 000001 000110 100010 000001 110100

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*1 4*14# *0 14*5# *0 12*11# *1 0*13# *0 3*1# *2 1*14# *1 0*13# *2 10*10#
PR:1110 0101 1011 1101 0001 1110 1101 1010
LR: 10110011111101101100010101110111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00110110010110111101011011001011
R: 10110011111101101100010101110111

****************Round 2****************

K: 010100011111111100011011000110111101100111011101

Step 4.1: Extend R (32bit) to 48bit
E: 110110100111111110101101011000001010101110101111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100010 111000 000010 110110 011110 110111 001001 110010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 1*1# *2 12*9# *0 1*0# *2 11*14# *0 15*9# *3 11*7# *1 4*4# *2 9*6#
PR:0001 1001 0000 1110 1001 0111 0100 0110
LR: 00101111010101010100000110001101

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 10110011111101101100010101110111
R: 00101111010101010100000110001101

****************Round 3****************

K: 011001011011110111100011000000111101010110110101

Step 4.1: Extend R (32bit) to 48bit
E: 100101011110101010101010101000000011110001011010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:111100 000101 011101 001001 101000 111110 100111 101111

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 14*5# *1 2*4# *1 14*15# *1 4*6# *2 4*10# *2 15*6# *3 3*8# *3 7*13#
PR:0101 0100 1111 0110 1010 0110 1000 1101
LR: 11100111000000000110001111111010

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00101111010101010100000110001101
R: 11100111000000000110001111111010

****************Round 4****************

K: 111100111110110011000111110010110010110110100101

Step 4.1: Extend R (32bit) to 48bit
E: 011100001110100000000000001100000111111111110101

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100000 110000 010011 000111 111110 110101 001001 010000

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 0*4# *2 8*5# *1 9*8# *1 3*5# *2 15*14# *3 10*1# *1 4*4# *0 8*10#
PR:0100 0101 1000 0101 1110 0001 0100 1010
LR: 01101010110100001010000011000111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 11100111000000000110001111111010
R: 01101010110100001010000011000111

****************End of loop****************

Step 5: Last permutation with IP reverse
T: 0110101011010000101000001100011111100111000000000110001111111010
CT:1000100111001011100000010100001000010010110011101101101110010111
```

## Homework Answer

```
1) K = 1000111 0111000 1100111 0011100 1110011 0011110 1001111 1111000
2) C0 = 1000111 0111000 1100111 0011100
   D0 = 1110011 0011110 1001111 1111000
3) K1 = 10111111 00000100 11110110 11110000 11110110 00100010
   K2 = 01010001 11111111 00011011 00011011 11011001 11011101
   K3 = 01100101 10111101 11100011 00000011 11010101 10110101
   K4 = 11110011 11101100 11000111 11001011 00101101 10100101
4) X0 = 01010110 01001011 11011011 10101101 00110110 01011011 11010110 11001011
5) L0 = 01010110 01001011 11011011 10101101
   R0 = 00110110 01011011 11010110 11001011
6) Please refer to 'L' and 'R' field in each round in the previous tracking details
7) Ciphered Text: 10001001 11001011 10000001 01000010 00010010 11001110 11011011 10010111
```

## Final Result of 16 Rounds

```
Step 1: Permutate plain text with IP
X: 0101011001001011110110111010110100110110010110111101011011001011
Step 2: Get Ki
Step 3: Initialize L and R
L: 01010110010010111101101110101101
R: 00110110010110111101011011001011

Step 4: Loop with f

****************Round 1****************

K: 101111110000010011110110111100001111011000100010

Step 4.1: Extend R (32bit) to 48bit
E: 100110101100001011110111111010101101011001010110

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001001 011100 011000 000001 000110 100010 000001 110100

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*1 4*14# *0 14*5# *0 12*11# *1 0*13# *0 3*1# *2 1*14# *1 0*13# *2 10*10#
PR:1110 0101 1011 1101 0001 1110 1101 1010
LR: 10110011111101101100010101110111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00110110010110111101011011001011
R: 10110011111101101100010101110111

****************Round 2****************

K: 010100011111111100011011000110111101100111011101

Step 4.1: Extend R (32bit) to 48bit
E: 110110100111111110101101011000001010101110101111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100010 111000 000010 110110 011110 110111 001001 110010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 1*1# *2 12*9# *0 1*0# *2 11*14# *0 15*9# *3 11*7# *1 4*4# *2 9*6#
PR:0001 1001 0000 1110 1001 0111 0100 0110
LR: 00101111010101010100000110001101

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 10110011111101101100010101110111
R: 00101111010101010100000110001101

****************Round 3****************

K: 011001011011110111100011000000111101010110110101

Step 4.1: Extend R (32bit) to 48bit
E: 100101011110101010101010101000000011110001011010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:111100 000101 011101 001001 101000 111110 100111 101111

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 14*5# *1 2*4# *1 14*15# *1 4*6# *2 4*10# *2 15*6# *3 3*8# *3 7*13#
PR:0101 0100 1111 0110 1010 0110 1000 1101
LR: 11100111000000000110001111111010

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00101111010101010100000110001101
R: 11100111000000000110001111111010

****************Round 4****************

K: 111100111110110011000111110010110010110110100101

Step 4.1: Extend R (32bit) to 48bit
E: 011100001110100000000000001100000111111111110101

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100000 110000 010011 000111 111110 110101 001001 010000

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 0*4# *2 8*5# *1 9*8# *1 3*5# *2 15*14# *3 10*1# *1 4*4# *0 8*10#
PR:0100 0101 1000 0101 1110 0001 0100 1010
LR: 01101010110100001010000011000111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 11100111000000000110001111111010
R: 01101010110100001010000011000111

****************Round 5****************

K: 011110011110011110010010111010100100101110011101

Step 4.1: Extend R (32bit) to 48bit
E: 101101010101011010100001010100000001011000001110

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:110011 001011 000100 110011 101110 100101 110110 010011

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 9*11# *1 5*2# *0 2*9# *3 9*4# *2 7*8# *3 2*2# *2 11*8# *1 9*5#
PR:1011 0010 1001 0100 1000 0010 1000 0101
LR: 01010101100101001110000101111111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 01101010110100001010000011000111
R: 01010101100101001110000101111111

****************Round 6****************

K: 011101001001010110111111010100110101001110011111

Step 4.1: Extend R (32bit) to 48bit
E: 101010101011110010101001011100000010101111111110

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:110111 100010 100100 010110 001000 110111 100001 100001

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 11*14# *2 1*14# *2 2*4# *0 11*5# *0 4*7# *3 11*7# *3 0*6# *3 0*2#
PR:1110 1110 0100 0101 0111 0111 0110 0010
LR: 10000100100101011101011110100101

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 01010101100101001110000101111111
R: 10000100100101011101011110100101

****************Round 7****************

K: 111101111000000001010111110101110001000110101001

Step 4.1: Extend R (32bit) to 48bit
E: 110000001001010010101011111010101111110100001011

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001101 110001 010011 111100 001111 011110 110010 100010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*1 6*13# *3 8*11# *1 9*8# *2 14*8# *1 7*1# *0 15*11# *2 9*15# *2 1*11#
PR:1101 1011 1000 1000 0001 1011 1111 1011
LR: 10001110000111001111101010000100

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 10000100100101011101011110100101
R: 10001110000111001111101010000100

****************Round 8****************

K: 001011111100101010110110110000100011101101101101

Step 4.1: Extend R (32bit) to 48bit
E: 010001011100000011111001011111110101010000001001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:011010 100000 101001 001111 101111 010110 111101 100100

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*0 13*9# *2 0*0# *3 4*6# *1 7*3# *3 7*13# *0 11*4# *3 14*3# *2 2*4#
PR:1001 0000 0110 0011 1101 0100 0011 0100
LR: 00010100111101100000001110010001

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 10001110000111001111101010000100
R: 00010100111101100000001110010001

****************Round 9****************

K: 111001011111011101001001011111011100010011000011

Step 4.1: Extend R (32bit) to 48bit
E: 100010101001011110101100000000000111110010100010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:011011 110110 000011 100101 011111 011011 100001 100001

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*1 13*5# *2 11*6# *1 1*7# *3 2*0# *1 15*6# *1 13*11# *3 0*6# *3 0*2#
PR:0101 0110 0111 0000 0110 1011 0110 0010
LR: 11011000011011001001000111100110

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00010100111101100000001110010001
R: 11011000011011001001000111100110

****************Round 10****************

K: 010000111101111111000111100011101110010001001111

Step 4.1: Extend R (32bit) to 48bit
E: 011011110000001101011001010010100011111100001101

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001011 001101 110010 011110 110001 001101 101101 000010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*1 5*2# *1 6*8# *2 9*1# *0 15*15# *3 8*6# *1 6*9# *3 6*10# *0 1*2#
PR:0010 1000 0001 1111 0110 1001 1010 0010
LR: 00111100111010010110101000110011

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 11011000011011001001000111100110
R: 00111100111010010110101000110011

****************Round 11****************

K: 011110011111100111010011101011101111011111000100

Step 4.1: Extend R (32bit) to 48bit
E: 100111111001011101010010101101010100000110100110

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:111001 100110 111010 000001 000110 111011 011001 100010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 12*10# *2 3*11# *2 13*10# *1 0*13# *0 3*1# *3 13*0# *1 12*2# *2 1*11#
PR:1010 1011 1010 1101 0001 0000 0010 1011
LR: 01110011110000011000000111001101

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00111100111010010110101000110011
R: 01110011110000011000000111001101

****************Round 12****************

K: 001101011110010111101011101110001100011111100011

Step 4.1: Extend R (32bit) to 48bit
E: 101110100111111000000011110000000011111001011010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100011 111001 101111 101000 011110 001111 100110 111001

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 1*12# *3 12*0# *3 7*7# *2 4*12# *0 15*9# *1 7*5# *2 3*13# *3 12*3#
PR:1100 0000 0111 1100 1001 0101 1101 0011
LR: 11111100100101011111111111100000

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 01110011110000011000000111001101
R: 11111100100101011111111111100000

****************Round 13****************

K: 111100110100010110010111110111101100111000000011

Step 4.1: Extend R (32bit) to 48bit
E: 011111111001010010101011111111111111111100000001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100011 001101 000100 111100 001000 010011 000100 000010

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 1*12# *1 6*8# *0 2*9# *2 14*8# *0 4*7# *1 9*1# *0 2*2# *0 1*2#
PR:1100 1000 1001 1000 0111 0001 0010 0010
LR: 10111011010110011111000011101111

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 11111100100101011111111111100000
R: 10111011010110011111000011101111

****************Round 14****************

K: 011111011000101110010101110111100110011101011000

Step 4.1: Extend R (32bit) to 48bit
E: 110111110110101011110011111110100001011101011111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:101000 101110 000101 100110 001001 000111 000000 000111

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*2 4*13# *2 7*1# *1 2*0# *2 3*0# *1 4*4# *1 3*2# *0 0*4# *1 3*8#
PR:1101 0001 0000 0000 0100 0010 0100 1000
LR: 00101101100101011011110110101000

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 10111011010110011111000011101111
R: 00101101100101011011110110101000

****************Round 15****************

K: 000101111011000010111111101110011111001101001000

Step 4.1: Extend R (32bit) to 48bit
E: 000101011011110010101011110111111011110101010000

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000000 100000 110000 010100 011001 100100 111000 011000

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*0 0*14# *2 0*0# *2 8*11# *0 10*8# *1 12*3# *2 2*15# *2 12*0# *0 12*5#
PR:1110 0000 1011 1000 0011 1111 0000 0101
LR: 01011011111000011100111111101010

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 00101101100101011011110110101000
R: 01011011111000011100111111101010

****************Round 16****************

K: 110010001110111111001101010011110000111011111101

Step 4.1: Extend R (32bit) to 48bit
E: 001011110111111100000011111001011111111101010100

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:111001 111001 000011 001110 101010 101111 000110 101001

Step 4.3: Permutate result of Step 3.3 with S_BOX
Pi:*3 12*10# *3 12*0# *1 1*7# *0 7*10# *2 5*13# *3 7*10# *0 3*14# *3 4*4#
PR:1010 0000 0111 1010 1101 1010 1110 0100
LR: 10001101111011110110011101001100

Step 4.4: L EXCLUSIVE_OR result of Step 3.4
Step 4.5: Assign R to L, and assign result of Step 3.5 to R
L: 01011011111000011100111111101010
R: 10001101111011110110011101001100

****************End of loop****************

Step 5: Last permutation with IP reverse
T: 1000110111101111011001110100110001011011111000011100111111101010
CT:1111110010011110010111011101101110000000001101101011111101111010
```
