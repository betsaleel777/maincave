<template>
  <div>
    <b-modal
      :id="`edit-${modalId}`"
      @ok="edit"
      @cancel="refresh"
      :title="`MODIFIER L'ACHAT : ${code}`"
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
  name: 'EditAchat',
  props: ['modalId', 'achat'],
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
    this.code = this.achat.value.code
    this.quantite = this.achat.value.quantite
    this.date = this.achat.value.date
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
        .$post('/api/approvisionnement/update', {
          id: this.modalId,
          quantite: this.quantite,
          date: this.date
        })
        .then((response) => {
          if (response.message) {
            this.$bvToast.toast(response.message, {
              title: `MODIFICATION D'ACHAT ${this.code}`,
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
              title: `MODIFICATION D'ACHAT ${this.code}`,
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
        .$get('/api/approvisionnement/' + this.modalId)
        .then((response) => {
          this.code = response.approvisionnement.code
          this.quantite = response.approvisionnement.quantite
          this.date = response.approvisionnement.created_at
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

<style lang="scss" scoped></style>
