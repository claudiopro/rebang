# rebang

Like React, top down composition of shell commands as reusable components

## Rationale

_«Once you hold a hammer, everything looks like a nail.»_ This is a proof of concept to represent hierarchies of shell commands as reusable components, and compose them using JSX doubles.

A component maps to a shell command, attributes map to flags, and content maps to stdin. When rendered, the inner components' output is piped to the outer component's stdin.

## Examples

```sh
echo 'Hello Rebang!' | wc -w
       2
```

```jsx
const RebangShell = require('rebang-shell'),
	Wc = require('rebang-commands/wc'),
	Echo = require('rebang-commands/echo');

RebangShell.execute(
	<Wc w={true}>
		<Echo>Hello Rebang!</Echo>
	</Wc>
);
```

## Components

Components must, at a minimum, define an `execute` method. _Note the API is not final yet_.

For instance, `rebang-commands/wc` could be implemented as:

```jsx
const Rebang = require('rebang'),
	RebangShell = require('rebang-shell');

const Wc = Rebang.createCommand({
	execute: function() {
		let args = [];
		if (this.props.w) {
			args.push('-w');
		}
		// Rebang takes care of piping stdin and stdout
		return RebangShell.execute(`wc ${args.join(' ')}`);
	}
});

module.exports = Wc;
```

## Lifecycle

Similarly to React, Rebang components have a lifecycle. Rebang invokes the `execute` method on each component. By default, components are re-executed every time their state or props change. The implementor has a chance to re-execute anyway or bail early by overriding the `shouldComponentExecute` method.

## License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2017 Claudio Procida
