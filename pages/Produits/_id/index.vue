<template>
  <div>
    <div
      v-if="!(Object.keys(caracteristiques).length > 0)"
      class="container alerter"
    >
      <h3>
        <b-alert variant="warning" show
          >Aucune vente trouvée pour ce produit</b-alert
        >
      </h3>
    </div>
    <div
      v-if="Object.keys(caracteristiques).length > 0"
      class="container contenu"
    >
      <p>
        <b>LIBELLE:</b> {{ caracteristiques.libelle }} <br /><b
          >PRIX D'ACHAT:</b
        >
        {{ caracteristiques.prix_achat }}<br />
        <b>PRIX DE VENTE:</b> {{ caracteristiques.prix_vente }}
      </p>
      <chart-line v-if="show" :data="lineData" :options="options" />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: false
    }
  },
  async asyncData({ params, $axios }) {
    const { ventes } = await $axios.$get(`/api/ventes/produit/${params.id}`)
    let caracteristiques = {}
    if (ventes.length > 0) {
      caracteristiques = {
        libelle: ventes[0].produit_linked.libelle,
        prix_achat: ventes[0].prix_achat,
        prix_vente: ventes[0].prix_vente
      }
    }
    const labels = ventes.map((vente) => {
      return vente.code
    })
    const quantite = ventes.map((vente) => {
      return vente.quantite
    })
    const lineData = {
      datasets: [
        { label: 'Quantite', data: quantite, borderColor: 'red', fill: false }
      ]
    }
    const options = {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        xAxes: [
          {
            type: 'category',
            labels
          }
        ]
      }
    }
    return { caracteristiques, lineData, options }
  },
  mounted() {
    this.show = true
  }
}
</script>

<style lang="css" scoped>
.alerter {
  margin-top: 70px;
  margin-bottom: 25px;
}
.contenu {
  margin-top: 25px;
  margin-bottom: 25px;
}
</style>
