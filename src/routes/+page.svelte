<script lang="ts">
	let input = `add(add(add(if(equals(outputs('ABC'),null),0,outputs('ABC')),if(equals(outputs('DEF'),null),0,outputs('DEF'))),outputs('GHI')),outputs('JKL'))`

	let output: string

	const indentString = '\t'

	function format(input: string): string {
		let indentLevel = 0
		const indent = () => indentString.repeat(Math.max(indentLevel, 0))
		let sameLine = false

		let debugLines: any[] = []
		const result = input.replaceAll(/[\(\)],?|,/g, (match, offset) => {
			let replacement = match
			if (match.startsWith('(')) {
				// find the next closing bracket
				const nextOffset = input.indexOf(')', offset)
				// if the distance between brackets is small enough, stay on one line
				const distance = nextOffset - offset
				if (distance <= 8) {
					sameLine = true
					replacement = match
				} else {
					indentLevel++
					replacement = match + '\n' + indent()
				}
			} else if (match.startsWith(')')) {
				const optionalBreak = () => (match.includes(',') ? '\n' + indent() : '')

				if (sameLine) {
					sameLine = false
					replacement = match + optionalBreak()
				} else {
					indentLevel--
					replacement = '\n' + indent() + match + optionalBreak()
				}
			} else if (match === ',') {
				replacement = match + '\n' + indent()
			}
			debugLines.push({
				offset,
				match,
				context: input.slice(offset - 5, offset + 5),
				replacement,
				indentLevel,
				sameLine,
			})
			return replacement
		})
		console.table(debugLines)
		return result
	}
	$: output = format(input)

	let copied = false
	async function copyOutput() {
		await navigator.clipboard.writeText(output)
		copied = true
		setTimeout(() => (copied = false), 2000)
	}
</script>

<main>
	<h1>Bracket Balancer</h1>

	<label class="input">
		<span>Input</span>
		<textarea bind:value={input}></textarea>
	</label>

	<label class="output">
		<span>Output</span>
		<div>
			<button on:click={copyOutput}>{copied ? '✓' : 'Copy'}</button>
			<textarea readonly bind:value={output}></textarea>
		</div>
	</label>
</main>

<style>
	textarea {
		height: 200px;
		font-family: monospace;
		white-space: pre-wrap;
		resize: vertical;
	}

	.output {
		& div {
			position: relative;
		}

		& button {
			display: inline;
			width: auto;
			position: absolute;
			top: var(--form-element-spacing-vertical);
			right: var(--form-element-spacing-horizontal);
		}

		& textarea {
			height: 800px;
		}
	}
</style>
