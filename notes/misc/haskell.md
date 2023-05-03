---
share: true
title: "Haskell"
---

## ghci

```haskell
> :module + Data.Ratio -- Imports rational numbers
> :info (+) -- Shows definition of + operator
> :info Num -- Shows class definition of Num
> :type 1 + 2 -- Shows the type of an expression
1 + 2 :: Num a => a
> :set +t -- Shows types on every eval
> :unset +t
> it -- The last return value
> :load foo.hs -- Load a file
```

Look up functions at https://hoogle.haskell.org/

## Lists

```haskell
> [1..10]
[1,2,3,4,5,6,7,8,9,10]
> 1 : []
[1]

> (head [1,2,3], tail [1,2,3])
(1,[2,3])
> tail []
*** Exception: Prelude.tail: empty list
> (take 2 [1,2,3], drop 2 [1,2,3])
([1,2],[3])
> init [1, 2, 3]
[1, 2]
> last [1, 2, 3]
3
> length [1, 2, 3]
3
> elem 2 [1, 2, 3]
True
> concat [[1], [2 3], [4]]
[1, 2, 3, 4]
> reverse [1, 2, 3]
[3, 2, 1]
> splitAt 2 [1, 2, 3]
([1, 2],[3])
> null []
true
> takeWhile odd [1,3,5,6,8,9,11]
[1,3,5]
> takeWhile (\x -> x /= 8) [1,3,5,6,8,9,11]
[1,3,5,6]
> dropWhile (\x -> x /= 8) [1,3,5,6,8,9,11]
[8,9,11]
> filter (\x -> x /= 2) [1,2,3]
[1,3]
> [2] `isInfixOf` [1,2,3]
True
> foldl (+) [1,2,3]
6
> foldl (\b a -> b ++ show a) "" [1,2,3]
"123"
> foldr (\a b -> b ++ show a) "" [1,2,3]
"321"
> map (\x -> x + 1) [1,2,3]
[2,3,4]
> 

type String = [Char]

> 'a':"bc"
"abc"
> it ++ "def"  
"abcdef"
> lines "the quick\nbrown fox\njumps"
["the quick","brown fox","jumps"]
> splitAt 3 "foobar"
("foo", "bar")
> isPrefixOf "foo" "foobar"
True
> "foo" `isPrefixOf` "foobar"
True
```

```haskell
len :: [a] -> Int
len [] = 0
len (_ : t) = 1 + len t

rev :: [a] -> [a]
rev [] = []
rev (h : t) = rev t ++ [h]

palindrome :: [a] -> [a]
palindrome l = l ++ rev l

myLast :: [a] -> Maybe a
myLast [] = Nothing
myLast [x] = Just x
myLast (x : y) = myLast y 

isPalindrome :: Eq a => [a] -> Bool
isPalindrome [] = True
isPalindrome [a] = True
isPalindrome [a, b] = a == b
isPalindrome (h : t) = h == last t && isPalindrome (init t)
```

## Tuples

```haskell
> fst (1, 2)
1
> snd (1, 2)
2
```

## Rational numbers

```haskell
> 11 % 29
11%29
it :: Ratio Integer
> (11 % 29) * (100 % 3)  
1100 % 87
```

## Integers

```haskell
odd :: Integral a => a -> Bool
even :: Integral a => a -> Bool
data Ordering = LT | EQ | GT
compare :: Ord a => a -> a -> Ordering
(==) :: Eq a => a -> a -> Bool
(/=) :: Eq a => a -> a -> Bool

> (compare 2 3) == LT
True
```

## Errors

```haskell
mySecond :: [a] -> a

mySecond xs = if null (tail xs)
              then error "list too short"
              else head (tail xs)
```

## Types

```haskell
-- A new type 'BookInfo' with a single constructor 'Book' that has 3 parameters
data BookInfo = Book Int String [String] deriving (Show)

myInfo = Book 9780135072455 "Algebra of Programming"
         ["Richard Bird", "Oege de Moor"]

> :t Book
Book :: Int -> String -> [String] -> BookInfo

-- Type synonyms
type CustomerID = Int
type Vector = (Double, Double)

-- Algebraic data types / Unions
data Bool = False | True
data Shape = Circle Vector Double
           | Poly [Vector]

-- Records
data Customer = Customer {
      customerID      :: CustomerID
    , customerName    :: String
    , customerAddress :: Address
    } deriving (Show)
customer1 = Customer 271828 "J.R. Hacker"
            ["255 Syntax Ct",
             "Milpitas, CA 95134",
             "USA"]
customer2 = Customer {
              customerID = 271828
            , customerAddress = ["1048576 Disk Drive",
                                 "Milpitas, CA 95134",
                                 "USA"]
            , customerName = "Jane Q. Citizen"
            }

-- Paramaterized
data Maybe a = Just a
             | Nothing
someBool = Just True
someString = Just "something"

-- Recursion
data List a = Cons a (List a)
            | Nil
              deriving (Show)


```

## Pattern Matching

```haskell
-- Fold by pattern matching list constructors
sumList (x:xs) = x + sumList xs
sumList []     = 0

-- Unpacking tuples
third (a, b, c) = c

-- Basic constructors
bookID (Book id title authors) = id
nicerID (Book id _ _) = id

-- Case expressions
fromMaybe defval wrapped =
    case wrapped of
      Nothing     -> defval
      Just value  -> value

-- Guards
niceDrop n xs | n <= 0 = xs
niceDrop _ []          = []
niceDrop n (_:xs)      = niceDrop (n - 1) xs

lend3 amount balance
     | amount <= 0            = Nothing
     | amount > reserve * 0.5 = Nothing
     | otherwise              = Just newBalance
    where reserve    = 100
          newBalance = balance - amount

-- As-pattern
suffixes :: [a] -> [[a]]
suffixes xs@(_:xs') = xs : suffixes xs'
suffixes _ = []
```

## Local Variables

```haskell
-- let x = y in ...
lend amount balance = let reserve    = 100
                          newBalance = balance - amount
                      in if balance < reserve
                         then Nothing
                         else Just newBalance

-- `let` can shadow previous variables and parameters

-- ... where x = y
lend2 amount balance = if amount < reserve * 0.5
                       then Just newBalance
                       else Nothing
    where reserve    = 100
          newBalance = balance - amount
```

## Composition

```haskell
a $ b c = a (b c)
a . b = \x a b x
```

## Evaluation

```haskell
-- seq forces its first argument to be evaluated first, allowing you to change the evaluation performance characteristics
-- this is useful to eliminate function frames (thunks) from growing in recursive folds
seq :: a -> t -> t
```

