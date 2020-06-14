<template>
  <div>
    <b-modal
      :id="`edit-${modalId}`"
      @ok="edit"
      @cancel="refresh"
      :title="`MODIFIER LA VENTE : ${code}`"
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
        <label for="">Quantite</label>
        <input v-model="quantite" type="text" class="form-control" />
        <label for="datepicker">Date</label>
        <b-form-datepicker
          id="datepicker"
          v-model="date"
          today-button
          reset-button
          close-button
          locale="fr"
          selected-variant="success"
        ></b-form-datepicker>
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'EditVente',
  props: ['modalId', 'vente'],
  data() {
    return {
      alerter: false,
      messages: [],
      code: null,
      quantite: null,
      date: null
    }
  },
  mounted() {
    this.code = this.vente.value.code
    this.quantite = this.vente.value.quantite
    this.date = this.vente.value.date
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
        .$post('/api/vente/update', {
          id: this.modalId,
          quantite: this.quantite,
          produit: this.vente.value.produit,
          date: this.date
        })
        .then((response) => {
          if (response.message) {
            this.$bvToast.toast(response.message, {
              title: `MODIFICATION DE LA VENTE ${this.code}`,
              variant: response.variant,
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
              title: `MODIFICATION DE LA VENTE ${this.code}`,
              variant: 'danger',
              solid: true
            }
          )
          const { errors } = err.response.data
          if (errors.quantite) {
            this.messages.push(errors.quantite[0])
          }
        })
      this.$nextTick(() => {
        this.$bvModal.hide('edit')
      })
    },
    refresh() {
      this.$axios
        .$get('/api/vente/' + this.modalId)
        .then((response) => {
          this.code = response.vente.code
          this.quantite = response.vente.quantite
          this.produit = response.vente.produit_linked.id
          this.date = response.vente.created_at
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

<style lang="scss" scoped></style>
