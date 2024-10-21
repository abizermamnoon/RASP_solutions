# Problem 1
Write an s-op that contains the indices in reverse order.

```
def reverse_indices(seq) {
    return aggregate(select(indices, length-indices-1, ==), seq);
}
reverse = reverse_indices(indices);
     s-op: reverse
         Example: reverse("hello") = [4, 3, 2, 1, 0] (ints)
reverse("hello");
         =  [4, 3, 2, 1, 0] (ints)
```

# Problem 2
Write a function that "rotates" the input text by the specified number of characters.

```
def rotate(seq, count) {
    return aggregate(select(indices, indices-count, ==), seq, "x") if count<=indices else aggregate(select(indices, indices+length-count, ==), seq, "x");
}

rotate(tokens, 0)("hello")
;
         =  [h, e, l, l, o] (strings)
rotate(tokens, 1)("hello")
;
         =  [o, h, e, l, l] (strings)
rotate(tokens, 2)("hello");
         =  [l, o, h, e, l] (strings)
```

# Problem 3
Write a function that takes a sequence as input and "swaps every letter with its neighbor". Specifically, for every even index i, positions i and i+1 will be swapped; if the length of the sequence is odd, then the last element should not move.

```
def swap(seq){
  return aggregate(select(indices, indices+1, ==), tokens, "x") if indices%2==0 else aggregate(select(indices, indices-1, ==), tokens, "x");
  }

def newswap(seq){
  return aggregate(select(indices, indices, ==), tokens, "x") if indices==length-1 and length%2==1 else swap(seq);
  }

newswap(tokens)("hello")
;
         =  [e, h, l, l, o] (strings)
swap(tokens)("ababab");
         =  [b, a, b, a, b, a] (strings)
```

# Problem 4
Write a function that returns the maximum value in the sequence repeated for every position.

```
def maxseq(seq) {
  return load_from_location(sorted, lastone);
  }

set example "ababcabab"
sorted = sort(tokens, tokens);
     s-op: sorted
         Example: sorted("ababcabab") = [a, a, a, a, b, b, b, b, c] (strings)
lastone = length - 1;
     s-op: lastone
         Example: lastone("ababcabab") = [8]*9 (ints)
maxseq(tokens);
     s-op: out
         Example: out("ababcabab") = [c]*9 (strings)
```

# Problem 5
Write a function that returns the maximum value in the sequnce only of the tokens seen so far.

```
set example "hello$     "

dollar_loc = select_from_first(tokens, "$");
     selector: dollar_loc
         Example:
                             h e l l o $
                         h |           1
                         e |           1
                         l |           1
                         l |           1
                         o |           1
                         $ |           1
                           |           1
                           |           1
                           |           1
                           |           1
                           |           1

pos_dollar = aggregate(dollar_loc, indices);
     s-op: pos_dollar
         Example: pos_dollar("hello$     ") = [5]*11 (ints)

def secondhalf(seq){
  return aggregate(select(indices, pos_dollar+pos_dollar-indices, ==), seq, " ");
  }
     console function: secondhalf(seq)

def reverse_ag(seq){
  return seq if indices<=pos_dollar else secondhalf(seq);
  }
     console function: reverse_ag(seq)

reverse_ag(tokens);
     s-op: out
         Example: out("hello$     ") = [h, e, l, l, o, $, o, l, l, e, h] (strings)

set example "hello$ "
reverse_ag(tokens);
     s-op: out
         Example: out("hello$ ") = [h, e, l, l, o, $, o] (strings)

set example "hello$X"
reverse_ag(tokens);
     s-op: out
         Example: out("hello$X") = [h, e, l, l, o, $, o] (strings)

set example "hello$XXXXXXXXXX"
reverse_ag(tokens);
     s-op: out
         Example: out("hello$XXXXXXXXXX") = [h, e, l, l, o, $, o, l, l, e, h,  ,  ,  ,  ,  ] (strings)
```

# Problem 6
Write a function that counts the number of times a certain token appears in the input sequence.

```
def howmany(seq, atom){
  return selector_width(select(seq, atom, ==)) if contains(seq, atom) else "0";
  }

howmany(tokens, "a")("hello");
         =  [0]*5 (strings)
howmany(tokens, "h")("hello");
         =  [1]*5 (ints)
howmany(tokens, "l")("hello");
         =  [2]*5 (ints)
```

# Problem 7
Write a function that counts the number of times a certain token has appeared in the input sequence so far.


# Problem 8
Write a function that returns the minimum value in the sequnce only of the tokens seen so far.

```
def minseq(seq) {
  return load_from_location(sorted, 0);
  }

minseq(tokens);
     s-op: out
         Example: out("hello") = [e]*5 (strings)
```
