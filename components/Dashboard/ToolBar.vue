<template>
  <center>
    <b-button-group>
      <b-button @click="allProduct" variant="outline-success"
        >Liste produits</b-button
      >
      <b-button
        @click="confirmerFermeture"
        :disabled="!traitement"
        variant="outline-danger"
        >fermeture</b-button
      >
      <b-button @click="runModal" variant="outline-dark"
        >Ancien Inventaire</b-button
      >
      <b-button
        @click="confirmerInventaire"
        v-if="!traitement"
        variant="outline-primary"
        >Inventaire</b-button
      >
      <b-button @click="annuler" v-if="traitement" variant="outline-primary"
        >Annuler</b-button
      >
    </b-button-group>
    <ModalDate :dates="dates"></ModalDate>
  </center>
</template>

<script>
import ModalDate from '@/components/Dashboard/ModalDate'
export default {
  name: 'ToolBar',
  components: {
    ModalDate
  },
  props: ['traitement', 'dates'],
  methods: {
    wait(milliseconds) {
      return new Promise((resolve) => setTimeout(resolve, milliseconds))
    },
    runModal() {
      this.$bvModal.show('modal')
    },
    annuler() {
      this.$axios.$get('/api/dashboard/annuler').then((response) => {
        this.$bvToast.toast(response.message, {
          title: `ANNULATION DE BILAN D'INVENTAIRE`,
          variant: 'success',
          solid: true
        })
      })
      this.wait(1750).then(() => location.reload())
    },
    allProduct() {
      location.href = '/listing'
    },
    fermer() {
      this.$axios
        .$get('/api/dashboard/fermeture')
        .then((response) => {
          this.$bvToast.toast(response.message, {
            title: `FERMETURE`,
            variant: 'success',
            solid: true
          })
          this.wait(1750).then(() => location.reload())
        })
        .catch((err) => {
          console.log(err)
        })
    },
    confirmerInventaire() {
      const message = `Si vous validez l'enregistrement de l'inventaire les données de la MainCave
      passeront en transition de fermeture. Par conséquent aucun achat ou vente ne pourras être ajouté.
      Néanmoins des modifications pourront être effectuées. Vous pourrez toujours annuler cette
      opération dans le tableau de bord de la MainCave.`
      this.$bvModal
        .msgBoxConfirm(message, {
          title: "Enregistrement d'Inventaire",
          buttonSize: 'sm',
          okVariant: 'primary',
          okTitle: 'Valider',
          cancelTitle: 'Abandonner',
          footerClass: 'p-2',
          hideHeaderClose: false,
          centered: true
        })
        .then((value) => {
          if (value) {
            location.href = '/inventaire'
          }
        })
        .catch((err) => {
          alert(err)
        })
    },
    confirmerFermeture() {
      const message = `Si vous validez la fermeture, les données de la MainCave pour cette période
      seront déinitivements innaccéssibles.Puisqu'un nouvel exercice s'ouvre alors tout les achats
      et ventes de la période passée ne pourront plus être manipulés.`
      this.$bvModal
        .msgBoxConfirm(message, {
          title: 'FERMETURE',
          buttonSize: 'sm',
          okVariant: 'primary',
          okTitle: 'Valider',
          cancelTitle: 'Abandonner',
          footerClass: 'p-2',
          hideHeaderClose: false,
          centered: true
        })
        .then((value) => {
          this.fermer()
        })
        .catch((err) => {
          alert(err)
        })
    }
  }
}
</script>

<style scoped></style>
