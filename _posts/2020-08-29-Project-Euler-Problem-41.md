
Here's a cute problem from [Project Euler](https://projecteuler.net/):

> **Problem 41: Pandigital Primes**  
We shall say that an n-digit number is pandigital if it makes use of all the digits 1 to n exactly once. For example, 2143 is a 4-digit pandigital and is also prime.  
What is the largest n-digit pandigital prime that exists?

There are a few pieces to solving this problem:
1. Creating a sequence of primes
2. Filtering out the primes that are pandigital
3. Getting the largest one.

This works, but it can take a huge amount of time.
So here's my take for solving this problem in 26 miliseconds.


### Creating a sequence of primes

Creating a sequence of numbers is easy.  
Creating a sequence of primes, however, requires identifying which of those numbers are primes and filtering them out.
So let's make a function to identify if a number is a prime.  
One could simply check if this numbers has any divisors. But that takes time, especially for large numbers.  

Fortunately, a little thought can make this problem much easier.  
For example, the only even number that is prime is the number 2.  
Therefore, for numebrs greater than 2, we don't really need to check even numbers. Just odd numbers.  
We've just reduced the amount of work by 50%.  

Another insight is that we don't need to search for divisors all the way up to the number we're checking.  
We only need to go all the way up to it's square root.  

Take number 21, for instance. It's square root is slightly over 4.  
We know that 21 is divisible by 7, which is greater than the square root of 21.  
Indeed, 21 divided by 7 is 3.  
But this means that 21 is also divisible by 3, which is less than the square root of 21.  
Knowing that 21 is divisible by 3 is enough to show that it's not a prime. We don't need to go all the way to number 7.

In short, if some prime factor greater than the square root exists, then a prime factor smaller than the square root must also exist. This is a brilliant insight that the Babylonians used to calculate square roots in an amazingly simple and efficient method (considering the technology they had available).

The takeaway for our purposes is that we don't need to check for any divisors above the square root of a number to know wheather it's prime or not.  

This significantly reduces the amount of calculation we have to do.  
So let's write that in a function:



```python
def is_prime(n):
    
    """
    Checks if an integer is a prime.
    
    Parameters:
    -----------
    n : int
        The integer we wish to check if it is prime.
    
    Returns:
    -------
    bool.
        True if n is prime. 
        False if it isn't
    
    Notes:
    ------
    
    This function first checks if n is an odd number greater than 2.
    If it is, then n is not prime and False is returned.
    
    Otherwise, we check for the existence of a number m that can divide n.
    We only check for m up to the square root of n (rounder upwards to the nearest integer).
    This is because if n is divisible by a number >= sqrt(n), then it is also divisible by a number <= sqrt(n).
    If a divisor for n is found, then n is not prime, and False is returned.
    
    Otherwise, n is prime and True is return.
    
    By this approach, 1 is considered a prime, which it is not.
    However, we are not concerned with this particular case.
    
    Examples:
    ---------
    >>>is_prime(12)
    False
    
    >>>is_prime(17)
    True
    
    >>>is_prime(7919)
    True
    
    """
    import numpy as np
    
    #If n is even, then n is not prime, unless it is 2.
    if (n != 2) & (n % 2 == 0):
        return(False)
    
    else:
        
        #If n is odd, we'll have to check if any number m can divide n
        
        #We only need to check divisors up to the square root of n.
        #Therefore, the largest candidate for divisors of n is sqrt(n), rounded upwards:
        largest_candidate = int(np.ceil(np.sqrt(n)))
        
        #Test if each number up to sqrt(n) divides n.
        #We only test odd numbers, so we start at 3 and advance 2 steps each turn, doing 3, 5, 7, etc.
        for m in range(3, largest_candidate + 1, 2):
            #If m divides n,...
            if n % m == 0:
                #... then n is not a prime.
                return(False)
    
    #If no number up to the largest candidate divides n,
    #then n is a prime.
    return(True)
```

Let's test our functions in some use cases:


```python
assert is_prime(2)
assert is_prime(3)
assert is_prime(5)
assert is_prime(7)
assert is_prime(11)
assert not is_prime(20)
assert not is_prime(49)
assert not is_prime(60)
assert is_prime(7919) #the 1,000-th prime number
assert is_prime(1000003) #an even bigger prime number
```

Our function got all answers correctly.  
It's also quite fast, considering it's been written in Python:


```python
#%%timeit
is_prime(99999999977) #This number is a prime
```

    28.4 ms ± 1.82 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
    

We can now use this function to generate a series of prime numbers.

Recall the description of the Problem:

> What is the **largest** n-digit pandigital prime that exists?

So we don't want to have an entire list stored in memory. We only want one value. 
It therefore makes sense to create a *generator* of primes, rather than a list of primes.  
Furthermore, since we're interested on the largest value, it makes sense to generate these primes in decreasing order.  
Therefore, we won't have to generate all the numbers untill we get to our answer.  
Our answer will simply be the first number produced by our generator.  
So let's create a generator of primen numbers in decreasing order.  
We'll make it decreasing from some number $n$. That is, we're goinf to generate all prime numbers smaller than (or equal to) $n$, in decreasing order:


```python
def primes(n):
    
    """
    A generator for the first prime numbers in decreasing order.
    
    This function generates all prime numbers up to a certain value (not necessarily prime).
    
    Parameters:
    -----------
    n : int
        We wish to generate all prime numbers until this value.
        Hence, primes(n) generates all prime numbers smaller or equal to n.
        
    Yields:
    --------
    int:
        A prime number smaller or equal to n.
        
    Examples:
    ---------
    >>> list(primes(10))
    [7, 5, 3, 2]
    """
    
    #For all numbers, starting at n, and going down...
    for i in range(n, 1, -1):
        #If it is a prime, yield it.
        #Otherwise, ignore it.
        if is_prime(i):
            yield(i)
```

The function itself is quite short: consider all numbers, starting at $n$ and going down. If the number is prime, yield it. If not, ignore it. Yielding means I don't have to actually calculate all prime numbers before I return them. I'll only calculate them as I need them, and if I need them. Since numbers are in decreasing order, I'll only need to generate one number. That is much faster than generating all numbers and then filtering them. 

But, just to visualize what we could have *if* we wanted to generate all the numbers, here are all the prime numbers from 1 to 10:


```python
%%time
list(primes(10))
```

    Wall time: 0 ns
    




    [7, 5, 3, 2]



### Filtering pandigital numbers

What is a pandigital number?

We'll have to teach our computer to recognize one, won't we?

Python has this very cool feature that a string is essentally a list of characters.
This means if the transform a number (say, 1234) into a string ('1234'), we can simply ask: "Hey, is '3' in there?". We'll have our answer! So let's do that:


```python
def is_pandigital(n):
    
    """
    Checks if a number is pandigital.
    A pandigital number that contains all the smallest natural numbers (except zero) in some order.
    
    Parameters:
    -----------
    n : int
        The number we wish to check if it is a pandigital.
    
    Results:
    --------
    bool:
        True, if the number is a pandigital.
        False, if it isn't.
    
    
    """
    
    #Transform n into a string
    n = str(n)
    
    #Initialize the output as being true.
    output = True
    
    #Start with the smallest of positive natural numbers, i = 1.
    i = 1
    
    #We expect to find all digits, 1, 2, 3,... all untill the number of digits n has.
    #So we run the algorithm until we have spanned all digits of n,
    #or until we find a digit that is missing, in which case the output becomes False and we return it.
    while (i <= len(n)) and output:
        
        #If digit i is found among the digits of n,
        #we continue believing the number is a palyndrome.
        #If not, the output becomes false and we return it.
        output = (str(i) in n)
        
        #Move to next digit
        i += 1

    return(output)
```

Let's check if it works:


```python
## Some tests:
assert is_pandigital(1234) #small number with digits in correct order
assert is_pandigital(2431) #small number with digits in random order
assert is_pandigital(987654321) #large number with digits in descending order
assert is_pandigital(123456789) #large number with digits in correct order
assert is_pandigital(456123789)  #large number with digits in random order
assert not is_pandigital(123455) #a non-pandigital number. One digit is repeated
assert not is_pandigital(123457) #a non-pandigital number. One number is skipped a number
```

It works! :)

Now let's combine this function with our generator of prime numbers to create a generator for numbers that are both prime and pandigital. This is what we're really concerned with. We want to make it a generator, and to have it generate pandigital primes in descending order, for the same reasons we're discussed previously.  

Here's how the generator will work:  

It will generate prime numbers using the prime numbers generator we created above.  
For every generated prime, if it is pandigital, it will be yielded.  
If it is not pandigital, it will be ignored.  


```python
def pandigital_primes(n):
    
    """
    Generates pandigital primes up to a number n in decreasing order.
    A pandigital prime is a number that is both prime and pandigital.
    
    Parameters:
    -----------
    n : int
        We wish to generate all pandigital primes until this value.
        Hence, pandigital_primes(n) generates all pandigital primes smaller than or equal to n.
    
    Yields:
    -------
    int
    A pandigital prime number smaller or equal to n.

    Examples:
    ---------
    >>> list(pandigital_primes(10000))
    [4231, 2341, 2143, 1423]
    
    """
    
    #For all primes in the list of primes smaller than n...
    for p in primes(n):
        #... if it is pandigital...
        if is_pandigital(p):
            #yield it.
            yield(p)
```

Let's check if our generator works using the example given in the project description:

> For example, 2143 is a 4-digit pandigital and is also prime. 

We'll do this by asking: "What's the largest pandigital prime smaller than 2144?". We should get 2143 as our answer...


```python
next(pandigital_primes(2144))
```




    2143



### Getting the largest pan digital number

Now that we can generate pandigital prime numbers in descending order, all we have to do is generate the first element of this sequence. 

What should our starting point be?  
No pandigital number can be larger than $987654321$, so this looks like a good starting point.

I did that, and the code took for ages to run. 
That seemed strange.  
Our code seemed to be working pretty well up to this point.  
We identified some pretty large prime numbers.  So why is our code taking so long, and how do we make it faster?

Let's look at number $987654321$.
Is it a prime? No it isn't. It's divisible by 3:


```python
987654321 % 3 == 0
```




    True



A number is divisible by 3 when all it's digits add up to a number that is itself divisible by 3.  
$987654321$, for instance, adds up to 45:


```python
sum_of_digits = 0
for d in '987654321':
    sum_of_digits += int(d)
sum_of_digits
```




    45



But the sum of the digits is not affected by the order in which these digits appear.  
Therefore, **no 9-digit pandigital number is prime**.

The same is true for 8-digits pandigital numbers.
They add up to $45 - 9 = 36$, which is also divisible by $3$!

This brings us down to 7-digits numbers. 
These add up to $36 - 8 = 28$, so they may be divisible by $3$. We'll need to check.

Here's when we'll the functions we've developped above.  
But we'll only have to consider numbers that have at most 7 digits.
This is a significant reduction from considering numebrs that could have 8 or 9 digits.

The greatest pandigital number with 7 digits is $7654321$ (much smaller than $987654321$).
So let's start from there.

We have a generator of pandigital primes.
It will generate pandigital primes in descending order, starting from $7654321$.
The first number generated will be the largest pandigital prime possible:


```python
next(pandigital_primes(7654321))
```




    7652413



Is it fast?


```python
%%timeit
next(pandigital_primes(7654321))
```

    26.1 ms ± 1.08 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
    
