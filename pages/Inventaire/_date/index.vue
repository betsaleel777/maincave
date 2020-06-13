<template>
  <b-container>
    <!-- tableau de la liste des produits -->
    <div id="toPrint">
      <h3>Inventaire du {{ date }}</h3>
      <b-table
        :fields="fields"
        :items="items"
        hover
        responsive
        show-empty
        bordered
      ></b-table>
    </div>
    <!-- bouton d'impression -->
    <center>
      <b-button @click="printSection" variant="primary">Imprimer</b-button>
    </center>
  </b-container>
</template>

<script>
export default {
  name: 'Inventaire',
  data() {
    return {
      fields: [
        { key: 'libelle', label: 'Produit' },
        { key: 'moy_achat', label: 'Marge achat (moyenne)' },
        { key: 'moy_vente', label: 'Marge vente (moyenne)' },
        { key: 'quantite_achetee', label: 'Quantité achetée' },
        { key: 'quantite_vendue', label: 'Quantité vendue' },
        { key: 'montant_achat', label: "Montant d'achat" },
        { key: 'montant_vente', label: 'Montant de vente' },
        { key: 'marge', label: 'Marge' }
      ]
    }
  },
  async asyncData({ $axios, params }) {
    const { inventaires, date } = await $axios.$get(
      `/api/dashboard/inventaire/old/${params.date}`
    )
    const items = inventaires.map((produit) => {
      const margeValue =
        produit.moy_vente * produit.quantite_vendue -
        produit.moy_achat * produit.quantite_achetee
      return {
        libelle: produit.produit_linked.libelle,
        moy_achat: produit.moy_achat,
        moy_vente: produit.moy_vente,
        quantite_achetee: produit.quantite_achetee,
        quantite_vendue: produit.quantite_vendue,
        montant_achat: produit.moy_achat * produit.quantite_achetee,
        montant_vente: produit.moy_vente * produit.quantite_vendue,
        marge: margeValue < 0 ? 0 : margeValue
      }
    })
    return { items, date }
  },
  methods: {
    printSection() {
      this.$htmlToPaper('toPrint')
    }
  }
}
</script>

<style scoped>
table {
  font-size: 80%;
}
table,
th,
td {
  border-collapse: collapse;
  border: 1px solid black;
  width: 100%;
  text-align: left;
}
</style>
