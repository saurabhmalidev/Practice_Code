1. Reverse a String
```
print(s)
#------------------------
# List + Swap
s = 'abcdef'
s = list(s)
for i in range(int(len(s)/2)):
    temp = s[i]             # Assigning 1st character to String
    s[i] = s[-(i+1)]        # Assigning last character to 1st position, last element is -(i+1)
    s[-(i+1)] = temp        # Assigning Last element the temp value

print(s)
#------------------------
# Loop (ch + rev)
s = "abcdef"
rev = ""
for ch in s:
    rev = ch + rev          # rev  = b + a
print(rev)
#------------------------
# Slicing
s = "abcdef"
print(s[::-1])
#------------------------
# reversed() + join()
s = "abcdef"
print("".join(reversed(s)))
```

2. Check whether a String is a Palindrome.
```
# A palindrome is a sequence (string, number, etc.) that reads the same forward and backward.
s = 'abcdcba'

is_pal = True                     #<----this thinking of FLAG is important
for i in range(int(len(s)/2)):    #<----When middle element need not to process skip +1
    if s[i] != s[-(i+1)]:
        is_pal = False
        break                     # Once condition met no need to check further
    else:
        pass
if is_pal:
    print("Palindrome")
else:
    print("Not a Palindrome")

# Thinking:
1. Does the middle element need to be processed - if yes use: range(len(s)//2 + 1)
2. Does the middle element need to be processed - if no  use: range(len(s)//2)
```
3. Count the Number of Vowels in a String.
```
s = 'sdkjdfkdlnfe'
count = 0
for i in s:
    if i in ['a','e','i','o','u']:    #or you can use "if i in 'aeiou':"
        count += 1
print(count) 
```
4. Count Uppercase and Lowercase Characters.
```
s = 'asJfbasDSDA'
count_lower = 0
count_upper = 0

for i in s:
    if i.islower():
        count_lower +=1
    elif i.isupper():
        count_upper +=1
    else:
        pass
print(f"Lower Count : {count_lower}")
print(f"Upper Count : {count_upper}")
```

5. Remove All Spaces from a String.
```
statement = 'hello people of India'
i_state = list(statement.split(" "))
result = ''.join(i_state)
print(statement)
print(result)
```
6. Count the Frequency of Each Character.
```
  word =  "banana"
  seen = []
  my_dict = {}
  for i in word:
      if i in seen:
          my_dict[i] += 1
      else:
          seen.append(i)
          my_dict[i] = 1
  print(my_dict)
```
8. Find the First Non-Repeating Character.
```
s = 'yasdbkmasbdbykmn'
my_dict = {}
for i in s:
    if i in my_dict:
        my_dict[i] += 1
    else:
        my_dict[i] = 1

for key, value in my_dict.items():
    if value == 1:
        print(key)
        break
```
9. Check whether Two Strings are Anagrams.
```
# An anagram is when two strings contain exactly the same characters with the same frequencies, but the characters may be in a different order.
s1 = "listen"
s2 = "silent"

if len(s1) != len(s2):    #<-----------lecgth check 1st
    print("Not a Anagram")
else:
    s1l = list(sorted(s1))
    s2l = list(sorted(s2))
    flag = True
    for i in range(len(s1)):
        if s1l[i] != s2l[i]:
            flag = False
            break
        else:
            pass
            
    if flag:
        print("Anagram")
    else:
        print("Not Anagram")
```
10. Count the Number of Occurrences of a Substring (without using .count()).
```
s = 'abcdcdc'
subs = 'cdc'

count = 0
for i in range((len(s)-len(subs)+1)):  #<------This Range is Important
    for j in range(len(subs)):
        if s[i+j] != subs[j]:
            break
    else:                              #<------This else belongs to teh For Loop its a For-else Loop
        count +=1
    
print(count)

NOTE: The else in a for...else loop does not mean: "If the if condition is false..."
Instead, it means: "Execute this block if the for loop finishes normally (without hitting a break)." 
```
11. Replace Every Space with a Hyphen (-).
```
sent = "I love India"
sent2 = list(sent.split(' '))
result ='-'.join(sent2)
print(result)
```
12. Capitalize the First Letter of Every Word (without using .title()).
```

```
13. Find the Longest Word in a Sentence.
```

```
14. Compress a String (e.g., aaabbcccc → a3b2c4).
```

```
15. Remove Duplicate Characters while Preserving Order.
```

```
16. Check whether One String is a Rotation of Another.
```

```
17. Find the Most Frequent Character in a String.
```

```
18. Reverse Each Word in a Sentence while Keeping the Word Order the Same.
```

```
19. Remove Consecutive Duplicate Characters.
```

```
20. Check whether Two Strings are Isomorphic.
```

```
21. Implement Left, Right, and Center String Alignment without using .ljust(), .rjust(), or .center().
```
thickness = int(input()) #This must be an odd number
c = 'H'

#Top Cone
for i in range(thickness):
    print((c*i).rjust(thickness-1)+c+(c*i).ljust(thickness-1))

#Top Pillars
for i in range(thickness+1):
    print((c*thickness).center(thickness*2)+(c*thickness).center(thickness*6))

#Middle Belt
for i in range((thickness+1)//2):
    print((c*thickness*5).center(thickness*6))    

#Bottom Pillars
for i in range(thickness+1):
    print((c*thickness).center(thickness*2)+(c*thickness).center(thickness*6))    

#Bottom Cone
for i in range(thickness):
    print(((c*(thickness-i-1)).rjust(thickness)+c+(c*(thickness-i-1)).ljust(thickness)).rjust(thickness*6))


    H
   HHH
  HHHHH
 HHHHHHH
HHHHHHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHHHHHHHHHHHHHHHHHHHHHH
  HHHHHHHHHHHHHHHHHHHHHHHHH
  HHHHHHHHHHHHHHHHHHHHHHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
  HHHHH               HHHHH
                   HHHHHHHHHHH
                    HHHHHHHHH
                     HHHHHH
                       HHH
                        H   
```
