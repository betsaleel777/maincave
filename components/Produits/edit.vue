<template>
  <div>
    <b-modal
      :id="`edit-${modalId}`"
      @ok="edit"
      @cancel="refresh"
      title="MODIFIER UN PRODUIT"
    >
      <div class="form-group">
        <b-alert
          :show="alerter"
          @dismissed="dismiss"
          variant="danger"
          dismissible
          fade
          >{{ messages.join(' , ') }}</b-alert
        >
        <label for="">Libellé</label>
        <input v-model="libelle" type="text" class="form-control" />
        <label for="">Prix d'achat</label>
        <input v-model="prix_achat" type="text" class="form-control" />
        <label for="">Prix de vente</label>
        <input v-model="prix_vente" type="text" class="form-control" />
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'EditProduct',
  props: ['modalId', 'produit'],
  data() {
    return {
      alerter: false,
      messages: [],
      libelle: null,
      prix_achat: null,
      prix_vente: null
    }
  },
  mounted() {
    this.libelle = this.produit.value.libelle
    this.prix_achat = this.produit.value.prix_achat
    this.prix_vente = this.produit.value.prix_vente
  },
  methods: {
    wait(milliseconds) {
      return new Promise((resolve) => setTimeout(resolve, milliseconds))
    },
    dismiss() {
      this.alerter = false
      this.messages = []
    },
    edit(event) {
      if (this.messages.length > 0) {
        this.messages = []
      }
      event.preventDefault()
      this.$axios
        .$post('/api/produit/update', {
          id: this.produit.value.id,
          libelle: this.libelle,
          prix_achat: this.prix_achat,
          prix_vente: this.prix_vente
        })
        .then((response) => {
          if (response.message) {
            this.$bvToast.toast(response.message, {
              title: `MODIFICATION DE PRODUIT`,
              variant: 'success',
              solid: true
            })
          }
          this.wait(2000).then(() => location.reload())
        })
        .catch((err) => {
          this.alerter = true
          this.$bvToast.toast(
            'les champs ont été mal remplis veuillez ressayez en tenant compe des indication',
            {
              title: `ENREGISTREMENT DE PRODUIT`,
              variant: 'danger',
              solid: true
            }
          )
          const { errors } = err.response.data
          if (errors.libelle) {
            this.messages.push(errors.libelle[0])
          }
          if (errors.prix_achat) {
            this.messages.push(errors.prix_achat[0])
          }
          if (errors.prix_vente) {
            this.messages.push(errors.prix_vente[0])
          }
        })
      this.$nextTick(() => {
        this.$bvModal.hide('edit')
      })
    },
    refresh() {
      this.$axios
        .$get('/api/produit/' + this.modalId)
        .then((response) => {
          this.libelle = response.produit.libelle
          this.prix_achat = response.produit.prix_achat
          this.prix_vente = response.produit.prix_vente
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

<style lang="scss" scoped></style>
