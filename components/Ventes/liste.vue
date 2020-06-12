<template>
  <div class="container">
    <div class="row commandes">
      <div class="col-md-10"></div>
      <div class="col-md-2">
        <button
          :disabled="traitement"
          @click="add"
          style="width:100%"
          class="btn btn-primary"
        >
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
        <b-button
          :disabled="data.value.fermer == true"
          @click="edit(data.value.id)"
          variant="outline-primary"
          >editer</b-button
        >
        <!-- <b-button @click="show(data.value.id)" variant="outline-warning"
          >voir</b-button
        > -->
        <b-button
          :disabled="data.value.fermer == true"
          @click="trash(data.value.id, data.value.code)"
          variant="outline-danger"
          >supprimer</b-button
        >
        <EditVente :modalId="data.value.id" :vente="data"></EditVente>
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
    <AddVente :produits="produits"></AddVente>
  </div>
</template>

<script>
import AddVente from '@/components/Ventes/add'
import EditVente from '@/components/Ventes/edit'
export default {
  name: 'ListeVente',
  components: {
    AddVente,
    EditVente
  },
  data() {
    return {
      items: [],
      produits: [],
      traitement: false,
      page: 1,
      parPage: 7,
      critere: null,
      fields: [
        { key: 'code', label: 'Code', sortable: false },
        { key: 'libelle', label: 'Produit', sortable: true },
        { key: 'prix_vente', label: 'Prix vente', sortable: true },
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
    const { ventes, traitement } = await this.$axios.$get('/api/ventes')
    this.items = ventes.map((vente) => {
      return {
        id: vente.id,
        code: vente.code,
        quantite: vente.quantite,
        date: vente.created_at,
        produit: vente.produit_linked.id,
        libelle: vente.produit_linked.libelle,
        prix_vente: vente.produit_linked.prix_vente,
        option: {
          id: vente.id,
          produit: vente.produit_linked.id,
          code: vente.code,
          date: vente.created_at,
          fermer: vente.fermer,
          quantite: vente.quantite
        }
      }
    })
    if (traitement) {
      this.traitement = traitement
    }
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
              .$post('/api/vente/trash', { id })
              .then((response) => {
                this.$bvToast.toast(response.message, {
                  title: `SUPPRESSION DE LA VENTE ${code}`,
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
