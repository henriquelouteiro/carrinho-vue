<template>
  <div>
    <NavBarVivo />
    <div class="container conteudo mb-4">
      
      <div class="d-flex justify-content-center align-items-center produto_header mb-2 ">
        <h3>
          {{ total | numeroPreco }} | {{ carrinho.length }} 
        </h3>   
      </div>

      <section class="d-flex justify-content-center flex-wrap produtos-box border-sections">
        <div class="card" v-for="(item, index) in produtos" :key="item.id">
          <div class="card-body d-flex justify-content-between flex-column">
            <strong class="text-uppercase produto_titulo ">{{ item.nome }}</strong>
            <p class="card-text mb-2">
              {{ item.preco | numeroPreco }}
            </p>
            <button
              v-if="item.quantidade > 0"
              class="modal_btn "
              @click="adicionarCarrinho(index)"
            >
              Adicionar Item
            </button>
            <button v-else class="modal_btn esgotado" disabled>
              Produto Esgotado
            </button>
          </div>
        </div>
      </section>

      <section class="modal" v-if="produto" @click="fecharModal">
        <div class="modal_container">
          <div class="modal_img"></div>
          <img class="foto" :src="produto.foto" />
          <div class="modal_dados">
            <button @click="produto = false" class="modal_fechar">X</button>
            <span class="modal_preco">{{ produto.preco | numeroPreco }}</span>
            <h2 class="modal_titulo">{{ produto.nome }}</h2>
            <p>{{ produto.descricao }}</p>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import NavBarVivo from "@/components/NavBarVivo.vue";

export default {
  name: "HomeView",
  components: {
    NavBarVivo,
  },
  data() {
    return {
      produtos: [],
      produto: false,
      carrinho: [],
      carrinhoAtivo: false,
      mensagemAlerta: "Item adicionado",
      alertaAtivo: false,
    };
  },
  filters: {
    numeroPreco(valor) {
      return valor.toLocaleString("pt-BR", {
        style: "currency",
        currency: "BRL",
      });
    },
  },
  watch: {
    remove(index) {
      this.carrinho.splice(index, 1);
    },
    url() {
      this.getProdutos();
    },
    carrinho() {
      window.localStorage.carrinho = JSON.stringify(this.carrinho);
    },
  },
  created() {
    document.title = "VIVO [M2]";
    this.getProdutos();
    this.checarLocalStorage();
  },
  computed: {
    total() {
      return this.carrinho.reduce((total, produto) => {
        return total + produto.preco;
      }, 0);
    },
  },
  methods: {
    checarLocalStorage() {
      if (window.localStorage.carrinho)
        this.carrinho = JSON.parse(window.localStorage.carrinho);
    },
    fetchProdutos() {
      fetch("https://api.figures3d.com.br/api/produtos")
        .then((r) => r.json())
        .then((r) => {
          this.produtos = r;
          console.log(this.produtos);
        });
    },
    fetchProduto(id) {
      fetch(`https://api.figures3d.com.br/api/produtos/${id}`)
        .then((r) => r.json())
        .then((r) => {
          this.produto = r;
        });
    },
    abrirModal(id) {
      this.fetchProduto(id);
    },
    fecharModal({ target, currentTarget }) {
      if (target === currentTarget) this.produto = false;
    },
    clickForaCarrinho({ target, currentTarget }) {
      if (target === currentTarget) this.carrinhoAtivo = false;
    },
    removerItem(index) {
      //remover item carrinho splice
      this.carrinho.splice(index, 1);
    },
    adicionarCarrinho(index) {
      console.log(index);
      if (this.verificarCategoriaExistente(this.produtos[index].categoria))
        return;

      this.carrinho.push({
        nome: this.produtos[index].nome,
        preco: this.produtos[index].preco,
        categoria: this.produtos[index].categoria,
      });
    },
    verificarCategoriaExistente(categoria) {
      let aux = false;
      this.carrinho.forEach((item) => {
        if (item.categoria === categoria) {
          aux = true;
        }
      });
      return aux;
    },
    getProdutos() {
      //fetch("https://my-json-server.typicode.com/henriquelouteiro/api-fake/produtos")
      fetch("https://api.figures3d.com.br/api/produtos")
        .then((r) => r.json())
        .then((r) => {
          this.produtos = r;
          console.log(this.produtos);
        });
    },
  },
};
</script>

<style>
.conteudo {
  background: #ebebeb;
  padding: 10px 5px;
  border-radius: 20px;
}

/* LISTA PRODUTOS */
.produto_titulo {
  font-size: 1.5rem;
  line-height: 1;
}
.produto_header{
  border: 1px solid rgb(92, 0, 100);
  padding: 10px;
  border-radius: 20px;
  background: #cb9dff;
}
.produtos-box {
  padding: 10px 0;
  gap: 10px;
}

.border-sections{
  border-top: 3px solid rgb(44, 44, 44)
}

.produto {
  display: flex;
  align-items: center;
  margin-bottom: 40px;
  background: #ffffff;
  box-shadow: 0 0 2rem rgba(0, 0, 0, 0.1);
  cursor: pointer;
}

.card {
  width: 200px;
  padding: 0;
  
  box-shadow: inset 0 15rem 30rem rgb(243, 218, 239);
}

/* MODAL*/
.modal_btn {
  background: rgb(50, 29, 58);
  cursor: pointer;
  color: #ffffff;
  border: none;
  font-size: 1rem;
  padding: 7px 20px;
  font-family: "Noto Serif";
  border-radius: 100px;
}

.modal_btn.esgotado {
  background: #2b2828;
}

.modal_btn:active {
  background: #808080;
}
</style>
