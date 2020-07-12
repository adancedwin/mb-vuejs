<template>
  <input
    type="text"
    placeholder="Album Id"
    id="album-filter"
    v-model="filterInput"
    v-on:keyup.enter="updateContentTableRows(filterAlbumById())"
  >
</template>

<script>
  export default {
    name: 'FilterItems',
    data() {
      return {
        filterInput: "",
      };
    },
    mounted() {
      const rowsFilterInput = this.getCachedFilterInput();
      if (rowsFilterInput === null) {
        this.cacheFilterInput(this.filterInput);
      } else {
        this.filterInput = rowsFilterInput;
      }
      this.manageFilterInput();
    },
    methods: {
      manageFilterInput() {
        const rowsFilterInput = this.getCachedFilterInput();
        if (this.filterInput.length > 0) {
          return this.cacheFilterInput(this.filterInput);
        } else if (this.filterInput.length === 0 && rowsFilterInput.length > 0) {
          return this.cacheFilterInput(this.filterInput);
        }
      },
      updateContentTableRows(newRows) {
        this.$emit("keyupEnter", newRows)
      },
      getAllRows() {
        return JSON.parse(localStorage.getItem('allRowsCached'));
      },
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = 0;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        let allRows = this.getAllRows();
        return allRows.slice(startIndexValue, endIndexValue);
      },
      getCachedFilterInput() {
        return JSON.parse(localStorage.getItem('rowsFilterInput'));
      },
      cacheFilterInput(value) {
        localStorage.setItem('rowsFilterInput', JSON.stringify(value));
      },
      filterAlbumById() {
        this.manageFilterInput()
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
      resetScroll() {
        this.scroll = 3300;
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
