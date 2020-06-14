/* eslint-disable no-console */
<template>
  <div>
    <b-modal id="add" @ok="save" title="AJOUTER UN ACHAT">
      <div class="form-group">
        <b-alert
          :show="alerter"
          @dismissed="dismiss"
          variant="danger"
          dismissible
          fade
          >{{ messages.join(' , ') }}</b-alert
        >
        <label for="">Produit</label>
        <v-select v-model="selected" :options="produits"></v-select>
        <label for="">Quantite</label>
        <input v-model="quantite" type="text" class="form-control" />
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'AddAchat',
  props: ['produits'],
  data() {
    return {
      alerter: false,
      messages: [],
      selected: null,
      produit: null,
      quantite: null
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
      if (this.selected !== null) {
        this.produit = this.selected.code
      }
      this.$axios
        .$post('/api/approvisionnement/add', {
          produit: this.produit,
          quantite: this.quantite
        })
        .then((response) => {
          if (response.message) {
            this.$bvToast.toast(response.message, {
              title: `ENREGISTREMENT D'ACHAT`,
              variant: 'success',
              solid: true
            })
          }
          this.wait(2000).then(() => location.reload())
        })
        .catch((err) => {
          this.alerter = true
          this.$bvToast.toast(
            'les champs ont été mal remplis veuillez ressayez en tenant compe des indications',
            {
              title: `ENREGISTREMENT D'ACHAT`,
              variant: 'danger',
              solid: true
            }
          )
          const { errors } = err.response.data
          if (errors.produit) {
            this.messages.push(errors.produit[0])
          }
          if (errors.quantite) {
            this.messages.push(errors.quantite[0])
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
