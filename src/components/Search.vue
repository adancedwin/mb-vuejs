<template>
  <input
    type="text"
    placeholder="Album Title or Photo Title"
    id="search-bar"
    v-model="searchInput"
    v-on:keyup.enter="updateContentTableRows(searchAlbumPhotoTitle())"
  >
</template>

<script>
  export default {
    name: 'Search',
    data() {
      return {
        searchInput: "",
      };
    },
    mounted() {
      const rowsSearchInput = this.getCachedSearchInput();
      if (rowsSearchInput === null) {
        this.cacheSearchInput(this.searchInput);
      } else {
        this.searchInput = rowsSearchInput;
      }
      this.manageSearchInput();
    },
    methods: {
      manageSearchInput() {
        const rowsSearchInput = this.getCachedSearchInput();
        if (this.searchInput.length > 0) {
          return this.cacheSearchInput(this.searchInput);
        } else if (this.searchInput.length === 0 && rowsSearchInput.length > 0) {
          return this.cacheSearchInput(this.searchInput);
        }
      },
      updateContentTableRows(newRows) {
        this.$emit("keyupEnter", newRows)
      },
      getAllRows() {
        return JSON.parse(localStorage.getItem('allRowsCached'));
      },
      getSomeRows() {
        let allRows = this.getAllRows();
        const defaultRowsAmount = 25;
        const startIndexValue = 0;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return allRows.slice(startIndexValue, endIndexValue);
      },
      getCachedSearchInput() {
        return JSON.parse(localStorage.getItem('rowsSearchInput'));
      },
      cacheSearchInput(value) {
        localStorage.setItem('rowsSearchInput', JSON.stringify(value));
      },
      searchAlbumPhotoTitle() {
        this.manageSearchInput();
        const rowsData = this.getAllRows();
        let results = [];
        if (this.searchInput.length === 0) {
          this.resetScroll();
          results = this.getSomeRows();
        } else {
          for (const item of rowsData) {
            if (item.albumTitle.includes(this.searchInput) || item.photoTitle.includes(this.searchInput)) {
              results.push(item);
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
  #search-bar {
    margin: 10px;
    min-width: 160px;
  }
</style>
