# Yup's cast algorithm

`cast` basically just applies the transform in sequence or returns the default

```
sch._cast(val, opt)
	let _val;
	if val is undefined
		_val = getDefault
	else
		_val = this.transforms.reduce((v, f) => f(v, val, opt), val)
	
	if _val is undefined
		return getDefault
	else if sch is not object or array:
		return _val
	
	if sch is object
		props = this._nodes // this has been sorted taking into account dependencies of when and ref
		intermediateValue = {}
		for prop in props
			field = this.fields[prop]
			inputValue = _val[prop]
			
			field = field.resolve({
				val: inputValue,
				parent: intermediateValue
			})
			
			// It's not "_cast"
			v = field.cast(inputValue, intermediateValue)

			intermediateValue[prop] = v
		return intermediateValue

	if sch is array
		return _val.map(v => innerType.cast(v))
```

I think it would be very uneasy to rewrite _cast
We should rather play with the options, and avoid entering inside these algorithms.
