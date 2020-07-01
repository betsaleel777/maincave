<template>
  <div>
    <div class="panneaux">
      <b-container fluid>
        <b-row>
          <b-col
            ><Panneau
              :variant="'primary'"
              :titre="'Total des ventes de la période en cours'"
              :valeur="ventes"
              :libelle="' '"
            ></Panneau
          ></b-col>
          <b-col
            ><Panneau
              :variant="'dark'"
              :titre="'Total des achats de la période en cours'"
              :valeur="achats"
              :libelle="' '"
            ></Panneau
          ></b-col>
          <b-col
            ><Panneau
              :variant="'info'"
              :titre="'Produit le plus demandé (quantite)'"
              :valeur="mostWantedQuantity"
              :libelle="mostWantedName"
            ></Panneau
          ></b-col>
          <b-col
            ><Panneau
              :variant="'success'"
              :titre="'Produit le plus rentable (montant)'"
              :valeur="mvpQuantity"
              :libelle="mvpName"
            ></Panneau
          ></b-col>
        </b-row>
      </b-container>
    </div>
    <div class="toolbar">
      <ToolBar :dates="dates" :traitement="traitement"></ToolBar>
    </div>
  </div>
</template>

<script>
import Panneau from '@/components/Dashboard/Panneau'
import ToolBar from '@/components/Dashboard/ToolBar'

export default {
  name: 'Index',
  components: {
    Panneau,
    ToolBar
  },
  async asyncData({ $axios }) {
    const {
      ventes,
      achats,
      mvpQuantity,
      mvpName,
      mostWantedQuantity,
      mostWantedName
    } = await $axios.$get('/api/dashboard/utils')
    const { dates } = await $axios.$get('/api/dashboard/dates/')
    const { traitement } = await $axios.$get('/api/dashboard/status/examen')
    return {
      traitement,
      dates,
      ventes,
      achats,
      mvpName,
      mvpQuantity,
      mostWantedName,
      mostWantedQuantity
    }
  },
  mounted() {
    this.$on('run-modal', () => {
      this.$bvModal.show('modal')
    })
  }
}
</script>
<style scoped>
.panneaux {
  margin: 30px;
}
</style>
