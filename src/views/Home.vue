<template>
  <div>
    <NavBarVivo />
    <div class="container conteudo mb-4">
      <section
        v-for="(categoria, index) in categorias"
        class="d-flex flex-column border-sections sec-produtos"
        :key="index"
      >
        <h3 class="text-color-purple">{{ categoria[0].categoria }}</h3>
       <p class="section-descricao" v-if="categoria[0].categoria == 'Internet'"  >Selecione um plano de internet para continuar</p>
       <p class="section-descricao" v-else-if="verificarCategoriaCarrinho('Internet')">Agora seleciona uma das opções abaixo</p>
        <div class="d-flex flex-wrap produtos-box">
          <div class="card" v-for="item in categoria" :key="item.id">
            <div class="card-body d-flex justify-content-between flex-column">
              <button
                v-if="
                  item.categoria == 'Internet' || verificarCategoriaCarrinho('Internet')
                "
                class="card_btn"
                @click="adicionarCarrinho(item.id)"
              >
                +
              </button>
              <button v-else class="card_btn disabled">+</button>
              <strong class="text-uppercase produto_titulo">{{
                item.nome
              }}</strong>

              <p class="card-text mb-2">
                {{ item.preco | numeroPreco }}
              </p>

              <span
                @click="showModal(item)"
                id="show-btn"
                class="d-flex justify-content-end card_detalhes"
                data-toggle="modal"
                data-target="#exampleModalLong"
                >+ detalhes</span
              >
            </div>
          </div>
        </div>
      </section>

      <section class="detalhe-compras sec-produtos">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">Produto</th>
              <th scope="col">Categoria</th>
              <th scope="col">Valor</th>
              <th class="d-flex justify-content-center" scope="col">Ação</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(produto, index) in carrinho" :key="index">
              <th scope="row">{{ produto.nome }}</th>
              <td>{{ produto.categoria }}</td>
              <td>{{ produto.preco | numeroPreco }}</td>
              <td align="center">
                <svg
                  @click="removerCarrinho(index)"
                  class="less-icon MuiSvgIcon-root"
                  focusable="false"
                  viewBox="0 0 24 24"
                  aria-hidden="true"
                >
                  <path
                    d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"
                  ></path>
                </svg>
              </td>
            </tr>
          </tbody>
        </table>

        <div class="d-flex justify-content-between align-items-center text-color-purple">
          <h3>Total</h3>
          <h3>{{ total | numeroPreco }}/mês</h3>
        </div>
      </section>

      <b-modal
        ref="my-modal"
        hide-footer
        :title="produto.categoria + ' | ' + produto.nome"
      >
        <div class="modal-body-conteudo">
          <p>{{ produto.detalhes }}</p>
          <button
            v-if="
              produto.categoria == 'Internet' || verificarCategoriaCarrinho('Internet')
            "
            class="card_btn modal_btn"
            @click="adicionarCarrinho(produto.id)"
          >
            Adicionar
          </button>
        </div>
      </b-modal>

      <div class="alerta" :class="{ativo: alertaAtivo}">
      <p class="alerta_mensagem">{{mensagemAlerta}}</p>
    </div>
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
      categorias: [],
      produtos: [],
      produto: false,
      carrinho: [],
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
    document.title = "VIVO - CLONE";
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
    isInternet(categoria){
      return categoria == 'Internet';
    },
    showModal(produto) {
      this.produto = produto;
      this.$refs["my-modal"].show();
    },
    removerCarrinho(index) {
      if(this.isInternet(this.carrinho[index].categoria)){
        this.carrinho = [];
        this.notificacao("Todos itens removidos");
        return
      }
      this.carrinho.splice(index, 1);
      this.notificacao("Item removido");
    },
    separarProdutos() {
      let produtos = {};
      this.produtos.forEach((produto) => {
        if (!produtos[produto.categoria]) {
          produtos[produto.categoria] = [];
        }
        produtos[produto.categoria].push(produto);
      });
      this.categorias = produtos;
    },
    checarLocalStorage() {
      if (window.localStorage.carrinho)
        this.carrinho = JSON.parse(window.localStorage.carrinho);
    },
    removerItem(index) {
      this.carrinho.splice(index, 1);
    },
    notificacao(msg) {
      this.mensagemAlerta = msg;
      this.alertaAtivo = true;
      setTimeout(() => {
        this.alertaAtivo = false;
      }, 2000);
    },
    adicionarCarrinho(index) {
      let idx = this.produtos.findIndex((produto) => produto.id === index);

      if (this.verificarCategoriaCarrinho(this.produtos[idx].categoria)){
        this.notificacao("Não é possível adicionar mais produtos desta categoria")
        return
      }

      this.carrinho.push({
        nome: this.produtos[idx].nome,
        preco: this.produtos[idx].preco,
        categoria: this.produtos[idx].categoria,
      });
       this.notificacao(`${this.produtos[idx].nome} adicionado ao carrinho.`)
    },
    verificarCategoriaCarrinho(categoria) {
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
      fetch(
        "https://my-json-server.typicode.com/henriquelouteiro/api-fake/produtos"
      )
        .then((r) => r.json())
        .then((r) => {
          this.produtos = r;
          console.log(this.produtos);
          this.separarProdutos();
        });
    },
  },
};
</script>

<style>

.conteudo {
  background: #f3f3f3;
  padding: 10px 5px;
  border-radius: 5px;
}

.text-color-purple{
  color: #79009e;
}

.section-descricao{
  color: rgb(90, 90, 90);
  font-weight: bold;
}
/* LISTA PRODUTOS */
.sec-produtos {
  padding: 10px;
}
.produto_titulo {
  font-size: 1.5rem;
  line-height: 1;
}

.produtos-box {
  padding: 5px 0;
  gap: 10px;
}

.border-sections {
  border-bottom: 3px solid rgb(44, 44, 44);
}

/* CARD PRODUTO */
.card {
  width: 205px;
  height: 180px;
  padding: 0;
  background: #ffffff;
  border: 1px solidd#B57BF8;
}

.card_detalhes{
  color: #79009e;
  cursor: pointer;
  width: max-content;
  align-self: flex-end;
}

.card_btn {
  width: 27px;
  font-weight: bold;
  font-size: 1.1rem;
  background: #79009e;
  opacity: 0.6;
  cursor: pointer;
  color: #ffffff;
  border: none;
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  font-family: "Noto Serif";
  border-radius: 50%;
  transition: 0.3s cubic-bezier(0.55, 0.025, 0.675, 0.96);
}

.card_btn:active {
  background: #808080;
}

.card_btn:hover {
  opacity: 1;
}
/* MODAL*/


.modal_btn{
  width: max-content;
  padding: 2px 10px;
  border-radius: 100px;
}

.modal-body-conteudo{
  display: flex;
  flex-direction: column;
  gap: 10px;

}
/* ALERTA */

.alerta {
  position: fixed;
  top: 10%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10;
  width: 100%;
  text-align: center;
  display: none;
}

.alerta.ativo {
  display: block;
  animation: fadeInDown .3s forwards;
}

.alerta_mensagem {
  background: #ffffff;
  display: inline-block;
  padding: 20px 40px;
  border: 2px solid black;
  box-shadow: 0px 3px 4px rgba(0,0,0,.1), 0px 4px 10px rgba(0,0,0,.2);
}

/* ICONS*/
.disabled {
  pointer-events: none;
  cursor: default;
  background: #808080;
}
.less-icon {
  width: 20px;
  height: 20px;
  cursor: pointer;
}

@media screen and (max-width: 680px) {
  .produtos-box {
    justify-content: center !important;
  }
}
</style>
