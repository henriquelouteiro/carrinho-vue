<template>
    <div v-if="produtos && produtos.length" class="container">
      <div  v-for="(produto, index) in produtos" :key="index">
        <h2 class="titulo">{{ produto.nome }}</h2>
        <p class="preco">{{ produto.preco }}</p>
        
      </div>
    </div>
</template>

<script>
export default {
  name: "ProdutosLista",
  data() {
    return {
      produtos: null,
    };
  },
  methods: {
    getProdutos() {
      fetch("https://my-json-server.typicode.com/henriquelouteiro/api-fake/produtos")
        .then((r) => r.json())
        .then((r) => {
          this.produtos = r;
          console.log(this.produtos);
        });
    },
  },
  watch: {
    url() {
      this.getProdutos();
    },
  },
  created() {
    this.getProdutos();
  },
};
</script>
