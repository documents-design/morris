# Morris
Morris is a locale-defined typographic rules fixer.
The set of rules we're currently building is for the french language.
It's designed to operate on raw text.

Its counterpart to work in a DOM environment and add real layout rules, such as line awareness, content block size awareness, hyphenation (or avoidance of), will be found at [@documents-design/morris-dom.git](https://github.com/documents-design/morris-dom.git).

## Usage

```
const m = new Morris(); // "frenchPlaintextRules" rules are loaded by default.
m.format("string");

const m = new Morris(frenchPlaintextRules); // equivalent
m.format("string");

const m = new Morris([frenchPlaintextRules, frenchHtmlAwareRules]); // two sets of rules will be merged.
m.format("string");

```


## Rules
All rules satisfy RuleInterface.

Some rules are stateless, and just find/replace operations.

```js
{
    description: "Replaces three dots with an ellipsis",
    find: /\.{3}/gi,
    replace: `${LIST.ELLIPSIS}`
}
```

Some rules must be stateful : they don't need a `find` property.  
Their `replace` property is a function taking the whole string as an argument.  
You're then free to iterate on it, use a parsing strategy, [...]

```ts
{
    description: "Replaces quotes with french quotes",
    replace(str: string): string {
      let open = false
      let output = ""
      for (const char of str) {
          if (char === "\"") {
              output += open ? LIST.RQUOTE : LIST.LQUOTE
              open = !open
              continue
          }
          output += char
      }
      return output
    }
}
``` 

## Current implemented rules

```
> let m = require('./dist/index');
> m.MorrisTest.testHtmlAware();
Replaces three dots with an ellipsis : Il n'est pas évident , de "régler" le texte:en effet , les règles de ponctuation sont complexes!Ah ,les ellipses… C'est la 123eme fois qu'on en parle!
Replaces quotes with french quotes : Il n'est pas évident , de «régler» le texte:en effet , les règles de ponctuation sont complexes!Ah ,les ellipses… C'est la 123eme fois qu'on en parle!
Ensures non-breaking space after opening quote : Il n'est pas évident , de « régler» le texte:en effet , les règles de ponctuation sont complexes!Ah ,les ellipses… C'est la 123eme fois qu'on en parle!
Ensures non-breaking space after closing quote : Il n'est pas évident , de « régler » le texte:en effet , les règles de ponctuation sont complexes!Ah ,les ellipses… C'est la 123eme fois qu'on en parle!
Removes spaces before simple punctuations : Il n'est pas évident, de « régler » le texte:en effet, les règles de ponctuation sont complexes!Ah,les ellipses… C'est la 123eme fois qu'on en parle!
Ensures a space after a simple or double punctuation : Il n'est pas évident, de « régler » le texte: en effet, les règles de ponctuation sont complexes! Ah, les ellipses… C'est la 123eme fois qu'on en parle!
Ensures a single non-breaking space before a double punctuation : Il n'est pas évident, de « régler » le texte : en effet, les règles de ponctuation sont complexes ! Ah, les ellipses… C'est la 123eme fois qu'on en parle !
Ensures a single space after a colon or semicolon : Il n'est pas évident, de « régler » le texte : en effet, les règles de ponctuation sont complexes ! Ah, les ellipses… C'est la 123eme fois qu'on en parle !
Normalizes ordinal numbers : Il n'est pas évident, de « régler » le texte : en effet, les règles de ponctuation sont complexes ! Ah, les ellipses… C'est la 123ème fois qu'on en parle !
Uses sup elements for numbers : Il n'est pas évident, de « régler » le texte : en effet, les règles de ponctuation sont complexes ! Ah, les ellipses… C'est la 123<sup>ème</sup> fois qu'on en parle !
```

---

## Contributing

We'd accept contributions, but this project should mature a bit before. As long as modifications are lightweight and don't pull external dependencies outside of `devDependencies`, you're free to contribute. The style guide is what `node_modules/.bin/tslint --fix` produces. 

### Références

- https://en.wikipedia.org/wiki/Whitespace_character#Unicode
- http://j.poitou.free.fr/pro/html/typ/resume.html
- https://fr.wikipedia.org/wiki/Lexique_des_r%C3%A8gles_typographiques_en_usage_%C3%A0_l%27Imprimerie_nationale
