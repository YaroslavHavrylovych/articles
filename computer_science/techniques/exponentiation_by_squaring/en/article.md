# Exponentiation by squaring

While solving one of the tasks at [InterviewBit](https://www.interviewbit.com) (I don't remember the exercise itself),
I saw a magical (and unknown to me) exponentiation method that worked in logarithmic time. 
When I threw away the magical part, the method was very exquisite and clear.

Here is the [code itself](https://github.com/YaroslavHavrylovych/dsa/blob/development/algorithms/math/exponentiation_by_squaring/java/Exponentiation.java). If everything is clear to you, then you can skip further reading:

```java
long pow(long val, long exp) {
    long res = 1;
    while(exp != 0) {
        if((exp & 1) == 1) res *= val;
        val *= val;
        exp >>= 1;
    }
    return res;
}
```

**The concrete problem's statement**: exponentiate `x` to `n`, where x=[1,2,3,...], n = [0,1,2,3,....]

Let's warm up the memory a little bit and start with the standard [linear time](https://en.wikipedia.org/wiki/Time_complexity#Linear_time) exponentiation procedure which follows the [definition](https://en.wikipedia.org/wiki/Exponentiation). Exempli gratia:

```
x^n=x*x*x*....*x (n разів)
```

We want to reduce the multiplications amount (x\*x) because this is a continuously repeated operation.
To achieve this, we use an essential exponentiation's property:

```mathematica
x^(a+b)=(x^a)*(x^b).
```

We want to present `n` as the sum of related numbers (multiplication), then `x^a` and `x^b` will have a common part.

We use the fact that any number can be written as the sum of powers of two since it is actually a number representation in the [binary number system](https://en.wikipedia.org/wiki/Binary_number). Let's take `177` as an example:

```mathematica
117=64+32+16+4+1=2^6+2^5+2^4+2^2+2^0. 
```

Denote by a factor of zero the degrees that are missing and those that are present then denote with the factor of 1: `1*2^6+1*2^5+1*2^4+0*2^3+1*2^2+0*2^1+1*2^0`.

```mathematica
117=1*2^6+1*2^5+1*2^4+0*2^3+1*2^2+0*2^1+1*2^0=1*n6+1*n5+1*n4+0*n3+1*n2+0*n1+1*n0,
n0=1 (res+=n0)
n1=n0+n0
n2=n1+n1 (res+=n2)
n3=n2+n2
...
```

The sum operation is replaced with multiplication when exponent decreases `x^32=x^16*x^16`,
and  `x^64=x^(32+32)=x^32*x^32` і etc. Each successive number is always twice the previous number and accordingly includes it in the product.  
Each multiplier at `n[i]` is always 0 or 1 from the binary form of the number  (`117` in binary form is` 1110101`).

At this point, it should be obvious that if we multiply `x` by itself, and multiply the result by those 
values of `x` that have 1 in the binary form of the number, then we achieve our `x^n`.
But we achieve this much faster: we can perform `x^117` with the cost of ten multiplication operations instead of 117 expected.

Let's gaze at the [code](https://github.com/YaroslavHavrylovych/dsa/blob/development/algorithms/math/exponentiation_by_squaring/java/Exponentiation.java) once again:

```java
long pow(long x, long n) {
    long res = 1; //the multiplication result 
    while(n != 0) {
        if((n & 1) == 1) res *= val; //x moves to the result
        x *= x; //x = x*x - for the next iteration
        n >>= 1; //division by 2 (achieved using the bit operation)
    }
    return res;
}
```

Of course, we could use any other base than 2,
but the convenience of binary is that numbers, in most programming languages,
are either stored in binary format or have a convenient, fast conversion method.