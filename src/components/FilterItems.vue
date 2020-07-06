<template>
  <input
    type="text"
    placeholder="Album Id"
    id="album-filter"
    v-model="filterInput"
    v-on:keyup.enter="updateContentTableRows"
  >
</template>

<script>
  export default {
    name: 'FilterItems',
    components: {},
    props: ["rows"],
    data() {
      return {
        filterInput: '',
      };
    },    
    created() {
      const rowsFilterInput = this.getFilterInput();
      if (rowsFilterInput === null) {
        return this.setFilterInput();
      } else if (rowsFilterInput.length > 0) {
        return this.FilterInput = rowsFilterInput;
      }
    },
    methods: {
      updateContentTableRows() {
        this.$emit("keyupEnter", this.filterAlbumById())
      },
      getAllRows() {
        return JSON.parse(localStorage.getItem('allRowsCached'));
      },
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = this.rows.length < defaultRowsAmount ? 0 : this.rows.length;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return this.getAllRows.slice(startIndexValue, endIndexValue);
      },
      getFilterInput() {
        return JSON.parse(localStorage.getItem('rowsFilterInput'));
      },
      setFilterInput() {
        localStorage.setItem('rowsFilterInput', JSON.stringify(this.filterInput));
      },
      filterAlbumById() {
        const rowsData = JSON.parse(localStorage.getItem('allRowsCached'));
        let results = [];
        if (this.filterInput.length === 0) {
          this.resetScroll();
          results = this.getSomeRows();
        } else {
          const albumIdValue = Number(this.filterInput)
          if (isNaN(Number(this.filterInput)) === false) {
            for (const item of rowsData) {
              if (item.albumId === albumIdValue) {
                  results.push(item);
              }
            }
          }
        }
        return results;
      },
    }
  }
</script>

<style>
  #album-filter {
    margin: 10px;
    max-width: 70px;
  }
</style>
