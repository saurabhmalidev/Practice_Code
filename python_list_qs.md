1. Find the largest and smallest number in a list without using max()/min().
2. Reverse a list without using reverse() or slicing.
3. Remove duplicates from a list while preserving order.
4. Count occurrences of each element in a list.
5. Check if a list is a palindrome.
6. Find the second largest element in a list.
7. Merge two lists and sort the result without using sort()/sorted().
8. Sum all elements in a list without using sum().
9. Find all pairs in a list that sum to a given target (two-sum, list version).
10. Rotate a list left by k positions in-place.
11. Flatten a nested list (list of lists) into a single list.
12. Find the intersection and union of two lists without using set().
13. Move all zeros in a list to the end while keeping the order of non-zero elements.
14. Find the missing number in a list of 1 to n.
15. Group anagrams together from a list of strings.
16. Find the majority element (appears more than n/2 times) without extra space.
17. Find the maximum sum of a contiguous subarray (Kadane's algorithm).
18. Find the longest increasing subsequence in a list.
19. Given a list of intervals, merge all overlapping intervals.
20. Find the median of two sorted lists in O(log(min(n,m))) time.
21. Find the trapping rainwater problem — max water trapped between bars represented as a list.
22. Given a list of stock prices, find max profit with at most two transactions.
23. Find all unique triplets in a list that sum to zero (3Sum).
24. Implement a sliding window maximum for a list given window size k.

====================================
====================================

**Q1. Find the largest and smallest number in a list without using max()/min().**

NOTE : understand why we used two if here, why no if and elif? How elif works after if?
NOTE : why we didnt use the else condition here?
```
lst = [21, 7, 2, 9, 4]    
#Validation
if len(lst) == 0:
    print("List has no elements")
else:
    maxele =  float('-inf')
    smallele =  float('+inf')
#logic
for i in lst:
    if i > maxele:
        maxele = i
    if i < smallele : 
        smallele = i
print(maxele)
print(smallele)
```

**Q2. Reverse a list without using reverse() or slicing.**
```
lst = [1,3,5,12,44,132,0,55]

for i in range(int(len(lst)/2)):
     temp = lst[i]
     lst[i] = lst[-(i+1)]
     lst[-(i+1)] = temp

print(lst)
```

**Q3. Remove duplicates from a list while preserving order.** **| IMPORTANT : No LIST, use SET**
NOTE:  Dont use seen, use set, how?
```
lst = [1,3,5,12,5,44,44,132,0,1,55]
seen = []
for i in range(len(lst)):
    if lst[i] in seen:
        pass
    else:
        seen.append(lst[i])
print(seen)

# works, but it's O(n²) time complexity, and that's a real problem, not a nitpick.
# if lst[i] in seen does a linear scan through the seen list every single time, for every element.
# On a list of 10,000 items that's 100 million operations.
```
```
lst = [1,3,5,12,5,44,44,132,0,1,55]

seen = set()    # starts empty
result = []     # start empty

for i in range(len(lst)):
    if lst[i] not in seen:
        seen.add(lst[i])         # addint to set 
        result.append(lst[i])    # adding to list
print(result)
```
```
# When seen is a list, num in seen works by scanning item by item, from the start, comparing each one to num,
until it finds a match or reaches the end. If seen has 1000 items and your number isn't there, Python compares
against all 1000 before saying "not found." That's O(n) — the cost grows linearly with the size of seen.

# When seen is a set, Python uses a hash table internally. It computes a hash of num and jumps almost directly
to where that value would be stored, without scanning anything. Whether seen has 10 items or 10 million, the
lookup takes roughly the same tiny amount of time — O(1), constant time.
```

**Q4. Count occurrences of each element in a list.**

NOTE :  No need of seen here, seen works, but it's O(n²) time complexity
```
lst = [1,3,5,12,5,44,44,132,0,1,55]
my_dict = {}
for i in lst:
    if i in my_dict:
        my_dict[i] += 1
    else:
        my_dict[i] = 1

for key, value in my_dict.items():
    print(f"{key} : {value}")
```
```
from collections import Counter
lst = [1,3,5,12,5,44,44,132,0,1,55]
freq = Counter(lst)
print(freq)
```

**Q5. Check if a list is a palindrome**

NOTE : A  palindrome is any sequence of characters that reads the same forward and backward.
Palindromes can be words, phrases, numbers, or names.

```
lst = [1,3,15,12,5,5,12,15,3,1]
flag = True
for i in range(int(len(lst)/2)):
    if lst[i] != lst[-(i+1)]:
        flag = False
        break
    else:
        pass

if flag:
    print("Palindrome")
else:
    print("Not a Palindrome")
```
```
# WROTE A DEAD CODE LOGIC : No need for else it does nothing, below is enoough.
for i in range(int(len(lst)/2)):
    if lst[i] != lst[-(i+1)]:
        flag = False
        break
```

**6. Find the second largest element in a list. | IMPORTANT**
NOTE : Take care of 2nd element.
```
lst = [21, 21, 7, 2, 9, 44]

if len(lst) < 2:
    print("No second largest")
else:
    largest = float('-inf')
    second = float('-inf')

    for num in lst:
        if num > largest:
            second = largest
            largest = num                                              
        
        elif num != largest and num > second:    #<---------------TOOO IMPORTANT, Two conditions
            second = num

    print(f"Largest : {largest}")
    if second == float('-inf'):
        print("No second largest")
    else:    
        print(f"Second Largest : {second}")
```  

**7. Merge two lists and sort the result without using sort()/sorted().**


**8. Sum all elements in a list without using sum().**

```
lst = [1,2,3,4,5]
result = 0
for i in lst:
    result = i + result
print(result)
```
**9. Find all pairs in a list that sum to a given target (two-sum, list version).**
```
# aka TWO SUM Problem
lst = [0,1,2,3,4,5]
target = 5
for i in range(len(lst)):
    for j in range(i+1,len(lst)):
        if lst[i] + lst[j] == target:
            print(f"{lst[i]},{lst[j]}")
        else:
            pass
```

**10. Rotate a list left by k positions in-place.**
```
** Q. **
lst = [0,1,2,3,4,5]
k = 3

# 1st : Not in place solution
temp = []
for i in range(k):
    lst.append(lst.pop(0))

lst.extend(temp)
print(lst)
```

**11. Flatten a nested list (list of lists) into a single list.**
```
lst = [[1,1,1],[2,4,8],[3,9,27]]

result = []
for sub_list in lst:
    for i in sub_list:
        result.append(i)
print(result)
```

**12. Find the intersection and union of two lists without using set().**

```
ints = []
unilist = []
for i range(range(lat1)):
    if lst1[i] in lst2:
        ints.append(lst1[i])
```

**13. Move all zeros in a list to the end while keeping the order of non-zero elements.**
```
```
**14. Find the missing number in a list of 1 to n.**
```
```
**15. Group anagrams together from a list of strings.**
```
```
**16. Find the majority element (appears more than n/2 times) without extra space.**
```
```
**17. Find the maximum sum of a contiguous subarray (Kadane's algorithm).**
```
```
**18. Find the longest increasing subsequence in a list.**
```
```
**19. Given a list of intervals, merge all overlapping intervals.**
```
```
**20. Find the median of two sorted lists in O(log(min(n,m))) time.**
```
```
**21. Find the trapping rainwater problem — max water trapped between bars represented as a list.**
```
```
**22. Given a list of stock prices, find max profit with at most two transactions.**
```
```
**23. Find all unique triplets in a list that sum to zero (3Sum).**
```
```
**24. Implement a sliding window maximum for a list given window size k.**
