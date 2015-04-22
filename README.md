# resig
JavaScript Micro-Templating from John Resig 
[JavaScript Micro-Templating](http://ejohn.org/blog/javascript-micro-templating/)


Super-simple templating function that is fast, caches quickly, and is easy to use in the browser or nodejs.

```html
<script type="text/html" id="tmpl">
  <div id="<%= id %>" class="<%= (i % 2 == 1 ? "even" : "odd") %>">
    <div>
      <img src="<%= profileImageUrl %>"/>
    </div>
    <div class="contents">
      <p><b><a href="/<%= fromUser %>"><%= fromUser %></a>:</b> <%= text %></p>
    </div>
  </div>
</script>
```

You would use it from script like so:
```js
var resig = require('resig');
var results = document.getElementById('results');
results.innerHTML = resig('tmpl', dataObject);
```

You could pre-compile the results for later use. If you call the templating function with only an ID (or a template code) then it’ll return a pre-compiled function that you can execute later:
```js
var userTmpl = resig('tmpl'), html = '';
for (var i = 0; i < users.length; i++ ) {
  html += userTmpl(users[i]);
}
```

Finally, here's the code in full:
```js
// Simple JavaScript Templating
// John Resig - http://ejohn.org/ - MIT Licensed
(function(){
  var cache = {};
 
  this.tmpl = function tmpl(str, data){
    // Figure out if we're getting a template, or if we need to
    // load the template - and be sure to cache the result.
    var fn = !/\W/.test(str) ?
      cache[str] = cache[str] ||
        tmpl(document.getElementById(str).innerHTML) :
     
      // Generate a reusable function that will serve as a template
      // generator (and which will be cached).
      new Function("obj",
        "var p=[],print=function(){p.push.apply(p,arguments);};" +
       
        // Introduce the data as local variables using with(){}
        "with(obj){p.push('" +
       
        // Convert the template into pure JavaScript
        str
          .replace(/[\r\t\n]/g, " ")
          .split("<%").join("\t")
          .replace(/((^|%>)[^\t]*)'/g, "$1\r")
          .replace(/\t=(.*?)%>/g, "',$1,'")
          .split("\t").join("');")
          .split("%>").join("p.push('")
          .split("\r").join("\\'")
      + "');}return p.join('');");
   
    // Provide some basic currying to the user
    return data ? fn( data ) : fn;
  };
})();
```
