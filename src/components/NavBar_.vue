<template>
    <header class="header">
        <div class="container navbar">
            <img class="logo" src="../assets/techno.svg" alt="Techno">
            <div class="carrinho_menu">Carrinho</div>
        </div>
    </header>
</template>

<script>
export default {
  name: "NavBar_",
};
</script>

<style scoped>
.header{
    background: #7700ff;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.navbar{
    display: flex;
    justify-content: space-between;
}

.logo {
  width: 80px;
}

.carrinho_menu::after {
  content: "";
  display: inline-block;
  background: url("../assets/carrinho.svg") no-repeat center center;
  width: 25px;
  height: 25px;
  margin-left: 10px;
}

.carrinho_menu {
  display: flex;
  align-items: center;
  cursor: pointer;
  font-weight: bold ;
  color: white;
}
</style>