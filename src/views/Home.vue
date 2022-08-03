<template>
  <div class="home">
    <ProdutosLista />
  </div>
</template>

<script>
// @ is an alias to /src
import ProdutosLista from '@/components/ProdutosLista';
export default {
  name: 'HomeView',
  components: {
    ProdutosLista
  },
  created() {
    document.title = "M2";
  }
}
</script>
