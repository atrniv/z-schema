z-schema validator
==================

JSON Schema validator for Node.js (draft4 version)

Coded according to:

[json-schema documentation](http://json-schema.org/documentation.html),
[json-schema-core](http://json-schema.org/latest/json-schema-core.html),
[json-schema-validation](http://json-schema.org/latest/json-schema-validation.html),
[json-schema-hypermedia](http://json-schema.org/latest/json-schema-hypermedia.html)

Passing all tests here (even optional, except zeroTerminatedFloats):

[json-schema/JSON-Schema-Test-Suite](https://github.com/json-schema/JSON-Schema-Test-Suite)

Will try to maintain this as much as possible, all bug reports welcome.

Basic Usage
-----------

```javascript
var report = zSchema.validate(json, schema, function(report) {
    if (report.valid === true) ...
});
```

If ```report.valid === false```, then errors can be found in ```report.errors```.

The report object will look something like:

```json
{
    "valid": false,
    "errors": [
    
    ]
}
```

Advanced (Server) Usage
-----------------------

You can pre-compile schemas (for example on your server startup) so your application is not
bothered by schema compilation and validation when validating ingoing / outgoing objects.

```javascript
var validator = new zSchema();
validator.compileSchema(schema, function (err, compiledSchema) {
    assert.isUndefined(err);
    ...
});
```

Then you can re-use compiled schemas easily with sync-async validation API.

```javascript
var report = validator.validateWithCompiled(json, compiledSchema);
assert.isTrue(report.valid);
...
```

```javascript
validator.validateWithCompiled(json, compiledSchema, function(err, success, report) {
    assert.isTrue(success);
    ...
});
```

Note:

Most basic schemas don't have to be compiled for validation to work (although recommended).
Async compilation was mostly created to work with schemas that contain references to other files.

Customization
-------------

You can add validation for your own custom string formats like this:
(these are added to all validator instances, because it would never make sense to have multiple 
functions to validate format with the same name)

```javascript
zSchema.registerFormat('xstring', function (str) {
    return str === 'xxx';
});
zSchema.validate('xxx', {
    'type': 'string',
    'format': 'xstring'
}, function (report) {
    // report.valid will be true
}
```

When creating new instance of validator, you can specify some options that will alter the validator behaviour.

* strict: ```true/false```

if strict mode is turned on, you are not able to leave properties "addtionalItems" and "additionalProperties" to default

```javascript
var validator = new zSchema({
    strict: true
});
```