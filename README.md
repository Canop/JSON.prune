JSON.prune
==========

[![Chat on Miaou](https://dystroy.org/miaou/static/shields/room-en.svg?v=1)](https://dystroy.org/miaou/8?Javascript)
[![Chat on Miaou](https://dystroy.org/miaou/static/shields/room-fr.svg?v=1)](https://dystroy.org/miaou/3?Code_Croissants)

JSON.prune is a pruning `JSON.stringify` for the very specific cases where you need to stringify big or recursive javascript objects and don't really need the result to be complete.

    var json = JSON.stringify(window); // fails
    var json = JSON.prune(window); // builds a JSON valid string from a pruned version of the
                                   // recursive, deep, and not totally accessible window object
    var prunedWindow = JSON.parse(JSON.prune(window)); // builds a lighter acyclic version of window

JSON.prune also lets you, in case of need, stringify inherited and/or non enumerable properties.

	JSON.prune(window.location, {inheritedProperties:true}); // without inherited properties, FireFox and IE only show an empty object

It's totally useless for at least 99% of js developpers.

JSON.prune.log is a proxy over console.log deep cloning the objects (using JSON.prune) before logging them, in order to avoid the delay problem encountered on non primitive objects logging.

You should not use it frequently, only when you really need to see the objects how they were at logging time.

    // make sure someObject is logged as it was at logging time
    JSON.prune.log(someObject);

Project/Test page
-----------------

[dystroy.org/JSON.prune](http://dystroy.org/JSON.prune)

Include it in a page
-------------------

	<script src=http://dystroy.org/JSON.prune/JSON.prune.js></script>

Use it with CommonJS or in node
-------------------------------

	var prune = require('json-prune');
	var json = prune(obj);

Discussions
-----------

[JavaScript room on Miaou](https://dystroy.org/miaou/8)

Customization: replace the "-pruned-" placeholder
-------------------------------------------------

Here's how are handled by default by JSON.prune the special values needing pruning:

Value | Default
------|--------
`undefined` | Key and value are ommited (same as `JSON.stringify`)
function | Key and value are ommited (same as `JSON.stringify`)
already written or too deep object (cycle prevention) | The `"-pruned-"` string
array with too many elements | Truncation: `JSON.prune` applied to only the start of the array

By specifiying a `replacer` or a `prunedString` in `JSON.prune` options, you can customize those prunings.

The `replacer` function takes 3 arguments:
* the value to replace
* the default replacement value
* a boolean indicating whether the replacement is due to a cycle detection

Returning `undefined` makes `JSON.prune` ommit the property (name and value).

The default value makes it easy to just specify the specific behavior you want instead of implementing the whole standard replacement.

### Example 1: Use an object instead of a string as pruning mark


	var json = JSON.prune(obj, {prunedString: '{}' });

Note: if you want a string to be inserted, don't forget the double quotes, as in `'"-pruned-"'`.

### Example 2: Silent Pruning

If you want the pruned properties to just be ommited, pass `undefined` as `prunedString`:

	var obj = {a:3};
	obj.self = obj;
	var json = JSON.prune(obj);
	console.log(json); // logs {"a":3,"self":"-pruned-"}
	json = JSON.prune(obj, {prunedString: undefined });
	console.log(json); // logs {"a":3}

Note: You get the same behavior with

	json = JSON.prune(obj, {replacer: function(){}});

### Example 3: Verbose Pruning

	var options = {replacer:function(value, defaultValue, circular){
		if (circular) return '"-circular-"';
		if (value === undefined) return '"-undefined-"';
		if (Array.isArray(value)) return '"-array('+value.length+')-"';
		return defaultValue;
	}};
	var json = JSON.prune(obj, options);

### Example 4: Function "serialization"

	var options = {replacer:function(value, defaultValue){
		if (typeof value === "function") return JSON.stringify(value.toString());
		return defaultValue;
	}};
	var json = JSON.prune(obj, options);

### Example 5: Array truncation mark

The default behavior on big arrays is to silently write only the first elements. It's possible with a `replacer` to add a string as last element:

	var obj = {arr: Array.apply(0,Array(100)).map(function(_,i){ return i+1 })}
	function replacer(value, defaultValue){
		if (Array.isArray(value)) return defaultValue.replace(/]$/, ',"-truncated-"]');
		return defaultValue;
	}
	var json = (asPrunedJSON(obj, {arrayMaxLength:5, replacer});

This produces

	{"arr":[1,2,3,4,5,"-truncated-"]}

License
-------

MIT
