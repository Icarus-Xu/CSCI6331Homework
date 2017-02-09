# Homework 2

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

    int[] permutationResult = new int[32];
    int temp;
    for (int i = 0; i < 8; i++) {
        int row = exclusiveOrResult[i * 6] * 2 + exclusiveOrResult[i * 6 + 5];
        int column = exclusiveOrResult[i * 6 + 1] * 8 + exclusiveOrResult[i * 6 + 2] * 4 + exclusiveOrResult[i * 6 + 3] * 2 + exclusiveOrResult[i * 6 + 4];
        temp = S_BOX[i][row][column];
        System.out.print("*" + row + " " + column + "*" + temp + "# ");
        for (int j = 0; j < 4; j++) {
            permutationResult[4 * (i + 1) - j - 1] = temp % 2;
            temp /= 2;
        }
    }
    permutationResult = permutateText(permutationResult, P, permutationResult.length);
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

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 4*14# *0 14*5# *0 12*11# *1 0*13# *0 3*1# *2 1*14# *1 0*13# *2 10*10#
SR:1110 0101 1011 1101 0001 1110 1101 1010

Step 4.4: Permutate result of Step 4.3 with P
PR:1011 1110 1011 0010 1101 0011 0101 1101
LR: 11101000111110010000100011110000

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 00110110010110111101011011001011
R: 11101000111110010000100011110000

****************Round 2****************

K: 010100011111111100011011000110111101100111011101

Step 4.1: Extend R (32bit) to 48bit
E: 011101010001011111110010100001010001011110100001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001001 001110 100011 101001 100111 101100 111001 111100

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 4*14# *0 7*4# *3 1*10# *3 4*10# *3 3*7# *2 6*12# *3 12*14# *2 14*5#
SR:1110 0100 1010 1010 0111 1100 1110 0101

Step 4.4: Permutate result of Step 4.3 with P
PR:0011 0000 1101 0100 1000 1111 1111 1101
LR: 00000110100011110101100100110110

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11101000111110010000100011110000
R: 00000110100011110101100100110110

****************Round 3****************

K: 011001011011110111100011000000111101010110110101

Step 4.1: Extend R (32bit) to 48bit
E: 000000001101010001011110101011110010100110101100

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:011001 010110 100110 111101 101011 001111 110000 011001

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 12*9# *0 11*13# *2 3*9# *3 14*2# *3 5*14# *1 7*5# *2 8*10# *1 12*0#
SR:1001 1101 1001 0010 1110 0101 1010 0000

Step 4.4: Permutate result of Step 4.3 with P
PR:0000 0101 1100 1100 0110 0101 1001 1011
LR: 11101101001101010110110101101011

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 00000110100011110101100100110110
R: 11101101001101010110110101101011

****************Round 4****************

K: 111100111110110011000111110010110010110110100101

Step 4.1: Extend R (32bit) to 48bit
E: 111101011010100110101010101101011010101101010111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000001 100100 010101 101101 011111 101000 011011 110010

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 0*0# *2 2*7# *1 10*5# *3 6*13# *1 15*6# *2 4*2# *1 13*15# *2 9*6#
SR:0000 0111 0101 1101 0110 0010 1111 0110

Step 4.4: Permutate result of Step 4.3 with P
PR:1100 0110 0011 0111 0101 0100 1111 0001
LR: 11000000101110000000110111000111

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11101101001101010110110101101011
R: 11000000101110000000110111000111

****************End of loop****************

Step 5: Last permutation with IP reverse
T: 1100000010111000000011011100011111101101001101010110110101101011
CT:1010111100000011101011011001111000110000101110101100101111010001
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
7) Ciphered Text: 10101111 00000011 10101101 10011110 00110000 10111010 11001011 11010001
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

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 4*14# *0 14*5# *0 12*11# *1 0*13# *0 3*1# *2 1*14# *1 0*13# *2 10*10#
SR:1110 0101 1011 1101 0001 1110 1101 1010

Step 4.4: Permutate result of Step 4.3 with P
PR:1011 1110 1011 0010 1101 0011 0101 1101
LR: 11101000111110010000100011110000

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 00110110010110111101011011001011
R: 11101000111110010000100011110000

****************Round 2****************

K: 010100011111111100011011000110111101100111011101

Step 4.1: Extend R (32bit) to 48bit
E: 011101010001011111110010100001010001011110100001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001001 001110 100011 101001 100111 101100 111001 111100

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 4*14# *0 7*4# *3 1*10# *3 4*10# *3 3*7# *2 6*12# *3 12*14# *2 14*5#
SR:1110 0100 1010 1010 0111 1100 1110 0101

Step 4.4: Permutate result of Step 4.3 with P
PR:0011 0000 1101 0100 1000 1111 1111 1101
LR: 00000110100011110101100100110110

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11101000111110010000100011110000
R: 00000110100011110101100100110110

****************Round 3****************

K: 011001011011110111100011000000111101010110110101

Step 4.1: Extend R (32bit) to 48bit
E: 000000001101010001011110101011110010100110101100

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:011001 010110 100110 111101 101011 001111 110000 011001

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 12*9# *0 11*13# *2 3*9# *3 14*2# *3 5*14# *1 7*5# *2 8*10# *1 12*0#
SR:1001 1101 1001 0010 1110 0101 1010 0000

Step 4.4: Permutate result of Step 4.3 with P
PR:0000 0101 1100 1100 0110 0101 1001 1011
LR: 11101101001101010110110101101011

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 00000110100011110101100100110110
R: 11101101001101010110110101101011

****************Round 4****************

K: 111100111110110011000111110010110010110110100101

Step 4.1: Extend R (32bit) to 48bit
E: 111101011010100110101010101101011010101101010111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000001 100100 010101 101101 011111 101000 011011 110010

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 0*0# *2 2*7# *1 10*5# *3 6*13# *1 15*6# *2 4*2# *1 13*15# *2 9*6#
SR:0000 0111 0101 1101 0110 0010 1111 0110

Step 4.4: Permutate result of Step 4.3 with P
PR:1100 0110 0011 0111 0101 0100 1111 0001
LR: 11000000101110000000110111000111

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11101101001101010110110101101011
R: 11000000101110000000110111000111

****************Round 5****************

K: 011110011110011110010010111010100100101110011101

Step 4.1: Extend R (32bit) to 48bit
E: 111000000001010111110000000001011011111000001111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100110 011111 001001 100010 111011 111111 010110 010010

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*2 3*8# *1 15*5# *1 4*3# *2 1*6# *3 13*4# *3 15*13# *0 11*7# *0 9*9#
SR:1000 0101 0011 0110 0100 1101 0111 1001

Step 4.4: Permutate result of Step 4.3 with P
PR:0001 1110 1101 0100 0111 1100 0001 1100
LR: 11110011111000010001000101110111

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11000000101110000000110111000111
R: 11110011111000010001000101110111

****************Round 6****************

K: 011101001001010110111111010100110101001110011111

Step 4.1: Extend R (32bit) to 48bit
E: 111110100111111100000010100010100010101110101111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100011 101110 101010 111101 110110 010111 100000 110000

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*3 1*12# *2 7*1# *2 5*15# *3 14*2# *2 11*5# *1 11*14# *2 0*1# *2 8*0#
SR:1100 0001 1111 0010 0101 1110 0001 0000

Step 4.4: Permutate result of Step 4.3 with P
PR:0011 0110 1110 0101 1100 0001 0000 1100
LR: 11110110010111011100110011001011

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11110011111000010001000101110111
R: 11110110010111011100110011001011

****************Round 7****************

K: 111101111000000001010111110101110001000110101001

Step 4.1: Extend R (32bit) to 48bit
E: 111110101100001011111011111001011001011001010111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000011 010100 001010 101100 001100 101000 011111 111110

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 1*15# *0 10*2# *0 5*3# *2 6*7# *0 6*11# *2 4*2# *1 15*6# *2 15*8#
SR:1111 0010 0011 0111 1011 0010 0110 1000

Step 4.4: Permutate result of Step 4.3 with P
PR:1110 1101 1111 0000 1001 0110 1000 0110
LR: 00011110000100011000011111110001

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11110110010111011100110011001011
R: 00011110000100011000011111110001

****************Round 8****************

K: 001011111100101010110110110000100011101101101101

Step 4.1: Extend R (32bit) to 48bit
E: 100011111100000010100011110000001111111110100010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:101000 000000 101000 010101 000000 101100 010011 001111

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*2 4*13# *0 0*15# *2 4*8# *1 10*2# *0 0*2# *2 6*12# *1 9*3# *1 7*4#
SR:1101 1111 1000 0010 0010 1100 0011 0100

Step 4.4: Permutate result of Step 4.3 with P
PR:0101 0010 1100 1000 1100 0101 1011 1010
LR: 10100100100101010000100101110001

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 00011110000100011000011111110001
R: 10100100100101010000100101110001

****************Round 9****************

K: 111001011111011101001001011111011100010011000011

Step 4.1: Extend R (32bit) to 48bit
E: 110100001001010010101010100001010010101110100011

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001101 010110 001111 100011 111110 001110 111101 100000

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 6*13# *0 11*13# *1 7*10# *3 1*15# *2 15*14# *0 7*8# *3 14*3# *2 0*7#
SR:1101 1101 1010 1111 1110 1000 0011 0111

Step 4.4: Permutate result of Step 4.3 with P
PR:1001 0011 1100 1110 1101 1101 1111 0110
LR: 10001101110111110101101000000111

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 10100100100101010000100101110001
R: 10001101110111110101101000000111

****************Round 10****************

K: 010000111101111111000111100011101110010001001111

Step 4.1: Extend R (32bit) to 48bit
E: 110001011011111011111110101011110100000000001111

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100001 100110 000100 111001 001000 011010 010001 000000

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*3 0*15# *2 3*11# *0 2*9# *3 12*12# *0 4*7# *0 13*7# *1 8*14# *0 0*13#
SR:1111 1011 1001 1100 0111 0111 1110 1101

Step 4.4: Permutate result of Step 4.3 with P
PR:0110 1100 1011 1100 1111 1111 1110 1011
LR: 11001000001010011111011010011010

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 10001101110111110101101000000111
R: 11001000001010011111011010011010

****************Round 11****************

K: 011110011111100111010011101011101111011111000100

Step 4.1: Extend R (32bit) to 48bit
E: 011001010000000101010011111110101101010011110101

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000111 001111 100010 000000 010101 000010 001100 110001

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 3*4# *1 7*14# *2 1*6# *0 0*7# *1 10*15# *0 1*1# *0 6*8# *3 8*15#
SR:0100 1110 0110 0111 1111 0001 1000 1111

Step 4.4: Permutate result of Step 4.3 with P
PR:1110 1001 0100 1111 1011 1000 1011 0101
LR: 01100100100100001110001010110010

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 11001000001010011111011010011010
R: 01100100100100001110001010110010

****************Round 12****************

K: 001101011110010111101011101110001100011111100011

Step 4.1: Extend R (32bit) to 48bit
E: 001100001001010010100001011100000101010110100100

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:000001 010111 000101 001010 110010 001001 001001 000111

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 0*0# *1 11*10# *1 2*0# *0 5*6# *2 9*9# *1 4*7# *1 4*4# *1 3*8#
SR:0000 1010 0000 0110 1001 0111 0100 1000

Step 4.4: Permutate result of Step 4.3 with P
PR:0110 1001 0111 1000 0011 0000 0000 1000
LR: 10100001010100011100011010010010

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 01100100100100001110001010110010
R: 10100001010100011100011010010010

****************Round 13****************

K: 111100110100010110010111110111101100111000000011

Step 4.1: Extend R (32bit) to 48bit
E: 010100000010101010100011111000001101010010100101

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:101000 110110 111100 110100 001111 100001 101010 100110

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*2 4*13# *2 11*6# *2 14*14# *2 10*3# *1 7*1# *3 0*4# *2 5*3# *2 3*1#
SR:1101 0110 1110 0011 0001 0100 0011 0001

Step 4.4: Permutate result of Step 4.3 with P
PR:1110 0010 1100 0001 1000 1101 0001 1110
LR: 10000110010100010110111110101100

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 10100001010100011100011010010010
R: 10000110010100010110111110101100

****************Round 14****************

K: 011111011000101110010101110111100110011101011000

Step 4.1: Extend R (32bit) to 48bit
E: 010000001100001010100010101101011111110101011001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:001111 010100 100100 110111 011010 111001 101000 000001

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*1 7*1# *0 10*2# *2 2*4# *3 11*11# *0 13*0# *3 12*6# *2 4*12# *1 0*1#
SR:0001 0010 0100 1011 0000 0110 1100 0001

Step 4.4: Permutate result of Step 4.3 with P
PR:1100 0000 0111 0001 0000 1000 0100 1011
LR: 01100001001000001100111011011001

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 10000110010100010110111110101100
R: 01100001001000001100111011011001

****************Round 15****************

K: 000101111011000010111111101110011111001101001000

Step 4.1: Extend R (32bit) to 48bit
E: 101100000010100100000001011001011101011011110010

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:101001 111001 100110 111110 110111 000010 010110 111010

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*3 4*4# *3 12*0# *2 3*9# *2 15*4# *3 11*9# *0 1*1# *0 11*7# *2 13*3#
SR:0100 0000 1001 0100 1001 0001 0111 0011

Step 4.4: Permutate result of Step 4.3 with P
PR:0010 0111 0001 0010 1011 1101 0000 0000
LR: 10100001010000111101001010101100

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 01100001001000001100111011011001
R: 10100001010000111101001010101100

****************Round 16****************

K: 110010001110111111001101010011110000111011111101

Step 4.1: Extend R (32bit) to 48bit
E: 010100000010101000000111111010100101010101011001

Step 4.2: Extended R EXCLUSIVE_OR Ki
ER:100110 001100 010111 001010 101001 010101 101110 100100

Step 4.3: Permutate result of Step 4.2 with S_BOX
Pi:*2 3*8# *0 6*3# *1 11*14# *0 5*6# *3 4*1# *1 10*13# *2 7*14# *2 2*4#
SR:1000 0011 1110 0110 0001 1101 1110 0100

Step 4.4: Permutate result of Step 4.3 with P
PR:0111 0000 1101 0001 0111 0101 0010 1101
LR: 00010001111100011011101111110100

Step 4.5: L EXCLUSIVE_OR result of Step 4.4
Step 4.6: Assign R to L, and assign result of Step 4.5 to R
L: 10100001010000111101001010101100
R: 00010001111100011011101111110100

****************End of loop****************

Step 5: Last permutation with IP reverse
T: 0001000111110001101110111111010010100001010000111101001010101100
CT:1111010000101100000000110000011001011101100101110011100110011111
```
