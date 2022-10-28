# Yup's resolve algorithm

There are 4 classes in Yup:
- `Ref` : stores a path using which it will extract a value in an object
- `Lazy` : stores a builder function using which it will build a new schema
- `Condition` : stores again a builder function furnished using the `when` method and calculates a new schema with it
- `Schema` : A schema
	- Schema have a `when` method and store the input in a  `conditions` attribute, which is a `Condition` array

When we input a value, we have to calculate Lazy and Condition, this is what resolve does.

```
sch.resolve
	// Base case
	if sch is Ref
		return this
	if sch is Schema and sch.conditions.length == 0
		return this.clone
	// Recursive case
	if sch is Lazy
		return LazyBuilder(...).resolve
	if sch is Condition
		return WhenBuilder(...).resolve
	if sch is Schema and sch.conditions.length !== 0
		return this.conditions.reduce((s, condition) => condition.resolve(s, { value }), sch).resolve
```

Here's where everything is happening:
- [Lazy.ts l.38](https://github.com/jquense/yup/blob/pre-v1/src/Lazy.ts#L38)
- [Condition.ts l.66](https://github.com/jquense/yup/blob/pre-v1/src/Condition.ts#L66)
- [Reference.ts l.76](https://github.com/jquense/yup/blob/pre-v1/src/Reference.ts#L76)
- [Schema.ts l.276](https://github.com/jquense/yup/blob/pre-v1/src/schema.ts#L276)
