## Change default bootstrap with regular one

### Install sass, copycat, bootstrap-sass and jquery

```terminal
$ npm install --save-dev sass-brunch copycat-brunch
$ npm install --save bootstrap-sass jquery
```

### Change brunch-config.js
```javascript
exports.config = {
  // ..
      order: {
        after: ["web/static/css/app.scss"] // concat app.css last
      }
  // ..
    copycat: {
      "fonts": ["node_modules/bootstrap-sass/assets/fonts/bootstrap"] // copy node_modules/bootstrap-sass/assets/fonts/bootstrap/* to priv/static/fonts/
    },
    sass: {
      options: {
        includePaths: ["node_modules/bootstrap-sass/assets/stylesheets"], // tell sass-brunch where to look for files to @import
        precision: 8 // minimum precision required by bootstrap-sass 
      }
  // ..
  npm: {
    enabled: true,
    globals: { // bootstrap-sass' JavaScript requires both '$' and 'jQuery' in global scope
      $: 'jquery',
      jQuery: 'jquery',
      bootstrap: 'bootstrap-sass' // require bootstrap-sass' JavaScript globally
    }
```

### Delete web/static/css/phoenix.css

### Rename web/static/css/app.css to web/static/css/app.scss

### Create empty web/static/css/_custom.scss

### Edit web/static/css/app.scss

```css
$icon-font-path: "/fonts/";
@import "bootstrap";

@import "custom";
```
