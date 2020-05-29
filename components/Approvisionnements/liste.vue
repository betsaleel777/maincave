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
        <!-- <b-button @click="show(data.value.id)" variant="outline-warning"
          >voir</b-button
        > -->
        <b-button
          @click="trash(data.value.id, data.value.code)"
          variant="outline-danger"
          >supprimer</b-button
        >
        <EditAchat :modalId="data.value.id" :achat="data"></EditAchat>
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
    <AddAchat :produits="produits"></AddAchat>
  </div>
</template>

<script>
import AddAchat from '@/components/Approvisionnements/add'
import EditAchat from '@/components/Approvisionnements/edit'
export default {
  name: 'ListeProduit',
  components: {
    AddAchat,
    EditAchat
  },
  data() {
    return {
      items: [],
      produits: [],
      page: 1,
      parPage: 7,
      critere: null,
      fields: [
        { key: 'code', label: 'Code', sortable: false },
        { key: 'libelle', label: 'Produit', sortable: true },
        { key: 'prix_achat', label: 'Prix achat', sortable: true },
        { key: 'quantite', label: 'Quantite', sortable: true },
        { key: 'date', label: 'Date', sortable: true },
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
    const { approvisionnements } = await this.$axios.$get(
      '/api/approvisionnements'
    )
    this.items = approvisionnements.map((approvisionnement) => {
      return {
        id: approvisionnement.id,
        code: approvisionnement.code,
        quantite: approvisionnement.quantite,
        date: approvisionnement.created_at,
        produit: approvisionnement.produit_linked.id,
        libelle: approvisionnement.produit_linked.libelle,
        prix_achat: approvisionnement.produit_linked.prix_achat,
        option: {
          id: approvisionnement.id,
          code: approvisionnement.code,
          date: approvisionnement.created_at,
          quantite: approvisionnement.quantite
        }
      }
    })
    const { produits } = await this.$axios.$get('/api/produits')
    this.produits = produits.map((produit) => {
      return {
        code: produit.id,
        label: produit.libelle
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
    trash(id, code) {
      this.$bvModal
        .msgBoxConfirm(`Voulez vous vraiment supprimer l'achat ${code}`, {
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
              .$post('/api/approvisionnement/trash', { id })
              .then((response) => {
                this.$bvToast.toast(response.message, {
                  title: `SUPPRESSION DE L'ACHAT ${code}`,
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
