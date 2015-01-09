## Assignment: Caching the Inverse of a Matrix
**Ashok Kumar T.**

### Introduction

**Caching** is a technology based on the memory subsystem of your computer. 
The main purpose of a cache is to accelerate your computer tasks more 
rapidly.

The aim  of this programming assignment is to write an R function that 
is able to cache potentially time-consuming computations. For example, 
taking the mean of a numeric vector in R is typically a fast operation. 
However, for a very long vector, it may take too long to compute the 
mean, especially if it has to be computed repeatedly (*e.g. in a loop*). 
If the contents of a vector are not changed, it may make sense to cache 
the value of the mean so that when we need it again, it can be looked 
up in the cache rather than recomputed. 

### Program Disscussion

Matrix inversion is usually a costly computation and there may be some
benefit to caching the inverse of a matrix rather than computing it
repeatedly (*there are also alternatives to matrix inversion that is
not discussed here*). In this assignment, a pair of functions `makeCacheMatrix` 
and `cacheSolve` are written to cache the inverse of a matrix.

`makeCacheMatrix` is a function which creates a special "matrix" object 
that can cache its inverse, and `cacheSolve` is a function which computes 
the inverse of the special "matrix" returned by `makeCacheMatrix`. If 
the inverse has already been calculated (*and the matrix has not changed*), 
then `cacheSolve` will retrieve the inverse from the cache.

Computing the inverse of a square matrix can be done with the `solve`
function in R. For example, if `X` is a square invertible matrix, then
`solve(X)` returns its inverse.

The first function `makeCacheMatrix` creates a special "matrix", which is
really a list containing a function to

1.  `get` - To get the value of the matrix
2.  `set` - To set the value of the matrix
3.  `getInverse` - To get the inverse of the matrix
4.  `setInverse` - To set the inverse of the matrix

```
makeCacheMatrix <- function(x = matrix())
{
	m <- NULL
	set <- function(y)
	{
		x <<- y
		m <<- NULL
	}
	get <- function() x
	setInverse <- function(i) m <<- solve(x)
	getInverse <- function() m
	list(set = set, get = get, setInverse = setInverse, getInverse = getInverse)
}

```

The other function `cacheSolve` calculates the inverse of the special "matrix" 
created with the above function. It first checks to see if the inverse of the 
matrix has already been calculated. If so, it gets the inverse from the cache 
and skips the computation. Otherwise, it calculates the inverse of the data and 
sets the inverse matrix in the cache via the `setInverse` function.

```
cacheSolve <- function(x, ...)
{
	m <- x$getInverse()
	if(!is.null(m))
	{
		return(m)
	}
	m <- solve(x$get())
	x$setInverse(m)
	m
}
```

### Program Implementation

The complete program **cachematrix.R** can be publicly accessed via 
GitHub repository[`cachematrix.R`](https://raw.githubusercontent.com/AshokHub/ProgrammingAssignment2/master/cachematrix.R).

```
> source("cachematrix.R")
> a <- makeCacheMatrix(matrix(1:4,2))
> a$get()
     [,1] [,2]
[1,]    1    3
[2,]    2    4
> a$getInverse()
NULL
> a$setInverse()
> a$getInverse()
     [,1] [,2]
[1,]   -2  1.5
[2,]    1 -0.5
> cacheSolve(a)
     [,1] [,2]
[1,]   -2  1.5
[2,]    1 -0.5
> a$set(matrix(5:8,2))
> a$getInverse()
NULL
> a$get()
     [,1] [,2]
[1,]    5    7
[2,]    6    8
> cacheSolve(a)
     [,1] [,2]
[1,]   -4  3.5
[2,]    3 -2.5
> a$getInverse()
     [,1] [,2]
[1,]   -4  3.5
[2,]    3 -2.5
```
