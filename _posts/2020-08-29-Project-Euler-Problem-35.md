
Prime numbers are cool, but circular primes are awesome!

A number is a circular prime if all its circular permutations are prime.  For example, the circular permutations of $179$ are $917$, $791$ and $179$ itself. They're all prime! So $179$ is a circular prime.

You can get the feeling that circular primes aren't that common...

How many circular primes are there from 1 to 1 million?

That's Project Euler's [problem number 35](https://projecteuler.net/problem=35):

>The number, 197, is called a circular prime because all rotations of the digits: 197, 971, and 719, are themselves prime.
There are thirteen such primes below 100: 2, 3, 5, 7, 11, 13, 17, 31, 37, 71, 73, 79, and 97.
How many circular primes are there below one million?

We begin by creating a function to identify if a number is prime. This is the same function I used in [another post]():


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
    We only check for m up to the square root of n 
    (rounded upwards to the nearest integer).
    This is because if n is divisible by a number >= sqrt(n), 
    then it is also divisible by a number <= sqrt(n).
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
        #Therefore, the largest candidate for divisors of n is sqrt(n),
        #rounded upwards:
        largest_candidate = int(np.ceil(np.sqrt(n)))
        
        #Test if each number up to sqrt(n) divides n.
        #We only test odd numbers, 
        #so we start at 3 and advance 2 steps each turn, 
        #doing 3, 5, 7, etc.
        for m in range(3, largest_candidate + 1, 2):
            #If m divides n,...
            if n % m == 0:
                #... then n is not a prime.
                return(False)
    
    #If no number up to the largest candidate divides n,
    #then n is a prime.
    return(True)
```

Next, we create a generator that yields cyclic permutations of a given number:


```python
def cyclic_permutations(n):
    
    """
    A generator that yields cyclic permutations of a given number
    
    Parameters
    ----------
    n : int
        A number whose cyclic permutations we wish to derive
    
    Yields
    -------
    int
        A cyclic permutation of that number
        
    Example
    -------
    >>> list(cyclic_permutations(123))
    [123, 312, 213]
    
    """
    
    s = str(n)
    L = len(s)
    
    for shift in range(L):
        output = int(''.join([s[(position - shift)%L] \
                              for position in range(L)]))
        yield output
```

For example, the cyclic permutations of $123$ are:


```python
list(cyclic_permutations(123))
```




    [123, 312, 231]



Next, let's create a function to check if a number is a circular prime.  
For a number to be a circular prime, all its circular permutations must be prime.  
Therefore, this function will combine both the `is_prime` function and the `cyclic_permutations` generator we defined before:


```python
def is_circular_prime(n):
    
    """
    Checks if a number is a a circular prime.
    
    A number is a circular prime if all its circular permutations
    are prime numbers.
    
    Parameters
    ----------
    n : int
        A number we wish to check if is a circular prime.
    
    Results
    -------
    bool
        True if a number is a circular prime.
        False otherwise.
    
    Examples
    --------
    >>> is_circular_prime(179)
    True
    
    >>> is_circular_prime(31)
    True
    
    >>> is_circular_prime(7)
    True
    
    >>> is_circular_prime(7707)
    False
    
    """
    
    #Creates a list of all cyclic permutations of a number n
    permutations = list(cyclic_permutations(n))
    
    #Verifies if each element of the list is prime
    prime = list(map(is_prime, permutations))
    
    #Verifies if all elements of the list are prime
    #If all elements are prime, returns True
    #Otherwise, returns False.
    
    all_prime = all(prime)
    
    return(all_prime)
    
```

Let's check if it works on the example provided at the problem's description: 197.
We see that our function indeed identifies it as a circular prime:


```python
%%timeit
is_circular_prime(197)
```

    38.6 µs ± 13.9 µs per loop (mean ± std. dev. of 7 runs, 1 loop each)
    

We wish to identify all circular primes up to a million.  
All we have to do is run this function on all numbers from 1 to a million.  
One way to do this is with the function we define below:


```python
def count_circular_primes(N):
    
    """
    Counts the number of smaller primes smaller than or equal to N
    
    Parameters
    ----------
    N : int
        The number below which we wish to count all circular primes
        
    Results
    -------
    int
        The number of circular primes smaller than or equal to N
    
    Notes:
    ------
    This function assumes n >= 3.
    This way, it can start at 3 and count only odd numbers
    as candidates for being circular primes.
    
    Examples:
    ---------
    >>>count_circular_primes(100)
    13
    """
    
    assert N >= 3, "Input must be greater or equal to 3"
    assert isinstance(N, int), "Input must be an integer"
    
    #Our count starts with 1, representing the number 2.
    #This way, we can continue our count starting at 3,
    #and counting only odd numbers, since no even number beyond 2 is prime.
    count_circular_primes = 1
    
    #For each odd number until N...
    for i in range(3, N + 1, 2):
        #...if it is a circular prime...
        if is_circular_prime(i):
            #... add 1 to the count of circular primes
            count_circular_primes += 1
            
    return(count_circular_primes)
    
```

To see that it works, let's verify the problem's description's claim that there are 13 circular primes smaller than 100:


```python
count_circular_primes(100)
```




    13



Our answer is correct!  
We may thereby attempt to use the function above to calculare all circular primes up to a million:


```python
%%time
count_circular_primes(10**6)
```

    Wall time: 33.8 s
    




    55



The answer we get is the correct one: there are 55 circular primes beneath a million.
So we did solve the problem. But it did take a while.
Indeed, we can make this function faster if we consider the following:

If we verified that 179 is a circular prime, we don't need to verify that 971 is also a prime.
When we verify that 179 is a circular prime, we immediately learn that at least $3$ numbers are prime: $179$, $917$, and $791$.
So, when we verify that 179 is circular, rather than adding 1 to the count of circular primes, we should add 3,
and skip numbers $917$ and $791$ when we arrive at them.

One way we can do this is by checking if the first digit of the number we're evaluating is also its smallest digit.  
We don't need to verify number $791$, for instance, because number $197$ has already been verified on its behalf.

A difficulty arrises, however, in numbers like $113$, where $1$ will be the smallest digit twice.  
These means we'll add $3$ to the counter twice: first, when we meet number $113$, and then again, when we meet number $131$.
So, rather than adding $3$ each time, let's add $3/2$, where the $2$ in the denominator is simply the number of times the smallest digit (that is, $1$) appears in $113$.

These considerations allows us to modify our previous function and build a faster version for it:



```python
def fast_count_circular_primes(N):
    
    """
    Counts the number of smaller primes smaller than or equal to N.
    
    This function is faster than count_circular_primes,
    particularly for larger values of N.
    
    Parameters
    ----------
    N : int
        The number below which we wish to count all circular primes
        
    Results
    -------
    int
        The number of circular primes smaller than or equal to N
    
    Notes:
    ------
    This function assumes n >= 3.
    This way, it can start at 3 and count only odd numbers
    as candidates for being circular primes.
    
    This function attempts to avoid counting cyclic primes multiple times.
    For example, once we verified that number 179 is a cyclic prime, 
    we do not have to verify that 917 is a multiple prime as well.
    
    So we only verify numbers whose first digit is also its smallest digit.
    Number 917 does not need to be counted, because number 179 has already been.
    
    Thus, when we verify that number 179 is a cyclic prime,
    we increment its count by 3 (not by 1),
    since it has 3 distinct cyclic permutations.
    
    A problem arises, however, with numbers whose smallest digit 
    appears more than once, such as 113.
    In this case, the smallest digit will be the first digit twice.
    In order to correct this, 
    rather than adding 3 to the number of cyclic counts,
    we add 3 / 2.
    More generally, we add the number of digits from the cyclic prime
    divided by the number of occurences of its smallest digit.
    This is the count_increment variable.
    
    
    Examples:
    ---------
    >>>count_circular_primes(100)
    13
    """
    
    #Our count starts with 1, representing the number 2.
    #This way, we can continue our count starting at 3,
    #and counting only odd numbers, since no even number beyond 2 is prime.
    count_circular_primes = 1
    
    #For each odd number until N...
    for i in range(3, N + 1, 2):
        #Create a string version of number i so we can loop through its digits
        s = str(i)
        #If its first number is not its smallest digit,
        #this number has been counted before. If it is...
        if(s[0] == min(s)):
            #...if it is a circular prime...
            if is_circular_prime(i):
                #Increment the count of circular primes accordingly.
                #See Notes for explanation.
                count_increment = len(s)/sum([j == min(s) for j in s])
                count_circular_primes += count_increment
            
    return(int(count_circular_primes))

```

Let's see how it performs calculating all the circular primes from 1 to a million:


```python
%%time
fast_count_circular_primes(10**6)
```

    Wall time: 4.62 s
    




    55



We've gotten the same result, but much faster!  
Let's see how much faster by looking at some confidence intervals:


```python
%timeit count_circular_primes(10**6)
%timeit fast_count_circular_primes(10**6)
```

    36.1 s ± 5.8 s per loop (mean ± std. dev. of 7 runs, 1 loop each)
    5.49 s ± 423 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
    

The average running time reduced from 36 to 5 seconds. 
That's quite amazing!


```python

```
