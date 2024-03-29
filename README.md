# Slack Black Theme

![screenshot of theme](example-screenshot.png)

The following code is required in order to modify the colors of Slack:

```javascript
document.addEventListener("DOMContentLoaded", function() {

   let webviews = document.querySelectorAll(".TeamView webview");

   const cssPath = 'https://raw.githubusercontent.com/sam-berry/slack-theme/master/black.css';
   let cssPromise = fetch(cssPath).then(response => response.text());

   let customCustomCSS = `
   :root {
      --primary: #09F;
      --text: #ffffff;
      --background: #000000;
      --background-elevated: #222;
   }
   `

   cssPromise.then(css => {
      let s = document.createElement('style');
      s.type = 'text/css';
      s.innerHTML = css + customCustomCSS;
      document.head.appendChild(s);
   });

   webviews.forEach(webview => {
      webview.addEventListener('ipc-message', message => {
         if (message.channel == 'didFinishLoading')
            cssPromise.then(css => {
               let script = `
                     let s = document.createElement('style');
                     s.type = 'text/css';
                     s.id = 'slack-custom-css';
                     s.innerHTML = \`${css + customCustomCSS}\`;
                     document.head.appendChild(s);
                     `
               webview.executeJavaScript(script);
            })
      });
   });
});
```

*This needs to be appended at the very bottom (outside of the block of code already in there, ie, at the document level).*

*The file can be found: `/Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static/ssb-interop.js`*

## To Install

1. Make sure Slack is closed
1. Download this GitHub repositoy
1. Navigate to the root of the repo
1. Run:

```
rm /Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static/ssb-interop.js \ 
&& cp ssb-interop.js $_
```
