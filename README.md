JSON.prune
==========

JSON.prune is a pruning JSON.stringify for the very specific cases where you need to stringify big or recursive javascript objects and don't really need the result to be complete.

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

Include it
----------

	<script src=http://dystroy.org/JSON.prune/.js></script>

License
-------

Public Domain. Use as you wish and at your own risk.
