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
#3. Count the Number of Vowels in a String.
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
=
for i in s:
    if i.islower():
        count_lower +=1
    elif i.isupper():
        count_upper +=1
    else:
        pass
print(f"Lower Count : {count_lower}")
print(f"Upper Count : {count_upper}")
=

```
5. Remove All Spaces from a String.
```

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

```
9. Check whether Two Strings are Anagrams.
```

```
10. Count the Number of Occurrences of a Substring (without using .count()).
```

```
11. Replace Every Space with a Hyphen (-).
```

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
