<template>
  <div class="pagina">
    <h1 class="titolo">SERIE TV</h1>

    <BarraRicerca @cerca="cercaSerie" />

    <div class="top-bar">
      <button v-if="ricercaAttiva" class="bottone-home" @click="tornaHome">
        torna home
      </button>
    </div>

    <div v-if="!ricercaAttiva">
      <h2 class="titolo-rosso">TOP 10 PIÙ VISTE:</h2>
      <div v-if="ricercaAttiva && serieTrovate.length === 0">
        Nessun risultato trovato
      </div>
      <div class="riga">
        <div class="contenitore-serie" v-for="serie in top10" :key="serie.id">
          <CardSerie :serie="serie" />
        </div>
      </div>

      <h2 class="titolo-blu">ULTIME USCITE:</h2>

      <div class="riga">
        <div
          class="contenitore-serie"
          v-for="serie in nuoveUscite"
          :key="serie.id"
        >
          <CardSerie :serie="serie" />
        </div>
      </div>
    </div>
    <div v-else>
      <h2 class="titolo-ricerca">RISULTATI RICERCA:</h2>

      <div class="riga">
        <div
          class="contenitore-serie"
          v-for="serie in serieTrovate"
          :key="serie.id"
        >
          <CardSerie :serie="serie" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BarraRicerca from "@/components/BarraRicerca.vue";
import CardSerie from "@/components/CardSerie.vue";

export default {
  components: {
    BarraRicerca,
    CardSerie,
  },
  data() {
    return {
      top10: [],
      nuoveUscite: [],
      serieTrovate: [],
      ricercaAttiva: false,
    };
  },

  //* per il metodo per tradurre ho cercato online come fare e alla fine ho scelto di usare una api di ggogle usata anche per google traslate
  methods: {
    async traduci(testo) {
      if (!testo) return "nessuna descrizione";
      testo = testo.replace(/<[^>]*>/g, "");
      try {
        const res = await fetch(
          "https://translate.googleapis.com/translate_a/single?client=gtx&sl=en&tl=it&dt=t&q=" +
            encodeURIComponent(testo)
        );
        const dati = await res.json();
        return dati[0][0][0];
      } catch {
        return testo;
      }
    },

    async prendiSerie(nome) {
      const res = await fetch(`https://api.tvmaze.com/search/shows?q=${nome}`);
      const dati = await res.json();
      if (dati.length > 0) {
        const serie = dati[0].show;
        serie.tradotto = await this.traduci(serie.summary);
        return serie;
      }
      return null;
    },

    //*qui sotto ho scelto 10 serie tv piu viste e 10 serie tv uscite da poco per metterle automaticamente sempre nella home
    async caricaDati() {
      const top = [
        "La casa di carta",
        "Breaking Bad",
        "Dexter",
        "Game of Thrones",
        "Friends",
        "Dark",
        "Stranger Things",
        "The Walking Dead",
        "Prison Break",
        "Peaky Blinders",
      ];
      const nuove = [
        "The Last of Us",
        "Wednesday",
        "Fallout",
        "One Piece",
        "The Bear",
        "House of the Dragon",
        "Shogun",
        "Baby Reindeer",
        "3 Body Problem",
        "Avatar",
      ];
      for (let nome of top) {
        const s = await this.prendiSerie(nome);
        if (s) this.top10.push(s);
      }

      for (let nome of nuove) {
        const s = await this.prendiSerie(nome);

        if (s) this.nuoveUscite.push(s);
      }
    },

    async cercaSerie(testo) {
      console.log("RICERCA:", testo);

      if (!testo || testo.trim() === "") return;

      this.serieTrovate = [];
      this.ricercaAttiva = true;

      const res = await fetch(`https://api.tvmaze.com/search/shows?q=${testo}`);

      const dati = await res.json();

      for (let e of dati) {
        if (e.show) {
          let serie = e.show;

          serie.tradotto = await this.traduci(serie.summary);

          this.serieTrovate.push(serie);
        }
      }
    },
    //* qui sopra aspetta che le descrizioni delle serie vengano tradtte prima di inserirle con il push
    tornaHome() {
      this.ricercaAttiva = false;

      this.serieTrovate = [];
    },
  },
  //* mounted serve per caricare le 10 serie top e le 10 nuove uscite nella hom per far si che la pagina non sia vuota
  mounted() {
    this.caricaDati();
  },
};
</script>
<style scoped>
.pagina {
  background-color: rgb(20, 20, 20);

  min-height: 100vh;

  color: rgb(255, 0, 0);

  padding: 20px;
}

.titolo {
  font-size: 70px;
  text-align: center;
}

.titolo-rosso {
  font-size: 25px;
  text-align: center;
  color: rgb(255, 255, 255);
}

.titolo-blu {
  font-size: 25px;
  color: rgb(255, 255, 255);
  text-align: center;
}

.titolo-ricerca {
  font-size: 25px;
  color: rgb(255, 255, 255);
  text-align: center;
}

.riga {
  display: flex;

  flex-wrap: wrap;
}

.contenitore-serie {
  width: 16.66%;

  padding: 10px;
}

@media (max-width: 991px) {
  .contenitore-serie {
    width: 33.33%;
  }
}

@media (max-width: 767px) {
  .contenitore-serie {
    width: 50%;
  }
}

.top-bar {
  display: flex;

  justify-content: flex-end;

  margin-bottom: 10px;
}

.bottone-home {
  padding: 10px 15px;

  border: none;

  border-radius: 10px;

  background-color: white;

  color: black;

  font-weight: bold;

  cursor: pointer;
}

.bottone-home:hover {
  background-color: lightgray;
}
</style>
