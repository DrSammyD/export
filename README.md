# import/export

A [RequireJS](http://requirejs.org)/AMD loader plugin for loading text
resources and exporting separate strings separated on `<!-- export name="someProperty" -->`.

Known to work in RequireJS, but should work in other AMD loaders that support
the same loader plugin API.

## Docs

This is a fork of the Text plugin. All the documentation of the text plugin still applies.
See the [RequireJS API text section](http://requirejs.org/docs/api.html#text).

## Usage

Here's an example `some/module.html`
```html
<!-- export name:'things.stuff' -->
<p>stuff got imported</p>
<!-- export --><!-- export name:'things.otherStuff' -->
<p>other stuff got imported</p>
<!-- export -->
```
You can specify an export text file resource as a dependency like so:

```javascript
require(["text!some/module.html!export"],
    function(html) {
        var obj = html.things;
        var subProp = obj.stuff;
        var otherSubProp = obj.otherStuff;
    }
);
```

Another thing we can do is import it directly into another text module

Here's an example `some/parentModule.html`
```html
<h4><!-- import name="text!some/module.html!export" path="again.stuff" --></h4>
<h4><!-- import path="again.otherStuff" name="text!some/module.html!export" --></h4>
<h4><!-- import name="text!some/module.html" --></h4>
```

These will now import the strings from the other module directly into the `<h4>` tags;


The `<!-- export -->` comment is found through regex, so nested objects are not possible. It ignores exports with no property name, but any `<!-- export -->` comment will end the previous export.
[The regex works like this](https://regex101.com/r/qB2qY7/1).

It then removes the `<!-- export -->` comments from the text. All spaces are preserved.

It also works with the strip command

```javascript
require(["text!some/module.html!export!strip"],
    function(html) {
        //Any text in the header will be removed
        //including any <!-- export --> comments
    }
);
require(["text!some/module.html!strip!export"],
    function(html) {
        //Feel free to change the order of the
        //plugin tags
    }
);
require(["text!some/module.html!strip"],
    function(html) {
        //Again this is a fork, so this will work as normal
    }
);
```
The r.js build will inline all the text like so

```
require(["text!some/module.html!export"],
    function(html) {
       {'someProperty':'text between\n\rnext export comment','nextProperty':'text in the next export with a name'}
    }
);
```

## History
This plugin is a fork of the text plugin from [requirejs repo](https://github.com/requirejs/text)
If this project is out of date with the original requirejs text plugin, submit a pull request or let me know.
