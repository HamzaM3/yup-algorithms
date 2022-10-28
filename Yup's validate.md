# Yup's validate algorithm

`validate` is an async function so it makes sense (as far as I understand) that it uses callbacks to work.

A callback will be of the following type:
`(err: Error, val: any) => void`

Here are all the callbacks involved:
- `resRej` : `(err, val) => {if(err) reject(err) else resolve(val)`
  - [schema.ts, line 432](https://github.com/jquense/yup/blob/pre-v1/src/schema.ts#L432)
- `finishTestRun` : Used in `runTest` to handle the termination of all tests
  - [utils/runTests.ts, line 43](https://github.com/jquense/yup/blob/pre-v1/src/util/runTests.ts#L43)
- `treatFields` : Tests the fields of an object or an array after it was validated itself using `runTests` on all their validate functions
	- [object.ts, line 226](https://github.com/jquense/yup/blob/pre-v1/src/object.ts#L226)
	- [array.ts, line 108](https://github.com/jquense/yup/blob/pre-v1/src/array.ts#L108)

```
Schema.validate(val, opts, callback):
	schema = schema.resolve //calculates result of when and lazy
	// cast (simple)
	if not strict:
		call _cast //overriden for object and array
	// test
	if the call was not done with super:
		if the schema is unnested:
			callback = resRej
		else
			callback = finishTestRun of the runTest of the parent
	else
		callback = treatFields of current schema
	
	if the call was not done with super and is iterable:
		tests = [validate of all fields]
	else:
		tests = tests of current object
		
	runTests(tests, callback)
```

This is simplified and I remove the noise about options and so on.

