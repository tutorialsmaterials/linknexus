<script lang="ts">
    import { page } from '$app/stores'
    export let measurement_id: string;
    $: {
      if (typeof gtag !== 'undefined') {
        gtag('config', measurement_id, {
          page_title: document.title,
          page_path: $page.url.pathname,
        })
      }
    }
  </script>
  
  <svelte:head>
    <script
      async
      src={"https://www.googletagmanager.com/gtag/js?id=" + measurement_id}>
    </script>
    <script>
      window.dataLayer = window.dataLayer || []
  
      function gtag() {
        dataLayer.push(arguments)
      }
  
      gtag('js', new Date())
      gtag('config', measurement_id)
    </script>
  </svelte:head>
  