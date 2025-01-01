<script>
  import { onMount } from "svelte";

  // Array cu ID-urile video-urilor
  const videoIds = [
    "1043181960",
    "1043181960",
    "1043179505",
    "1043179505",
    "1043179505",
    // Adaugă alte ID-uri aici
  ];

  let players = new Map(); // Map pentru a asocia fiecare iframe cu player-ul său

  onMount(() => {
    // Load Vimeo Player API script
    const script = document.createElement("script");
    script.src = "https://player.vimeo.com/api/player.js";
    script.async = true;
    script.onload = setupVideoObservers;
    document.body.appendChild(script);
  });

  function setupVideoObservers() {
    const iframes = document.querySelectorAll("iframe[data-vimeo]");
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const iframe = entry.target;
          initializePlayer(iframe);
          observer.unobserve(iframe); // Oprire observație după inițializare
        }
      });
    });

    iframes.forEach((iframe) => observer.observe(iframe));
  }

  function initializePlayer(iframe) {
    const player = new Vimeo.Player(iframe);
    const videoId = iframe.dataset.id; // ID-ul video-ului din atributul iframe

    // Salvează player-ul în Map cu ID-ul video-ului ca cheie
    players.set(videoId, player);

    // Adaugă evenimente de hover
    iframe.addEventListener("mouseenter", () => {
      player.play();
    });

    iframe.addEventListener("mouseleave", () => {
      player.pause();
    });
  }

  // Funcție pentru a controla un video specific
  function playVideoById(videoId) {
    const player = players.get(videoId);
    if (player) {
      player.play();
    } else {
      console.error(`Player for video ID ${videoId} not found.`);
    }
  }
</script>

<style>
  .video-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr); /* 4 coloane egale */
  }
  .video-container {
    position: relative;
    padding-top: 56.25%; /* Aspect ratio 16:9 */
  }
  iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: 0;
  }

  @media (max-width: 1024px) {
    .video-grid {
      grid-template-columns: repeat(2, 1fr); /* 2 coloane pentru ecrane medii */
    }
  }

  @media (max-width: 768px) {
    .video-grid {
      grid-template-columns: 1fr; /* 1 coloană pentru ecrane mici */
    }
  }
</style>

<div class="video-grid">
  <!-- Generarea dinamică a iframe-urilor din array -->
  {#each videoIds as id, i}
    <div class="video-container">
      <iframe
        data-vimeo
        data-id={id}
        src={`https://player.vimeo.com/video/${id}?badge=0&autopause=0&player_id=${i}&app_id=58479&background=1&autoplay=0`}
        frameborder="0"
        allow="autoplay; fullscreen; picture-in-picture; clipboard-write"
        style="position:absolute;top:0;left:0;width:100%;height:100%;"
        title={`Video ${i + 1}`}
      ></iframe>
    </div>
  {/each}
</div>

