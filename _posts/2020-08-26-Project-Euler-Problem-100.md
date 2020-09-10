
When I came accross [Problem 100](https://projecteuler.net/problem=100) from the [Euler's Project](https://projecteuler.net/about), I felt it was too sweet to overlook:

>If a box contains twenty-one coloured discs, composed of fifteen blue discs and six red discs, and two discs were taken at random, it can be seen that the probability of taking two blue discs, P(BB) = (15/21)×(14/20) = 1/2.  
The next such arrangement, for which there is exactly 50% chance of taking two blue discs at random, is a box containing eighty-five blue discs and thirty-five red discs.  
By finding the first arrangement to contain over $10^{12} = 1,000,000,000,000$ discs in total, determine the number of blue discs that the box would contain.

According to the Euler's project, this problem has been solved 15,436 times to date.
Let's push that number to 15,437, shall we?

## The mathematical intuition

Let $N$ be the total number of disks in the box and $B$ be the number of blue ones.  
The probability of getting one blue ball is $\frac{B}{N}$. Once you get this ball, there are only $N-1$ balls left, out of which only $B-1$ are blue. So the probability of getting a second blue ball is $\frac{B-1}{N-1}$. The probability of getting two blue balls, therefore, is 

$$
\frac{B}{N}\cdot\frac{B-1}{N-1}
$$

We want to find $N$ and $B$ such that

$$
\frac{B}{N}\cdot\frac{B-1}{N-1} = \frac{1}{2}
$$

with $N > 10^{12}$.

Now bear with me here:  
If you have $10^{12}$ balls, does it make any difference to take one ball away?  
It makes no practical difference!  
With such a large value of $N$, $\frac{B}{N}$ and $\frac{B-1}{N-1}$ are virtually the same thing.  
This means we can write  

$$
\frac{B}{N}\cdot\frac{B-1}{N-1} = \frac{1}{2}
$$

as

$$
\frac{B}{N}\cdot\frac{B}{N} = \frac{1}{2}
$$

or, equivalently

$$
\left(\frac{B}{N}\right)^2 = \frac{1}{2}
$$

We wish to find the smallest values of $B$ and $N$ that satisfy this equation with $N > 10^{12}$.


## The same solution by another way

We would have reached the same expression by noting that the number of blue balls obtained by randomly selecting 2 balls from a large number of $N$ balls follows a binomial distribution with $n = 2$ and $p$ being simply the fraction of blue balls available. Thus, 

$$X \sim Bin \left(n = 2; p = \frac{B}{N} \right)$$

This means the probability of getting exactly two blue balls at random is

$$
P(X = 2) = C_2^2 \left(\frac{B}{N} \right)^2\left(1 - \frac{B}{N} \right)^{N-2} 
$$

which must be equal to $\frac{1}{2}$.

Note, however, that $C_2^2 = 1$ and that $N-2$ is so large that $\left(1 - \frac{B}{N} \right)^{N-2} \approx 1$. This takes us back to the previous expression

$$
\left(\frac{B}{N}\right)^2 = \frac{1}{2}
$$

## Preparing to code...

Let's write it in a slightly different way:

$$
B = \frac{N}{\sqrt{2}}
$$

This means we need to find a value of $N$ which is greater than $10^{12}$ *and* is divisible by $\sqrt{2}$.  
That's the number of balls in the urn we're looking for: it's the first part of our answer.
Once If we find such a value, all we have to do is divide it by $\sqrt{2}$ and we'll have the number of blue balls that must be in that urn. That's the second part of our answer.

## OK, let's do it!

I'll use square roots and deal with floats, so I'll import numpy.


```python
import numpy as np
```

Now I want a function to check if a number is divisible by another. Specifically, I want to check if a number $N$ is divisible by $\sqrt{2}$, but I'll make the function more general. One of the challenges of this function is that I'll be dealing with floats, so I can't simply do `x == 0`, because I may run into approximation issues. So I'll use numpy's `isclose` function instead:


```python
def is_divisible(a, b):
    
    '''Checks if a number (float) a is divisible by another number (float) b.
    
    Parameters:
    -----------
    a : a float which we want to test if is divisible by b
    b : a float which we want to test if divides a
    
    Returns:
    -------
    A boolean. True if *a* is divisible by *b* and False otherwise.
    
    '''
    #Divide a by b
    x = a / b 
    
    #Get the decimal part of the number
    x = x - int(x) 
    
    #Return True if the decimal part of a divided by b is zero
    #Return False otherwise.
    return(np.isclose(x, 0)) 
```

Now let's start from $N = 10^{12}$ and check whether that value of $N$ allows for a valid solution to our problem.  
If it doesn't, we'll increment $N$ and try again:


```python
%%time
#Start with N = 10^12:
N = 10**12

#While N is not divisible by sqrt(2)...
while not is_divisible(N, np.sqrt(2)):
    #Increase N by 1
    N += 1
```

    Wall time: 376 ms
    

We found a solution!

How many balls should be in the urn?


```python
print(N)
```

    1000000006663
    

And out of these balls, how many should be blue?


```python
B = N / np.sqrt(2)
print(B)
```

    707106785898.0
    

Shall we check if our results are valid?

Recall what we originally wanted: to have 

$$
\frac{B}{N} \cdot \frac{B-1}{N-1} = \frac{1}{2}
$$

Is it? Let's see:


```python
(B/N) * (B-1)/(N-1)
```




    0.49999999999979283



Correcting for floating point representation issues, 


```python
np.isclose((B/N) * (B-1)/(N-1), 0.5)
```




    True



So yes: we've solved the problem!
