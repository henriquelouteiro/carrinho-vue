<template>
  <div>
    
    <router-view/>
    </div>
</template>

<script>

export default {
  name: 'app',
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
