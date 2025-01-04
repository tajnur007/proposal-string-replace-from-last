# String.prototype.replaceLast

ECMAScript proposal for `String.prototype.replaceLast`, i.e. `.replaceLast()` method on string.

## Motivation

Replacing a sub-string in a string is a very common programming pattern. 

The proposal has a major concerns: **Semantical**. Which means `clearly representing the operation i want`.

And with the changes. There's a sugar here: Performance. Avoid obvious overhead. And may improve the constant factors in the time complexity.
Even there's not an order of magnitude change. But it's may useful in some performance-sensitive scenarios.

---

ECMAScript currently supports `String.prototype.replace` to replace the first instance of a sub-string/pattern from a string. However, there is no way to replace the **last instance** of a substring in a string.

Currently one of the most common way of achieving this is to **use a regex**:

```js
function replaceLastSubstring(string, substring, replacement) {
  const regex = new RegExp(`(${substring})(?!.*${substring})`); 
  return string.replace(regex, replacement);
}
```

This approach has the downsides, the regular expression itself can be slightly more complex to understand and debug. Performance might be slightly slower due to the regex engine's overhead. If the `substring` contains special characters that have meaning within regular expressions (e.g., ., *, +, ?, $, ^, etc.), the regex needs to be properly escaped to avoid unexpected behavior.

An alternate solution is to combine `.lastIndexOf()` with `.substring()`:

```js
function replaceLastSubstring(string, substring, replacement) {
  const lastIndex = string.lastIndexOf(substring);

  if (lastIndex !== -1) {
    const firstPart = string.substring(0, lastIndex);
    const lastPart = string.substring(lastIndex + substring.length);
    return firstPart + replacement + lastPart;
  }

  return string; 
}
```

The code involves string manipulation (concatenation and substring extraction), which can be less concise than using a single `replaceLast()` method.

Example usage:

```js
const testString = 'aabca';
const replacedString = replaceLastSubstring(testString, 'a', '_');
console.log(replacedString);

// Output: aabc_
```

## Proposed solution

I propose the addition of a new method to the String prototype - `replaceLast`. This would give developers a straight-forward way to accomplish this common, basic operation.

```js
const testString = 'aabca';
const replacedString = testString.replaceLast('a', '_');
```

This would behave the same as [String.prototype.replace](https://262.ecma-international.org/11.0/index.html#sec-string.prototype.replace) but would iterate from the last to the first.

## High-level API

The proposed signature is the same as the existing `String.prototype.replace` method:

```js
String.prototype.replaceLast(searchValue, replaceValue)
```
