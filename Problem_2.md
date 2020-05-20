# Problem 2

Write a method that finds the maximum of two numbers. You should not use if-else or any other comparison operator.

### Python solution by @frenchfries

```python
def maximum(a, b):
    return ((a + b) + (((a-b)**2)**(1/2))) / 2
```

### Python solution by @Yao

```python
def get_fake_comparison_buckets(a, b):
    fake_comparison_buckets = [""] * 3

    def store_in_bucket(e1, e2):
        diff = e1 - e2
        try:
            bucket = diff // abs(diff) + 1
        except ZeroDivisionError:
            bucket = 1

        fake_comparison_buckets[bucket] = str(e1)

    store_in_bucket(a, b)
    store_in_bucket(b, a)
    return fake_comparison_buckets


def print_max_from_fake_comparison_buckets(fake_comparison_buckets):
    print("The larger is", "".join(fake_comparison_buckets[1:]))


if __name__ == "__main__":
    print_max_from_fake_comparison_buckets(get_fake_comparison_buckets(0, 1))
    print_max_from_fake_comparison_buckets(get_fake_comparison_buckets(2, 1))
    print_max_from_fake_comparison_buckets(get_fake_comparison_buckets(1, 1))
```

### C solution by @Yao

```c
unsigned int intAbs(int num) {
    const unsigned int VALUE_BIT_COUNT = sizeof(int) * 8 - 1; // 31;
    const unsigned int VALUE_BIT_MASK = (unsigned int)-1 / 2; // 0x7FFFFFFF

    unsigned int signBit = (unsigned int)num >> VALUE_BIT_COUNT;
    unsigned int xorPad = signBit * VALUE_BIT_MASK;
    return (((unsigned int)num & VALUE_BIT_MASK) ^ xorPad) + signBit;
}
```

# Yao-Tier Problem 2

Design an algorithm to find the smallest K numbers in an array.

### Python solution by @frenchfries

```python
def k_smallest(lst, k):
    lst.sort()
    return lst[:k]
```

### Python solution by @kishan

```python
import heapq

def smallest_k(heap):
    heapq.heapify(heap)
    while True:
        yield heapq.heappop(heap)

gen = smallest_k([6,7,2,5,1,8])
assert [next(gen) for x in range(5)] == [1,2,5,6,7]
```

### Python solution by @FractalBach

```python
def k_smallest(k, arr):

    if k <= 0:
        return []
    if k >= len(arr) or len(arr) == 1:
        return arr

    highest = arr[0]
    ssl = [ highest ]     # Sorted Smallest List

    for x in arr[1:]:

        # Still adding numbers to the small list
        # Keep It Sorted
        if len(ssl) < k:
            ssl.append(x)
            ssl.sort()
            if x > highest:
                highest = x
            continue

        # List is at max capacity already,
        # If needed, remove the highest number,
        # and add in the new number.
        # Keep It Sorted.
        if x < highest:
            highest = x
            ssl[k-1] = x
            ssl.sort()

    return ssl


if __name__ == '__main__':
    arr = [99, 1, 444, 4, 555, 3, 999, 2, 93]
    k = 4
    print(k_smallest(k, arr))
```

### A more elaborate solution by @frenchfries

```python
def maximum(a, b):
    return ((a + b) + abs(a - b)) / 2


def max_in_lst(lst):

    if len(lst) == 1:
        return lst[0]
    else:
        return maximum(lst[0], max_in_lst(lst[1:]))


def same(lsta):
    a = lsta[0]
    for b in lsta:
        if a != b:
            return False
    return True


def k_smallest(lst, k):

    if same(lst):
        return lst[:k]

    for _ in range(len(lst) - k):
        current_max = max_in_lst(lst)
        for idx, value in enumerate(lst):
            if value == current_max:
                del lst[idx]
    return lst
```

### Python solution by @david tso

```python
def put_a_number(a_list):
    for i in range(len(a_list) - 1):
        if a_list[i] > a_list[i + 1]:
            num = a_list[i + 1]
            a_list[i + 1] = a_list[i]
            a_list[i] = num


    return a_list[i]

def arrage_a_list(a_list):
    for i in range(len(a_list) - 1):
        put_a_number(a_list)

    return a_list

def find_smallests(k, a_list):
    arrage_a_list(a_list)
    new_list = []
    i = 0
    while (i < k):
        new_list.append(a_list[i])
        i += 1

    return new_list

my_list = [44,32,6,22]
print(find_smallests(2, my_list))
```
