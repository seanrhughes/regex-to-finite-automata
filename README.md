# Regex to Finite Automata

## Table of Contents
[Overview](https://github.com/seanrosshughes/regex-to-finite-automata#overview)<br>

[String to Regex](https://github.com/seanrosshughes/regex-to-finite-automata#string-to-regex)<br>
- [Tokens](https://github.com/seanrosshughes/regex-to-finite-automata#tokens)<br>
- [Grammar](https://github.com/seanrosshughes/regex-to-finite-automata#grammar)<br>

[Regex to NFA](https://github.com/seanrosshughes/regex-to-finite-automata#regex-to-nfa)<br>
- [Examples](https://github.com/seanrosshughes/regex-to-finite-automata#examples)<br>
- [NFA / DFA Type](https://github.com/seanrosshughes/regex-to-finite-automata#nfa--dfa-type)

[NFA to DFA](https://github.com/seanrosshughes/regex-to-finite-automata#nfa-to-dfa)<br>
- [Examples](https://github.com/seanrosshughes/regex-to-finite-automata#examples-1)<br>

[Practical Examples](https://github.com/seanrosshughes/regex-to-finite-automata#practical-examples)
- [Matching Phone Numbers](https://github.com/seanrosshughes/regex-to-finite-automata#matching-phone-numbers)
- [Matching Email Addresses](https://github.com/seanrosshughes/regex-to-finite-automata#matching-email-addresses)
- [Matching URLs](https://github.com/seanrosshughes/regex-to-finite-automata#matching-urls)




## Overview
This project was created entirely in `OCaml`, and uses `Dune` as the build system, as well as `Graphviz` for visualization.
The project allows for the conversion of `Regular Expressions`, to `Finite Automata`. To run the code, execute the `viz.bc`
file found in `bin`, using the command `dune exec bin/viz.bc`. The output will appear as `output.png` (Code available upon request). 

## String to Regex
First, the code prompts the user to input a regex via the command line, and then asks if the user wants to convert the regex
to a `Nondeterministic Finite Automation (NFA)` or a `Deterministic Finite Automation (DFA)`. It then lexes that string into 
tokens, and parses those tokens into an `Abstract Syntax Tree (AST)`, representing the regex. It then uses the generated `AST` 
to convert the regex to an `NFA` or `DFA`.

The tokens that are lexed from the string are as follows:
### Tokens

<table>
  <tr>
    <th>Token Name</th>
    <th>Lexical Representation</th>
  </tr>
  <tr>
    <td><code>Tok_Char</code></td>
    <td><code>(any char)</code></td>
  </tr>
  <tr>
    <td><code>Tok_Epsilon</code></td>
    <td><code>ε</code></td>
  </tr>
  <tr>
    <td><code>Tok_Union</code></td>
    <td><code>|</code></td>
  </tr>
  <tr>
    <td><code>Tok_Star</code></td>
    <td><code>*</code></td>
  </tr>
  <tr>
    <td><code>Tok_LParen</code></td>
    <td><code>(</code></td>
  </tr>
  <tr>
    <td><code>Tok_RParen</code></td>
    <td><code>)</code></td>
  </tr>
  <tr>
    <td><code>Tok_Plus</code></td>
    <td><code>+</code></td>
  </tr>
  <tr>
    <td><code>Tok_Question</code></td>
    <td><code>?</code></td>
  </tr>
  <tr>
    <td><code>Tok_End</code></td>
    <td><code>(end of string)</code></td>
  </tr>
</table>

The regex itself has the grammar:
### Grammar

<ul>
  <li>S -> A <code>Tok_Union</code> S | A</li>
  <li>A -> B A | B</li>
  <li>B -> C <code>Tok_Star</code> | C <code>Tok_Plus</code> | C <code>Tok_Question</code> | C</li>
  <li>C -> <code>Tok_Char</code> | <code>Tok_Epsilon</code> | <code>Tok_LParen</code> S <code>Tok_RParen</code></li>
</ul>

## Regex to NFA
As the `AST` of the regex is recursed, and the code reaches new tokens, it generates the `NFA` to match those tokens. It creates new states and properly adds transitions that represent the regex. Each state is separated by an epsilon transition. This section utilizes [Thompson's Construction](https://en.wikipedia.org/wiki/Thompson%27s_construction).

`NFA`s and `DFA`s have the type:<br>

### NFA / DFA Type
```
type ('q, 's) transition = 'q * 's option * 'q

type ('q, 's) nfa_t = {
  sigma: 's list;
  qs: 'q list;
  q0: 'q;
  fs: 'q list;
  delta: ('q, 's) transition list;
}
```

### Examples
Inputting the regex `a|b` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/9aba3679-b682-4987-934e-4055993af668)

Inputting the regex `a+b*` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/754cb6c2-6f13-4af3-8cd2-d8d1dbb39c34)

Inputting the regex `((a|b)*)+` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/d298863b-48fb-4132-9708-3d2d70ffef3b)

Inputting the regex `((a|b|(a(a|b)*))?)+` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/07200d79-1f02-4da4-981a-367851a4af37)


## NFA to DFA 
To convert an `NFA` to a `DFA`, the code uses two main functions. `move`, and `e_closure`. `move` moves an `NFA` on one transition
symbol, and 
returns all states the `NFA` could be in. `e_closure` returns all states the `NFA` could be in after it takes zero or more 
epsilon transitions. This section utilizes [Powerset Construction](https://en.wikipedia.org/wiki/Powerset_construction), also known as
`Subset Construction`.

### Examples
Using the same examples as before, 

Inputting the regex `a|b` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/7a740ec9-0988-4aa3-9251-a2f6bf1a00c4)

Inputting the regex `a+b*` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/5bd8e724-8857-432d-980c-f7272746cd8c)

Inputting the regex `((a|b)*)+` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/2e7cbadd-9f65-4461-9b6d-9f922089f661)

Inputting the regex `((a|b|(a(a|b)*))?)+` generates:<br>

![output](https://github.com/seanrosshughes/regex-to-finite-automata/assets/92600908/75cd2ec5-d136-4242-a733-1c8ddd6fc24f)

## Practical Examples
Regular Expressions are used extensively in text manipulation. Their implementation at their core is mostly finite state machines, 
or in some specialized cases, recursive backtracking. These regular expressions have underlying finite state machines, regardless if 
they are seen by the user or not. Practical use cases often times generate very large finite state machines, so displaying them is 
not always very beneficial, but it is interesting at the very least to see the output.

### Matching Phone Numbers
The regex <br>

`(0|1|2|3|4|5|6|7|8|9)(0|1|2|3|4|5|6|7|8|9)(0|1|2|3|4|5|6|7|8|9)-(0|1|2|3|4|5|6|7|8|9)
(0|1|2|3|4|5|6|7|8|9)(0|1|2|3|4|5|6|7|8|9)-(0|1|2|3|4|5|6|7|8|9)(0|1|2|3|4|5|6|7|8|9)
(0|1|2|3|4|5|6|7|8|9)(0|1|2|3|4|5|6|7|8|9)`<br>

will match a phone number, consisting of three digits, followed by a `-`, followed by three more digits, followed by another `-`, and 
finally four more digits.<br>

The `DFA` it generates is rather large, so please find it in the repository under `phoneNumberDFA.png`

### Matching Email Addresses
The regex <br>

`(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9)+
@(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9).(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9)`<br>

will match any email address, consisting of a username of one or more alphanumeric characters, followed by the 
`@` symbol, followed by a domain name of one or more alphanumeric characters, followed by the `.` symbol, 
followed by a top-level domain of one or more alphanumeric characters.

The `DFA` it generates is rather large, so please find it in the repository under `emailDFA.png`

### Matching URLs
The regex <br>

`(https?://)?www.(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9)+.(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9)+(/(a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|0|1|2|3|4|5|6|7|8|9)+)*`<br>

matches a simple URL. It matches an optional scheme (`https://` or `http://`), followed by `www.`, followed by a domain of one or more alphanumeric characters, followed by a `.` followed by a top-level domain of one or more alphanumeric characters. It is then followed by an optional file path, which consists of a `/` followed by one or more alphanumeric characters, any number of times.

The `DFA` it generates is rather large, so please find it in the repository under `urlDFA.png`


