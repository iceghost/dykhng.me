<script>
  let count = 0;

  function add() {
    count += 1;
  }

  function subtract() {
    count -= 1;
  }
</script>

<div class="flex space-x-2 items-center">
  <button class="border p-1" on:click={subtract}>-</button>
  <pre>{ count }</pre>
  <button class="border p-1" on:click={add}>+</button>
</div>
<div class="message">
  <slot />
</div>
