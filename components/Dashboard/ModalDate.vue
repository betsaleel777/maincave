<template>
  <b-modal id="modal" @ok="oldInventoryGet" title="Ancien bilans d'inventaire">
    <v-select v-model="date" :options="dates"></v-select>
  </b-modal>
</template>

<script>
export default {
  name: 'ModalDate',
  props: ['dates'],
  data() {
    return {
      date: null
    }
  },
  methods: {
    oldInventoryGet() {
      // afficher une page imprimable contenant les informations de l'inventaire à cette date
      console.log('inventaire passé demandé')
    }
  }
}
</script>

<style lang="scss" scoped></style>
