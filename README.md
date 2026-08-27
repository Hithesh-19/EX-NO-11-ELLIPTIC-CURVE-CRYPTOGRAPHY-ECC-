# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.


## Program:
```C
#include <stdio.h>

// Structure for a point
typedef struct
{
    int x;
    int y;
    int is_infinity;
} Point;

const int a = 2;
const int b = 3;
const int p = 17;

// Modular inverse
int mod_inverse(int k, int p)
{
    k = k % p;

    if (k < 0)
        k += p;

    for (int x = 1; x < p; x++)
    {
        if ((k * x) % p == 1)
            return x;
    }

    return -1;
}

// Mod function
int mod(int value, int p)
{
    value = value % p;

    if (value < 0)
        value += p;

    return value;
}

// Point addition
Point point_addition(Point P, Point Q)
{
    Point R;

    if (P.is_infinity)
        return Q;

    if (Q.is_infinity)
        return P;

    int lambda;
    int inverse;

    // Point doubling
    if (P.x == Q.x && P.y == Q.y)
    {
        if (P.y == 0)
        {
            R.is_infinity = 1;
            return R;
        }

        inverse = mod_inverse(2 * P.y, p);

        lambda = mod(
            (3 * P.x * P.x + a) * inverse,
            p
        );
    }
    else
    {
        // P + Q
        if (P.x == Q.x)
        {
            R.is_infinity = 1;
            return R;
        }

        inverse = mod_inverse(Q.x - P.x, p);

        lambda = mod(
            (Q.y - P.y) * inverse,
            p
        );
    }

    R.x = mod(lambda * lambda - P.x - Q.x, p);

    R.y = mod(
        lambda * (P.x - R.x) - P.y,
        p
    );

    R.is_infinity = 0;

    return R;
}

// Scalar multiplication
Point scalar_multiplication(Point P, int n)
{
    Point R;

    R.x = 0;
    R.y = 0;
    R.is_infinity = 1;

    while (n > 0)
    {
        if (n & 1)
        {
            R = point_addition(R, P);
        }

        P = point_addition(P, P);

        n = n >> 1;
    }

    return R;
}

int main()
{
    Point G = {5, 1, 0};

    int n = 7;

    printf("Base point G: (%d, %d)\n", G.x, G.y);

    Point R = scalar_multiplication(G, n);

    if (R.is_infinity)
    {
        printf("Result of %d * G: Point at Infinity\n", n);
    }
    else
    {
        printf("Result of %d * G: (%d, %d)\n",
               n, R.x, R.y);
    }

    return 0;
}
```



## Output:
<img width="1028" height="701" alt="Screenshot 2026-08-27 140315" src="https://github.com/user-attachments/assets/6f668fea-3b3b-49e5-aea0-25d538b5d956" />




## Result:
The program is executed successfully

