JSON.prune
==========

A pruning JSON.stringify for the very specific cases where you need to be able to stringify very big or recursive javascript objects and don't really need the result to be complete.

    var json = JSON.stringify(window); // fails
    var json = JSON.prune(window); // builds a JSON valid string from a pruned version of the
                                   // recursive, deep, and not totally accessible window object
    var prunedWindow = JSON.parse(JSON.prune(window)); // builds a lighter acyclic version of window

It's totally useless for at least 99% of js developpers.

Project/Test page
-----------------

[dystroy.org/JSON.prune](http://dystroy.org/JSON.prune)

Include it
----------

	<script src=http://dystroy.org/JSON.prune/.js></script>
