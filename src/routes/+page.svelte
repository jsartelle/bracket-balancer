<script lang="ts">
  let input = `add(add(add(if(equals(outputs('ABC'),null),0,outputs('ABC')),if(equals(outputs('DEF'),null),0,outputs('DEF'))),outputs('GHI')),outputs('JKL'))`

  let output: string

  const indentString = '\t'

  function format(input: string): string {
    let indentLevel = 0
    const indent = () => indentString.repeat(Math.max(indentLevel, 0))
    return input.replaceAll(/[\(\)],?|,/g, (match) => {
      if (match.startsWith('(')) {
        indentLevel++
        return match + '\n' + indent()
      } else if (match.startsWith(')')) {
        indentLevel--
        return '\n' + indent() + match + '\n' + indent()
      } else if (match === ',') {
        return match + '\n' + indent()
      } else {
        return match
      }
    })
  }
  $: output = format(input)

  let copied = false
  async function copyOutput() {
    await navigator.clipboard.writeText(output)
    copied = true
    setTimeout(() => (copied = false), 3000)
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
      height: 500px;
    }
  }
</style>
