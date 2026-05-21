<template>
  <div class="card-serie">
    <img v-if="serie.image" :src="serie.image.medium" class="immagine-serie" />
    <div class="contenuto-card">
      <h3>
        {{ serie.name }}
      </h3>

      <p>⭐ {{ serie.rating.average || "n.v." }}</p>

      <p>
        {{ serie.tradotto }}
      </p>
    </div>
  </div>
</template>

<script>
//* le doppie graffe sopra {{}} servono per inserire dati javascript dentro l’html
//*  props significa che riceve dal componente padre un oggetto chiamato serie
export default {
  props: ["serie"],
};
</script>

<style scoped>
.card-serie {
  background-color: rgb(70, 70, 70);

  color: white;

  border-radius: 10px;

  overflow: hidden;

  height: 100%;
}

.immagine-serie {
  width: 100%;

  height: 300px;

  object-fit: cover;
}

.contenuto-card {
  padding: 10px;
}

h3 {
  font-size: 18px;
}
</style>
