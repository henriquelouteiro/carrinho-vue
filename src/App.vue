<template>
  <div>
    <NavBar_ />
    <router-view/>
    </div>
</template>

<script>
// @ is an alias to /src
import NavBar_ from '@/components/NavBar_';

export default {
  name: 'app',
  components: {
    NavBar_
  }
}
</script>


<style>
body, h1, h2, ul, li, p {
  padding: 0px;
  margin: 0px;
}

ul {
  list-style: none;
}

* {
  box-sizing: border-box;
}

body {
  background: #b57bf8;
  font-family: "Noto Serif";
}

#app {
  padding: 0 80px;
}

.container { 
  max-width: 900px;
  padding: 20px 0;
  margin: 0 auto;
}
</style>
