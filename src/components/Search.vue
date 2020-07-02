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
    props: ["rows"],
    data() {
      return {
        searchInput: '',
      };
    },
    created() {
      const rowsSearchInput = this.getSearchInput();
      if (rowsSearchInput === null) {
        return this.setSearchInput();
      } else if (rowsSearchInput.length > 0) {
        return this.searchInput = rowsSearchInput;
      }
    },
    methods: {
      getAllRows() {
        return JSON.parse(localStorage.getItem('allRowsCached'));
      },
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = this.rows.length < defaultRowsAmount ? 0 : this.rows.length;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return this.getAllRows.slice(startIndexValue, endIndexValue);
      },
      getSearchInput() {
        return JSON.parse(localStorage.getItem('rowsSearchInput'));
      },
      setSearchInput() {
        localStorage.setItem('rowsSearchInput', JSON.stringify(this.searchInput));
      },
      searchAlbumPhotoTitle() {
        this.setSearchInput();
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
