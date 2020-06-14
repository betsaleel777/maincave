<template>
  <div class="container">
    <div class="row commandes">
      <div class="col-md-10"></div>
      <div class="col-md-2">
        <button @click="add" style="width:100%" class="btn btn-primary">
          ajouter
        </button>
      </div>
    </div>
    <div class="recherche">
      <label for="search">Recherche:</label>
      <input id="search" v-model="critere" type="text" placeholder="" />
    </div>
    <b-table
      id="tableau"
      :current-page="page"
      :items="items"
      :fields="fields"
      :per-page="parPage"
      :filter="critere"
      show-empty
      bordered
      outlined
      hover
      primary-key="id"
    >
      <template v-slot:cell(option)="data">
        <b-button @click="edit(data.value.id)" variant="outline-primary"
          >editer</b-button
        >
        <nuxt-link
          :to="{ name: 'Produits-id', params: { id: data.value.id } }"
          class="btn btn-outline-warning"
          >ventes</nuxt-link
        >
        <b-button
          @click="trash(data.value.id, data.value.libelle)"
          variant="outline-danger"
          >supprimer</b-button
        >
        <EditProduct :modalId="data.value.id" :produit="data"></EditProduct>
      </template>
    </b-table>
    <div>
      <h6>{{ 'PAGE: ' + page }}</h6>
      <b-pagination
        v-model="page"
        :total-rows="rows"
        :per-page="parPage"
        aria-controls="tableau"
        align="center"
        pills
      ></b-pagination>
    </div>
    <AddProduct></AddProduct>
  </div>
</template>

<script>
import AddProduct from '@/components/Produits/add'
import EditProduct from '@/components/Produits/edit'
export default {
  name: 'ListeProduit',
  components: {
    AddProduct,
    EditProduct
  },
  data() {
    return {
      items: [],
      page: 1,
      parPage: 7,
      critere: null,
      produit: null,
      fields: [
        { key: 'libelle', label: 'Libellé', sortable: true },
        { key: 'prix_achat', label: 'Prix achat', sortable: true },
        { key: 'prix_vente', label: 'Prix vente', sortable: true },
        { key: 'option', label: 'Option' }
      ]
    }
  },
  computed: {
    rows() {
      return this.items.length
    }
  },
  async fetch() {
    const { produits } = await this.$axios.$get('/api/produits')
    this.items = produits.map((produit) => {
      return {
        id: produit.id,
        libelle: produit.libelle,
        prix_achat: produit.prix_achat,
        prix_vente: produit.prix_vente,
        option: {
          libelle: produit.libelle,
          id: produit.id,
          prix_achat: produit.prix_achat,
          prix_vente: produit.prix_vente
        }
      }
    })
  },
  methods: {
    wait(milliseconds) {
      return new Promise((resolve) => setTimeout(resolve, milliseconds))
    },
    add() {
      this.$bvModal.show('add')
    },
    edit(id) {
      this.$bvModal.show('edit-' + id)
    },
    show(id) {
      console.log(id)
    },
    trash(id, libelle) {
      this.$bvModal
        .msgBoxConfirm(`Voulez vous vraiment supprimer le produit ${libelle}`, {
          title: 'SUPPRESSION',
          size: 'sm',
          buttonSize: 'sm',
          okVariant: 'danger',
          okTitle: 'supprimer',
          cancelTitle: 'abandonner',
          footerClass: 'p-2',
          hideHeaderClose: false,
          centered: true
        })
        .then((value) => {
          if (value) {
            this.$axios
              .$post('/api/produit/trash', { id })
              .then((response) => {
                this.$bvToast.toast(response.message, {
                  title: `SUPPRESSION DE PRODUIT`,
                  variant: 'success',
                  solid: true
                })
                this.wait(1700).then(() => location.reload())
              })
              .catch((err) => {
                console.log(err)
              })
          }
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

<style scoped>
.container {
  margin-top: 20px;
  margin-bottom: 40px;
}
.commandes {
  margin-bottom: 15px;
}
.recherche {
  margin-bottom: 5px;
  margin-top: 5px;
}
</style>
