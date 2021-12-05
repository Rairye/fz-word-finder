# fz-word-finder
Fuzzy match word finder. Supports multiple simultaneous target strings and fuzzy match rules. (No regex or normalization)

## Installation

pip install fz-word-finder

## Overview

This package allows users to perform fuzzy-match searches of multiple target words simultaneously. It supports both space-delimited natural languages (such as English) and non-space-delimited natural languages (such as Japanese or Chinese). 

Users can changes the search settings to:

1.	Ignore or match case
2.	Ignore or match punctuation marks, whitespace characters, diacritics (Ex: “cafe” vs “café”), number variants, etc.
3.	Detect up to three types of text obfuscation:
    1.	Replacement of an alphanumeric character with a punctuation mark
    2.	Insertion of arbitrary whitespace characters/punctuation marks 
    3.	Replacement of a character with a similar-looking one
4.	Search for only whole-word matches, or both whole-word and substring matches

## Search Methods

This package supports three types of search methods:
1.	find_matches() -> This method finds both whole-word and substring matches and returns them as a result
2.	get_word_indices() -> This method returns the indices of all occurrences of words. By default, only whole-word matches are returned, but both whole-word and substring matches can be obtained if needed
3.	has_any_target_word() -> This method searches a string for target words. Returns True if the string contains any of the target words set in the finder. This is the fastest search method.

## Wiki

The wiki (currently under construction) can be found at: 

https://github.com/Rairye/fz-word-finder/wiki

## Quick Code Samples

Note: In practice, you would only need to use one of the three search methods.

### Default settings. Case is ignored.

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_words = ["hey", "doing"]

my_finder.add_target_words(target_words)

source_sentence = "Hey, how are you doing?"

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```


### Case is matched.

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_words = ["hey", "doing"]

my_finder.add_target_words(target_words)

my_finder.set_match_case(True)

source_sentence = "Hey, how are you doing?"

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```

### Whitespace and punctuation marks are ignored by the finder, except for target words that contain whitespace characters or punctuation marks.

```python

from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_word = ["in house"]

my_finder.add_target_words(target_word)

source_sentence = "Our in-house team produces our products in house."

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```

### Whitespace characters and punctuation marks are not mathed by the finder. This generates fuzzy matches if source_sentence contains a substring that is similar to a target word but differs by whitespace characters or punctuation marks.

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_word = ["in house"]

my_finder.add_target_words(target_word)

my_finder.set_match_punct_ws(False)

source_sentence = "Our in-house team produces our products in house."

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```

### Find words in which at least one alphanumeric character was replaced by a punctuation mark.

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_word = ["apples"]

my_finder.add_target_words(target_word)

my_finder.set_use_punct_as_variant(True)

source_sentence = "I am allergic to ap*les."

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```

### Include substrings using get_word_indices().

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_word = ["app"]

my_finder.add_target_words(target_word)
	
source_sentence = "I made an app for people who love apples."

no_ss_index_results = my_finder.get_word_indices(source_sentence)
ss_index_results = my_finder.get_word_indices(source_sentence, whole_words_only = False)

print("No substring results:")
for result in no_ss_index_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nSubstring results:")
for result in ss_index_results.items():
    print("{} : {}".format(result[0], result[1]))
```

### Loading default variants using the variant_tools module

```python

from fz_word_finder.finder import fz_finder
from fz_word_finder.variant_tools import get_variant_names, get_variant_dict

my_finder = fz_finder()

for variant_dict in get_variant_names():
    my_finder.add_variants(get_variant_dict(variant_dict))
```

### Loading default variants without variant_tools

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()
my_finder.load_default_variants()
```

### Detecting obfuscated text

```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()
my_finder.load_default_variants()

target_words = ["email", "telephone number"]

my_finder.add_target_words(target_words)

my_finder.set_use_punct_as_variant(True)
my_finder.set_ignore_punct_ws(True)

source_sentence = "Tell me your e   m@il and t3*3ph0ne-ñumbér."

match_results = my_finder.find_matches(source_sentence)

index_results = my_finder.get_word_indices(source_sentence, whole_words_only = False)

print("Match results:")
for result in match_results.items():
    print("{} : {}".format(result[0], result[1]))

print("\nIndex results:")
for result in index_results.items():
    print("{} : {}".format(result[0], result[1]))
    
print("\n\"{}\" has any target word -> {}".format(source_sentence, my_finder.has_any_target_word(source_sentence, whole_words_only = False)))
```

### fast_search = True vs fast_search = False
```python
from fz_word_finder.finder import fz_finder

my_finder = fz_finder()

target_words = ["telephone", "number", "telephone number"]

my_finder.add_target_words(target_words)

source_sentence = "telephone number"

fast_search_true = my_finder.find_matches(source_sentence)
fast_search_false = my_finder.find_matches(source_sentence, fast_search = False)

print("fast_search True:")
for result in fast_search_true.items():
    print("{} : {}".format(result[0], result[1]))

print("\nfast_search False:")
for result in fast_search_false.items():
    print("{} : {}".format(result[0], result[1]))

```

## Variant Lists

The raw forms of the variant lists can be found at the following repository:

https://github.com/Rairye/variant_lists





