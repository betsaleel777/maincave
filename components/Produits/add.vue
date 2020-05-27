/* eslint-disable no-console */
<template>
  <div>
    <b-modal id="add" @ok="save" title="AJOUTER UN PRODUIT">
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
  name: 'AddProduct',
  data() {
    return {
      alerter: false,
      messages: [],
      libelle: null,
      prix_achat: null,
      prix_vente: null
    }
  },
  methods: {
    wait(milliseconds) {
      return new Promise((resolve) => setTimeout(resolve, milliseconds))
    },
    dismiss() {
      this.alerter = false
      this.messages = []
    },
    save(event) {
      if (this.messages.length > 0) {
        this.messages = []
      }
      event.preventDefault()
      this.$axios
        .$post('/api/produit/add', {
          libelle: this.libelle,
          prix_achat: this.prix_achat,
          prix_vente: this.prix_vente
        })
        .then((response) => {
          if (response.message) {
            this.$bvToast.toast(response.message, {
              title: `ENREGISTREMENT DE PRODUIT`,
              variant: 'success',
              solid: true
            })
          }
          this.wait(4000).then(() => location.reload())
        })
        .catch((err) => {
          this.alerter = true
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
        this.$bvModal.hide('add')
      })
    }
  }
}
</script>

<style lang="scss" scoped></style>
