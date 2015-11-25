# UberParser.js
UberParser is designed to be slim simpler and snappy.

## Parser Structure
Every UberParser consists of the following components/steps.

 * Lexicon (Tokens)
 * Grammar Validation (Composition of Tokens)
 * Processing (Transforming Assembled Sentences)

## Designating a lexicon

Defining the available Tokens is simple

```
TOKEN_NAME1 = REGEX1
TOKEN_NAME2 = REGEX2
TOKEN_NAME3 = REGEX3
```
If you need more then one Regex to be considered a token simply make more of them.

```
TOKEN_NAME1 = REGEX1
TOKEN_NAME3 = REGEX2
TOKEN_NAME2 = REGEX3
TOKEN_NAME1 = REGEX4
TOKEN_NAME3 = REGEX5
TOKEN_NAME3 = REGEX6
```
This example would cause:
`TOKEN_NAME1` to be `REGEX1` and `REGEX4`
`TOKEN_NAME2` to be `REGEX3`
`TOKEN_NAME3` to be `REGEX2`, `REGEX5`, and `REGEX6`

## Grammar Validation

Grammars Must boil down all elements to a single element named `P_START`. This element is the only element that will not accept multiple definitions.

In this step each Token that can be grouped with another to make another token will do so until the process can not boil down again or the only result is `P_START`. If it can not be boiled down to this one element the validation will be considered a Failure

## Processing
In this last step the final validated Grammar Tree is passed to a function `processGrammar(tree)`

## Example
This is the Parser used for parsing the grammar files.

lexicon.upl
```
T_IDENTIFIER = /[a-zA-Z][a-zA-Z0-9]+/
T_REGEX = /\/(?:[^/\\]|\\.)*\//
T_STRICT_MATCHER = /"(?:[^"\\]|\\.)*"/
T_STRICT_MATCHER = /'(?:[^'\\]|\\.)*'/
T_MATCHER_QUANTITY = /[+*?]/
T_WHITESPACE = /[' '\t]+/
T_NEXTLINE = /[\n\r]+/
```
grammer.upg
```
P_START = S_DEFINITION? S_DEFINITION_LAST

T__ = T_WHITESPACE? T_NEXTLINE? 
T_MATCHER = T_STRICT_MATCHER T_MATCHER_QUANTITY?
T_MATCHER = T_REGEX T_MATCHER_QUANTITY?
T_MATCHER = T_IDENTIFIER T_MATCHER_QUANTITY?

S_DEFINITION = S_DEFINITION T_NEXTLINE
S_DEFINITION_LAST = T_IDENTIFIER T__ '=' (T__ T_MATCHER)+

```
