<script>
  import { onMount, onDestroy } from 'svelte';

  let currentIndex = 0;

  export let slides = [];

  let interval;

  function next() {
    currentIndex = (currentIndex + 1) % slides.length;
  }

  function start() {
    interval = setInterval(next, 4000);
  }

  function stop() {
    clearInterval(interval);
  }

  onMount(start);
  onDestroy(stop);
</script>

<div class="carousel-container" on:mouseenter={stop} on:mouseleave={start}>
  <div class="carousel-inner" style="transform: translateX(-{currentIndex * 100}%);">

    {#each slides as slide}
      <div class="carousel-slide" style="background-image:url({slide.image})">
        <h2>{slide.title}</h2>
      </div>
    {/each}

  </div>
</div>