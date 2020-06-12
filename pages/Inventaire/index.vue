<template>
  <b-container>
    <!-- tableau de la liste des produits -->
    <div id="toPrint">
      <h3>Inventaire</h3>
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
        { key: 'achat_unitaire', label: 'Quantité achetée' },
        { key: 'vente_unitaire', label: 'Quantité vendue' },
        { key: 'montant_achat', label: "Montant d'achat" },
        { key: 'montant_vente', label: 'Montant de vente' },
        { key: 'marge', label: 'Marge' }
      ]
    }
  },
  async asyncData({ $axios }) {
    const { inventaires } = await $axios.$get('/api/dashboard/inventaire')
    const items = inventaires.map((produit) => {
      const margeValue =
        produit.moy_vente * produit.vente_unitaire -
        produit.moy_achat * produit.achat_unitaire
      return {
        libelle: produit.libelle,
        moy_achat: produit.moy_achat,
        moy_vente: produit.moy_vente,
        achat_unitaire: produit.achat_unitaire,
        vente_unitaire: produit.vente_unitaire,
        montant_achat: produit.moy_achat * produit.achat_unitaire,
        montant_vente: produit.moy_vente * produit.vente_unitaire,
        marge: margeValue < 0 ? 0 : margeValue
      }
    })
    return { items }
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
