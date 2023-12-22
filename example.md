# Input

```
add(add(add(if(equals(outputs('ABC'),null),0,outputs('ABC')),if(equals(outputs('DEF'),null),0,outputs('DEF'))),outputs('GHI')),outputs('JKL'))
```

# Output

```
add(
	add(
		add(
			if(
				equals(
					outputs('ABC'),
					null
				),
				0,
				outputs('ABC')
			),
			if(
				equals(
					outputs('DEF'),
					null
				),
				0,
				outputs('DEF')
			)
		),
		outputs('GHI')
	),
	outputs('JKL')
)
```