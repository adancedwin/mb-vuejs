<template>
  <input
    type="text"
    placeholder="Album Title or Photo Title"
    id="search-bar"
    v-model="searchInput"
    @keyup.enter="searchAlbumPhotoTitle()"
  >
</template>

<script>
  export default {
    name: 'Search',
    components: {},
    props: ["rows", "allRowsCached"],
    data() {
      return {
        searchInput: '',
      };
    },
    methods: {
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = this.rows.length < defaultRowsAmount ? 0 : this.rows.length;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return this.allRowsCached.slice(startIndexValue, endIndexValue);
      },
      searchAlbumPhotoTitle() {
        const rowsData = this.allRowsCached;
        let results = [];
        if (this.searchInput.length === 0) {
          this.resetScroll();
          results = this.getSomeRows();
        } else {
          for (let i = 0; i < rowsData.length; i++) {
            const item = rowsData[i];
            if (item.albumTitle.includes(this.searchInput) || item.photoTitle.includes(this.searchInput)) {
              results.push(item);
            }
          }
        }
        this.rows = results;
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
