# Aho-Corasick pattern matching algorithm (C++)

This is a C++ implementation of the popular pattern matching algorithm published by [Alfred V. Aho and Margaret J. Corasick in 1975](https://dl.acm.org/doi/10.1145/360825.360855). It matches an input pattern against a dictionary of words with linear time (O(n)) and O(m) space complexity where n is the length of the input pattern and m the combined length of all the search patterns in the dictionary.

The algorithm is based on traversing a trie structure, which reflects a dictionary of searchable words. Each trie node represents a state, i.e. characters of the words in the dicitionary. When parsing an input pattern, each new character of the input pattern is searched among the child nodes of the current state in the trie starting from the root node. If the character is found, the state is updated to the new state (character), if not, the state is updated to a fail state, which represents the longest prefix of the pattern that could potentially still be found in the dictionary. For each search pattern in the dictionary, an object is stored in the matching state, i.e. the last character of the search pattern. It is either returned or a function is called using it as input parameter as is the case in this implementation.

Personally, I found these three short videos to be very explanatory when I searched for the algorithm: [building trie](https://www.youtube.com/watch?v=ePafMI_rSJg), [building fail paths](https://www.youtube.com/watch?v=qPyhPXPl3T4), and [pattern matching](https://www.youtube.com/watch?v=IcXimoT_YXA).

## Implementation

The implementation is intentionally kept as light-weight as possible as the whole trie (dictionary) is kept in memory while the input pattern is parsed. Each trie node stores a container (unordered_map) containing all child nodes, a pointer to the fail node and a pointer (unique_ptr) to an object stored for each match. The API is flexible to enable a wide range of applications and to keep the trie size minimal for each application.

## Usage

The trie object can be created using the default constructor adding the search patterns into the dictionary word by word:

```cpp
auto matchObj = [](const string& s){return s.size()};
AhoCorasick AC;
AC.addPattern("a", matchObj);
AC.addPattern("ab", matchObj);
AC.addPattern("aab", matchObj);
AC.prepare();
```

using an iterator range:

```cpp
auto matchObj = [](const string& s){return s.size()};
vector<string> dict = {"a", "ab", "aab"};
AhoCorasick AC;
AC.addPattern(dict.begin(), dict.end(), matchObj);
```

or, it can be constructed directly:

```cpp
auto matchObj = [](const string& s){return s.size()};
vector<string> dict = {"a", "ab", "aab"};
AhoCorasick AC(dict.begin(), dict.end(), matchObj);
```

For each search pattern, an object is stored in the corresponding trie state, which is calculated using an unary predicate passed to the addPattern member function or contructor.

The input pattern is processed using the match memeber function, which takes a reference to a container or a range of iterators and an binary predicate as arguments. The binary predicate is called on each match with the previously stored match object and iterator to the current position in the pattern as input argument. This might help to reduce the size of the match object, as alternatively the whole match pattern had to be stored to achieve this functionality. The match member function continues matching if the binary predicate returns true, otherwise it returns after the first match.

```cpp
string text = {aaaabccadjbabaabad};
match(text.begin(), text.end(), [](InputIt pos, int len){
string m(pos - len, pos); cout << m << endl; return true;
});
```

or 

```cpp
match("aaaabccadjbabaabad", [](InputIt pos, int len){
string m(pos - len, pos); cout << m << endl; return true;
});
```
