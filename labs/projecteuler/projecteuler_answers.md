```
import cipher1
import keylog

```

### [Problem 9](https://projecteuler.net/problem=9)

> A Pythagorean triplet is a set of three natural numbers, a < b < c, for which a^2 + b^2 = c^2.
> 
> For example, 3^2 + 4^2 = 9 + 16 = 25 = 5^2.  There exists exactly one Pythagorean triplet for which a + b + c = 1000.  Find the product abc.

```
pythagoreantriple : Integer -> Integer -> Integer -> Bit
property pythagoreantriple a b c =
    a^^2 + b^^2 == c^^2 /\
    a + b + c == 1000   /\
    a != 0
```

### [Problem 34](https://projecteuler.net/problem=34)

> 145 is a curious number, as 1! + 4! + 5! = 1 + 24 + 120 = 145.
>
> Find the sum of all numbers which are equal to the sum of the factorial of their digits.
> 
> Note: as 1! = 1 and 2! = 2 are not sums they are not included.
>
> (Aside: these numbers are called [factorions](https://en.wikipedia.org/wiki/Factorion).  It is unclear why Project Euler does not include 1 and 2.  Finally, it is known that the most number of digits a factorion can have is 6).
>
> Hints: the factorial function is usually defined recursively, but that tends to make SAT solving difficult.  Since you only need to calculate the factorial of the numbers 0-9, make your function just do a case by case calculation.  To get the digital representation of the number, create a function which takes in a number and a list of numbers and returns true exactly when the list is the base 10 representation.

```
factorial : Integer -> Integer
factorial n = if n == 2 then      2 else
	      if n == 3 then      6 else
	      if n == 4 then     24 else
	      if n == 5 then    120 else
	      if n == 6 then    720 else
	      if n == 7 then   5040 else
	      if n == 8 then  40320 else
	      if n == 9 then 362880 else
	      1

sum l = s ! 0
    where
     s = [0] # [ t + i | t <- l | i <- s ]

powersoften = [1] # [ 10 * i | i <- powersoften ]

alldigits : {a} (fin a) => [a]Integer -> Bit
alldigits l = [ 0 <= i /\ i < 10 | i <- l ] == ~0

basetenrep :
    {a}
    (fin a, a >= 1) =>
    Integer -> [a]Integer -> Bit
basetenrep n l = n == s /\ alldigits l /\ l @ 0 != 0
     where
      p = take powersoften
      z = zip (reverse l) p
      f = [ t.0 * t.1 | t <- z ]
      s = sum f

sumfactorial : {a} (fin a) => [a]Integer -> Integer
sumfactorial l = sum [ factorial i | i <- l ]


factorionprop :
    {a}
    (fin a, a >= 1) =>
    Integer -> [a]Integer -> Bit
property factorionprop n l =
    basetenrep n l /\
    sumfactorial l == n
```
```shell
Main> :sat factorionprop`{1}
factorionprop`{1} 2 [2] = True
(Total Elapsed Time: 0.048s, using Z3)
Main> :sat (\(x, l) -> factorionprop`{1} x l /\ x != 2)
(\(x, l) -> factorionprop`{1} x l /\ x != 2) (1, [1]) = True
(Total Elapsed Time: 0.047s, using Z3)
Main> :sat (\(x, l) -> factorionprop`{1} x l /\ x != 2 /\ x != 1)
Unsatisfiable
(Total Elapsed Time: 0.046s, using Z3)
Main> :sat factorionprop`{2}
Unsatisfiable
(Total Elapsed Time: 0.041s, using Z3)
Main> :sat factorionprop`{3}
factorionprop`{3} 145 [1, 4, 5] = True
(Total Elapsed Time: 0.067s, using Z3)
Main> :sat (\(x,l) -> factorionprop`{3} x l /\ x != 145)
Unsatisfiable
(Total Elapsed Time: 0.063s, using Z3)
Main> :sat factorionprop`{4}
Unsatisfiable
(Total Elapsed Time: 0.086s, using Z3)
Main> :sat factorionprop`{5}
factorionprop`{5} 40585 [4, 0, 5, 8, 5] = True
(Total Elapsed Time: 0.096s, using Z3)
Main> :sat (\(x,l) -> factorionprop`{5} x l /\ x != 40585)
Unsatisfiable
(Total Elapsed Time: 0.141s, using Z3)
Main> :sat factorionprop`{6}
Unsatisfiable
(Total Elapsed Time: 0.205s, using Z3)
```

### [Problem 36](https://projecteuler.net/problem=36)

> The decimal number, 585 = 1001001001_(2) (binary), is palindromic in both bases.
>
> Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.
>
> (Please note that the palindromic number, in either base, may not include leading zeros.)

```
doublepalindrome :
    {a, b}
    (fin a, a >= 1, fin b) =>
    [b] -> [a]Integer -> Bit
property doublepalindrome x l =
    basetenrep (toInteger x) l /\
    reverse l == l             /\
    reverse x == x             /\
    x@0
```

```shell
Main> :sat doublepalindrome`{1,3}
doublepalindrome`{1, 3} 0x5 [5] = True
(Total Elapsed Time: 0.042s, using Z3)
Main> :sat doublepalindrome`{3,10}
doublepalindrome`{3, 10} 0x2cd [7, 1, 7] = True
(Total Elapsed Time: 0.060s, using Z3)
Main> :sat doublepalindrome`{3,9}
doublepalindrome`{3, 9} 0x139 [3, 1, 3] = True
(Total Elapsed Time: 0.073s, using Z3)
...
```

### [Problem 43](https://projecteuler.net/problem=43)

> The number, 1406357289, is a 0 to 9 pandigital number because it is made up of each of the digits 0 to 9 in some order, but it also has a rather interesting sub-string divisibility property.
>
> Let d_(1) be the 1^(st) digit, d_(2) be the 2^(nd) digit, and so on. In this way, we note the following:
>
>    * d_(2)d_(3)d_(4)=406 is divisible by 2
>    * d_(3)d_(4)d_(5)=063 is divisible by 3
>    * d_(4)d_(5)d_(6)=635 is divisible by 5
>    * d_(5)d_(6)d_(7)=357 is divisible by 7
>    * d_(6)d_(7)d_(8)=572 is divisible by 11
>    * d_(7)d_(8)d_(9)=728 is divisible by 13
>    * d_(8)d_(9)d_(10)=289 is divisible by 17
>
> Find the sum of all 0 to 9 pandigital numbers with this property.  

```
listhasdigit :
    {a}
    (fin a, a >=1) =>
    [a]Integer -> Integer -> Bit
listhasdigit l n = [ i == n | i <- l ] != 0

hasalldigits : [10]Integer -> Bit
hasalldigits l =
    [ listhasdigit l i | i <- [0..9] ] == ~0

formnumber :
    {a}
    (fin a) =>
    [a]Integer -> Integer
formnumber l =
    sum [ i * t | i <- reverse l | t <- powersoften ]

pandigital : Integer -> [10]Integer -> Bit
pandigital n l =
    basetenrep n l  /\
    hasalldigits l  /\
    n234 % 2  == 0  /\
    n345 % 3  == 0  /\
    n456 % 5  == 0  /\
    n567 % 7  == 0  /\
    n678 % 11 == 0  /\
    n789 % 13 == 0  /\
    n890 % 17 == 0
     where
      n234 = formnumber (l @@ ([1,2,3] : [3][16]))
      n345 = formnumber (l @@ ([2,3,4] : [3][16]))
      n456 = formnumber (l @@ ([3,4,5] : [3][16]))
      n567 = formnumber (l @@ ([4,5,6] : [3][16]))
      n678 = formnumber (l @@ ([5,6,7] : [3][16]))
      n789 = formnumber (l @@ ([6,7,8] : [3][16]))
      n890 = formnumber (l @@ ([7,8,9] : [3][16]))
```

```shell
Main> :sat pandigital 
pandigital 4130952867 [4, 1, 3, 0, 9, 5, 2, 8, 6, 7] = True
(Total Elapsed Time: 1.001s, using Z3)
Main> :sat (\(x, l) -> pandigital x l /\ x != 4130952867)
(\(x, l) -> pandigital x l /\ x != 4130952867)
  (4160357289, [4, 1, 6, 0, 3, 5, 7, 2, 8, 9]) = True
(Total Elapsed Time: 7.419s, using Z3)
...
```
### [Problem 52](https://projecteuler.net/problem=52)

>It can be seen that the number, 125874, and its double, 251748, contain exactly the same digits, but in a different order.
>
> Find the smallest positive integer, x, such that 2x, 3x, 4x, 5x, and 6x, contain the same digits.

```
twolistssamedigits :
    {a}
    (fin a, a >=1) =>
    [a]Integer -> [a]Integer -> Bit
twolistssamedigits l1 l2 =
    [ listhasdigit l1 i | i <- l2 ] == ~0

productdigits : {a} (fin a, a >= 1) => Integer -> [6][a]Integer -> Bit
property productdigits n ls =
    basetenrep n l1                 /\
    alltwolists                     /\
    allforms                        /\
    [ alldigits l | l <- ls ] == ~0
    where
     [l1, l2, l3, l4, l5, l6] = ls
     tls = tail ls
     alltwolists = [ twolistssamedigits l1 l
     		   | l <- tls ] == ~0
     allforms = [ formnumber li == i * n
     	      	| li <- tls
		| i <- [2..6] ] == ~0
```

### [Problem 59](https://projecteuler.net/problem=59)

> Each character on a computer is assigned a unique code and the preferred standard is ASCII (American Standard Code for Information Interchange). For example, uppercase A = 65, asterisk (*) = 42, and lowercase k = 107.
>
> A modern encryption method is to take a text file, convert the bytes to ASCII, then XOR each byte with a given value, taken from a secret key. The advantage with the XOR function is that using the same encryption key on the cipher text, restores the plain text; for example, 65 XOR 42 = 107, then 107 XOR 42 = 65.
>
> For unbreakable encryption, the key is the same length as the plaintext message, and the key is made up of random bytes. The user would keep the encrypted message and the encryption key in different locations, and without both "halves", it is impossible to decrypt the message.
>
> Unfortunately, this method is impractical for most users, so the modified method is to use a password as a key. If the password is shorter than the message, which is likely, the key is repeated cyclically throughout the message. The balance for this method is using a sufficiently long password key for security, but short enough to be memorable.
>
> Your task has been made easy, as the encryption key consists of three lower case characters. Using cipher1.cry, a file containing the encrypted ASCII codes, and the knowledge that the plain text must contain common English words, decrypt the message and find the sum of the ASCII values in the original text.

```
containsWords :
    {a, b}
    (fin a, a >= 2, fin b) =>
    [a]Char -> [b][3]Char -> Bit
containsWords l ws = [ checkword w | w <- ws ] == ~0
    where
     checkword w = [ c0 == w@0 /\
     	       	     c1 == w@1 /\
		     c2 == w@2
		   | c0 <- l
		   | c1 <- tail l
		   | c2 <- tail (tail l) ] != 0

isLowercase : Char -> Bit
isLowercase c = c >= 0x61 /\ c <= 0x7a

isLowercaseStr : {a} (fin a) => [a]Char -> Bit
isLowercaseStr s = [ isLowercase c | c <- s ] == ~0

XORtowords :
    {a}
    (fin a, a >= 2) =>
    [a]Char -> [3]Char -> Bit
XORtowords ciphertext key =
    containsWords ct [ "the", "and", "are" ] /\
    isLowercaseStr key
     where
      keys = [ key ] # [ k | k <- keys ]
      jkeys = join keys
      ct = ciphertext ^ (take jkeys)

decrypt : {a} (fin a, a >= 2) => [a]Char -> [3]Char -> [a]Char
decrypt s key = s ^ (take ks)
    where
     ks = join keys
     keys = [ key ] # [ k | k <- keys ]
```
```shell
Main> :sat (XORtowords cipher)
(XORtowords cipher) "god" = True
(Total Elapsed Time: 1.751s, using Z3)
Main> decrypt cipher "god"
"(The Gospel of John, chapter 1) 1 In the beginning the Word already existed. He was with God, and he was God. 2 He was in the beginning with God. 3 He created everything there is. Nothing exists that he didn't make. 4 Life itself was in him, and this life gives light to everyone. 5 The light shines through the darkness, and the darkness can never extinguish it. 6 God sent John the Baptist 7 to tell everyone about the light so that everyone might believe because of his testimony. 8 John himself was not the light; he was only a witness to the light. 9 The one who is the true light, who gives light to everyone, was going to come into the world. 10 But although the world was made through him, the world didn't recognize him when he came. 11 Even in his own land and among his own people, he was not accepted. 12 But to all who believed him and accepted him, he gave the right to become children of God. 13 They are reborn! This is not a physical birth resulting from human passion or plan, this rebirth comes from God.14 So the Word became human and lived here on earth among us. He was full of unfailing love and faithfulness. And we have seen his glory, the glory of the only Son of the Father."
```

### [Problem 79](https://projecteuler.net/problem=79)

> A common security method used for online banking is to ask the user for three random characters from a passcode. For example, if the passcode was 531278, they may asked for the 2nd, 3rd, and 5th characters; the expected reply would be: 317.
>
> The text file, keylog.cry, contains fifty successful login attempts.
>
> Given that the three characters are always asked for in order, analyse the file so as to determine the shortest possible secret passcode of >unknown length.

```
passcode :
    {a}
    (fin a, a >= 1) =>
    [a]Integer -> Bit
passcode l = [ loop l kl != 0 | kl <- keylog ] == ~0
    where 
     loop ll kll = [ [ ll@(i:[a]), ll@j, ll@k] == kll /\
	             i < j                      /\
	             j < k
	           | i <- [0..a-1],
	     	     j <- [0..a-1],
	   	     k <- [0..a-1] ]
```
```shell
Main> :sat passcode`{1}
Unsatisfiable
(Total Elapsed Time: 0.033s, using Z3)
Main> :sat passcode`{2}
Unsatisfiable
(Total Elapsed Time: 0.048s, using Z3)
Main> :sat passcode`{3}
Unsatisfiable
(Total Elapsed Time: 0.106s, using Z3)
Main> :sat passcode`{4}
Unsatisfiable
(Total Elapsed Time: 0.508s, using Z3)
Main> :sat passcode`{5}
Unsatisfiable
(Total Elapsed Time: 1.074s, using Z3)
Main> :sat passcode`{6}
Unsatisfiable
(Total Elapsed Time: 2.066s, using Z3)
Main> :sat passcode`{7}
Unsatisfiable
Main> :sat passcode`{8}
passcode`{8} [7, 3, 1, 6, 2, 8, 9, 0] = True
(Total Elapsed Time: 5.667s, using Z3)
```
### Project Sean
Sean Weaver (Super Genius ala Wile E Coyote) has a problem in a similar vein:

> Find a four-digit number (greater than 999 and less than 10000) such that the least significant four digits of the square is that number.
>
> EXTRA CHALLENGE:
> What about five-digit numbers? Other numbers of digits?

```shell
Main> :sat (\(x : [32]) -> (x*x)%10000 == x /\ x > 999 )
(\(x : [32]) -> (x * x) % 10000 == x /\ x > 999) 0x000024a0 = True
(Total Elapsed Time: 0.195s, using Z3)
```

```
squaredrop :
    {a}
    (fin a, a>=2, a%2 == 0) =>
    Integer -> [a]Integer -> Bit
squaredrop n l =
    (basetenrep (n*n) l) /\
    formnumber (drop`{back = ((a)/2)} l) == n
```
```shell
Main> :sat squaredrop`{a=8} 
squaredrop`{a = 8} 9376 [8, 7, 9, 0, 9, 3, 7, 6] = True
(Total Elapsed Time: 21.782s, using Z3)
```