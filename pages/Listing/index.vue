<template>
  <b-container>
    <!-- tableau de la liste des produits -->
    <div id="toPrint">
      <h3>PRODUITS AVEC PRIX DE VENTE</h3>
      <b-table
        :fields="fields"
        :items="items"
        hover
        show-empty
        bordered
      ></b-table>
      <h3>PRODUITS AVEC PRIX D'ACHAT</h3>
      <b-table
        :fields="champs"
        :items="items"
        hover
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
  name: 'Listing',
  data() {
    return {
      fields: [
        { key: 'libelle', label: 'Libellé' },
        { key: 'prix_vente', label: 'Prix de Vente' }
      ],
      champs: [
        { key: 'libelle', label: 'Libellé' },
        { key: 'prix_achat', label: "Prix d'achat" }
      ],
      items: []
    }
  },
  async fetch() {
    const { produits } = await this.$axios.$get('/api/produits')
    this.items = produits.map((produit) => {
      return {
        id: produit.id,
        libelle: produit.libelle,
        prix_achat: produit.prix_achat,
        prix_vente: produit.prix_vente
      }
    })
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
