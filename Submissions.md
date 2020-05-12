# Problem

Design a method to find the frequency of occurences of any given word in a book. What if we were running this algorithm multiple times?

### Python solution by @Andy

```python
import collections
import string

def addWordCount(fileName: str, target: str, countDict: collections.defaultdict) -> None:
    
    # only parse if word has not been looked up previously
    if not countDict.get(target):
        with open(fileName, 'r') as inFile:  # multiple calls to the file could be expensive. may need to change
            for line in inFile:
                line = line.strip()
                if line:  # filters out blank lines in the script lol 
                    for word in line.split():
                        word = word.rstrip(string.punctuation).lower()  # removes trailing punctuation & makes word non-case sensitive
                        if word == target:
                            countDict[target] += 1


if __name__ == "__main__":
    fileName = "BeeMovieScript.txt"
    wordCounts = collections.defaultdict(int)
    
    tests = [
        "i",
        "bee",
        "bees",
        "name",
        "who",
        "i"
    ]

    for test in tests:
        addWordCount(fileName, test, wordCounts)

    for word, count in wordCounts.items():
        print(f"{word}: {count}")
```

### Python solution by @kishan

```python
from collections import defaultdict

def find_occurences_of_word(your_word):
    counter = defaultdict(int)

    with open('file.txt', 'r') as f:
        for line in f:
            for word in line.split():                
                counter[word] += f

    return counter[your_word]
```

### Python one-liner by @madhavarshney

```python
def get_word_count (str, word): return reduce(lambda c, w : (c + 1) if word == (w.translate(w.maketrans('', '', string.punctuation).lower())) else c, str.split(), 0);
```

### JavaScript one-liner by @madhavarshney

```js
const getWordCount = (str, word) => str.split(" ").reduce((acc, word) => word.trim().toLowerCase() === word ? acc + 1 : acc, 0);
```

### Python solution by @TryExceptElse

```python
from pathlib import Path
import re
import string


CHUNK = 2 ** 20  # 1MB

def count_word(book_path: Path, word: str) -> int:
    count = 0
    last_word_segment = ''  # Stores word section preceding chunk boundary.
    pattern = re.compile(rf'\b{word}\b', re.IGNORECASE)
    with book_path.open() as book:
        for chunk in book.read(CHUNK):
            # Count word instances in chunk.
            chunk = last_word_segment + chunk
            count += len(pattern.findall(chunk))

            # Find last whitespace and store all chars after it so that
            # we don't split a word with the chunk boundary.
            last_whitespace = chunk.rfind(
                string.whitespace + string.punctuation
            )
            last_word_segment = chunk[last_word_segment + 1:]

    return count
```


# Yao-Tier Problem

You have a large text file containing words. Given any two words, find the shortest distance (in terms of number of words) between them in the file.  If the operation will be repeated many times for the same file (but different pairs of words), can you optimize your solution?

### Python solution by @Yao

```python
with open("bee-movie-sanitized", 'r') as book_in_file:
    WORDS = book_in_file.read().split()


def get_word_distance(word0, word1):
    first_word_i = None
    cur_start_word = None

    # fast forward to first match of word0/1
    for cur_i, cur_word in enumerate(WORDS):
        if cur_word == word0 or cur_word == word1:
            cur_start_word = cur_word
            first_word_i = cur_i
            break

    # if not cur_start_word:
    #     do error stuff

    cur_start_i = first_word_i
    cur_min_distance = len(WORDS)
    for cur_i, cur_word in list(enumerate(WORDS))[first_word_i + 1:]:
        if cur_word == word0 or cur_word == word1:
            if cur_start_word != cur_word:
                cur_start_word = cur_word
                if (cur_distance := cur_i - cur_start_i) < cur_min_distance:
                    cur_min_distance = cur_distance
                    # debug
                    print(f"cur_distance updated: {cur_start_i} to {cur_i}: len {cur_distance}")

            cur_start_i = cur_i

    # if cur_min_distance == len(WORDS):
    #     do error stuff        

    return cur_min_distance


if __name__ == "__main__":
    print(WORDS[1024:1027+1])

    print(get_word_distance("it", "honey"))
    print(get_word_distance("to", "is"))
```

### Python solution by @kishan

```python
import re
import string
from collections import defaultdict

def strip_word(word):
    mask = string.ascii_letters + string.digits
    return re.sub('[^%s]' % mask, '', word)

def map_file(filename):
    word_map = defaultdict(list)

    with open(filename, 'r') as f:
        index = 0
        for line in f:
            for word in line.split():                
                stripped_word = strip_word(word).lower()
                if stripped_word:
                    word_map[stripped_word].append(index)
                    index += 1

    return word_map

def magnitude(index_a, index_b):
    return abs(index_a - index_b)

def compare_words(word_a, word_b, word_map):
    if word_a not in word_map:
        raise Exception(f"{word_a} not in index")
    if word_b not in word_map:
        raise Exception(f"{word_b} not in index")

    a_words = word_map[word_a]
    b_words = word_map[word_b]
    max_iteration = max(len(a_words), len(b_words))

    min_distance = magnitude(a_words[0], b_words[-1])
    word_a_index = 0
    word_b_index = 0
    try:
        for i in range(0, max_iteration):
            distance = magnitude(a_words[word_a_index], b_words[word_b_index])
            if distance < min_distance:
                min_distance = distance
            
            new_distance_a = magnitude(a_words[word_a_index+1], b_words[word_b_index])
            new_distance_b = magnitude(a_words[word_a_index], b_words[word_b_index+1])

            if new_distance_a > new_distance_b:
                word_b_index += 1
            else:
                word_a_index += 1
    except IndexError:
        pass

    return min_distance

word_map = map_file('beemovie.txt')
assert compare_words('barry', 'bee', word_map) == 1
```