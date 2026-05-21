<template>
  <div class="box-ricerca">
    <input
      v-model="testo"
      @keyup.enter="cerca"
      class="input-ricerca"
      placeholder="cerca serie..."
    />

    <button @click="cerca" class="bottone-ricerca">cerca</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      testo: "",
    };
  },
  //* emit serve per dire al componente padre che è stato creato un evento in questo caso l'evento cerca con un dato che sarà this.testo
  methods: {
    cerca() {
      this.$emit("cerca", this.testo);
    },
  },
};
</script>

<style scoped>
.box-ricerca {
  text-align: center;

  margin-bottom: 30px;
}

.input-ricerca {
  width: 50%;

  padding: 10px;

  border-radius: 10px;

  border: none;

  margin-right: 10px;
}

.bottone-ricerca {
  background-color: rgb(255, 0, 0);

  color: rgb(255, 255, 255);

  border: none;

  padding: 10px 15px;

  border-radius: 10px;
}
</style>
