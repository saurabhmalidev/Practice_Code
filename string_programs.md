1. Reverse a String
```
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
#reversed() + join()
s = "abcdef"
print("".join(reversed(s)))
#------------------------
```
2. Check whether a String is a Palindrome.
```

```
3. Count the Number of Vowels in a String.
```

```
4. Count Uppercase and Lowercase Characters.
```

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
