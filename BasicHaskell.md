# From Imperative Languages to Haskell
A sample program:  
```cpp
int main(int argc, const *char argv[]) {
    for (int i = 0; i < argc; i++) {
      printf("%s\n", argv[i]);
    }
    return 0;
}
```

```haskell
import System.Environment

main :: IO ()
main = do
  args <- getArgs
  forM a putStrLn
  return ()
```
The basics:
Variable binding:
```haskell
let a = 10
    b = 20
in a + b
```

```cpp
{ 
int a = 10;
int b = 20;
a + b;
}
```

Top level declarations:
```haskell
a = 1
b = 2
c = 3

myName = "Jared Roesch"
```

any declaration can have a type signature attached:
```haskell
a :: Int
a = 1

b :: Double
b = 2

c :: Integer
c = 3

myName :: String
myName = "Jared Roesch"

-- let is just an expression, can annontate types if I want
myName :: String
myName = 
    let firstName :: String
        firstName = "Jared"
        lastName :: String
        lastName  =  "Roesch"
    in firstName ++ lastName

-- or I can leave all the types off

myName :: String -- usually good to annotate this type as documentation
myName = 
    let firstName = "Jared"
        lastName  =  "Roesch"
    in firstName ++ lastName
``` 
Basic Types:
```haskell
int :: Int
int = 1

float :: Float
float = 2.0

double :: Double
double = 3.0

integer :: Integer
integer = 4 -- can be aribtraily sized

charList :: [Char]
charList = ['H', 'E', 'l', 'l, 'o', 'W', 'o', 'r', 'l', 'd']

string :: String 
string = "HelloWorld"

bool :: Boolean
bool = charList == string -- A string is simply a list of characters!
```

```cpp
void swap(int *x, int *y) {
    int tmp = *x;
    *x = *y;
    *y = tmp;
}
```

```haskell
swap :: (a, b) -> (b, a)
swap (x, y) = (y, x)
```

```haskell
data [a] = [] | a : [a]
-- or
data List a = Nil | List a (List a)
```

```haskell
data BinTree a = Empty
               | Leaf a
               | Branch (BinTree a) a (BinTree a)
               deriving (Show, Eq)
```

```haskell
tmap :: (a -> b) -> BinTree a -> BinTree b
tmap f Empty = Empty
tmap f (Leaf v) = Leaf $ f v
tmap f (Branch l v r) = 
  let l' = tmap f l
      v' = f v
      r' = tmap f r
  in (Branch l' v' r')
```

```haskell
data Map k a = BinTree k a (Map k a) (Map k a)
             | Tip
```

```haskell
mpmap :: (a -> b) -> Map k a -> Map k b
mpmap f Tip = Tip
mpmap f (BinTree k x l r) =
  let x' = f x
      l' = mpmap f l
      r' = mpmap f r
  in BinTree k x' l' r'
```

Let's model computations that have possibilty of failure (i.e. partial functions)
```haskell
data Maybe a = Nothing | Just a deriving (Show, Eq)
```

```haskell
mmap :: (a -> b) -> Maybe a -> Maybe b
mmap f Nothing  = Nothing
mmap f (Just v) = Just $ f v
```

Let's model computations that fail with an error
```haskell
data Either a b = Left a | Right b deriving (Show, Eq)
```

```haskell
emap :: (a -> b) -> Either e a -> Either e b
emap f (Left e)  = Left e
emap f (Right v) = Right (f v)
```

generalize this behavior into an abstract interface:

```haskell
-- types look similar
-- (a -> b) -> List a    -> List b
-- (a -> b) -> BinTree a -> BinTree b
-- (a -> b) -> Maybe a -> Maybe b
-- (a -> b) -> Map k a -> Map k b
-- (a -> b) -> Maybe a -> Maybe b
-- (a -> b) -> Either e a -> Either e b

-- all of them have the form 
-- (a -> b) -> f a -> f b
-- f <- [List, BinTree, Maybe, (Map k), (Either e)]
-- other people have realized this

-- It is a Functor!
-- wait what does that mean?

class Functor f where
  fmap :: (a -> b) -> f a -> f b
  
-- we can then make them all instances of Functor

-- now we can do
fmap (+1) (Just 1)
fmap (+1) Nothing
fmap (+1) [1,2,3]
fmap (+1) $ Map (Leaf 2) 1 (Leaf 3)

-- we can write general functions
incF :: (Functor f, Num a, Num b) => f a -> f b
incF = fmap (+1)

incF [1,2,3]
incF (Just 10)
```
