<template>
  <div id="app">
    <input
      type="text"
      placeholder="Album Title or Photo Title"
      id="search-bar"
      v-model="searchInput"
      @keyup.enter="searchAlbumPhotoTitle()"
    >
    <input
      type="text"
      placeholder="Album Id"
      id="album-filter"
      v-model="filterInput"
      @keyup.enter="filterAlbumById()"
    >
    <table>
      <thead>
        <tr>
          <th><a href="#" v-on:click="sortRows('albumId')">Album ID</a></th>
          <th><a href="#" v-on:click="sortRows('albumTitle')">Album Title</a></th>
          <th><a href="#" v-on:click="sortRows('photoTitle')">Photo Title</a></th>
          <th>Photo Thumbnail</th>
        </tr>
      </thead>
      <tbody>
        <row v-for="(row, index) in rows" :row.sync="row" :key="index"></row>
      </tbody>
    </table>
  </div>
</template>

<script>
  import axios from "axios";
  import Row from "./components/Row";
  // import NProgress from "nprogress";
  // import 'nprogress/nprogress.css';

  export default {
    name: 'App',
    components: {
      Row
    },
    data() {
      return {
        nextSort: 'desc',
        sortKey: 'albumId',
        rows: [],
        allRowsCached: [],
        searchInput: '',
        filterInput: '',
        scroll: 0,
      };
    },
    async created() {
      window.addEventListener('scroll', this.handleScroll);
      this.scroll = 3300;
      let rows = this.allRows;
      if (rows.length === 0) {
        await this.fetchRows();
      }
      this.rows = this.getSomeRows();
    },
    destroyed () {
      window.removeEventListener('scroll', this.handleScroll);
    },
    computed: {
      allRows() {
        return this.allRowsCached;
      }
    },
    methods: {
      async fetchRows() {
        const response = await axios.get('https://jsonplaceholder.typicode.com/albums')
        await this.assembleRows(response.data);
        this.allRowsCached = [...this.allRowsCached];
      },
      async assembleRows(albums) {
        const photos = await axios.get('https://jsonplaceholder.typicode.com/photos');
        for (let i = 0; i < albums.length; i++) {
          const albumPhotos = await this.getAlbumPhotos(albums[i], photos.data);
          this.allRowsCached = [...this.allRowsCached, ...albumPhotos];
        }
      },
      getAlbumPhotos(album, photos) {
        let items = [];
        for (let i = 0; i < photos.length; i++) {
          if (photos[i].albumId === album.id) {
            items.push(
              {
                albumId: album.id,
                albumTitle: album.title,
                photoTitle: photos[i].title,
                photoThumbnailUrl: photos[i].thumbnailUrl
              }
            );
          }
        }
        return items;
      },
      sortRows(key) {
        let sortingRows = this.rows;
        if (this.sortKey !== key) {
          this.nextSort = 'asc';
          this.sortKey = key;
        }
        let sortingFunc = (a, b) => { return (b[key] < a[key]) - (b[key] > a[key]); };
        if (this.nextSort === 'desc') {
          sortingFunc = (a, b) => { return (b[key] > a[key]) - (b[key] < a[key]); };
        }
        this.rows = sortingRows.sort(sortingFunc);
        this.nextSort = this.nextSort === 'asc' ? 'desc' : 'asc';
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
      filterAlbumById() {
        const rowsData = this.allRowsCached;
        let results = [];
        if (this.filterInput.length === 0) {
          this.resetScroll();
          results = this.getSomeRows();
        } else {
          const albumIdValue = Number(this.filterInput)
          if (isNaN(Number(this.filterInput)) === false) {
            for (let i = 0; i < rowsData.length; i++) {
              const item = rowsData[i];
              if (item.albumId === albumIdValue) {
                results.push(item);
              }
            }
          }
        }
        this.rows = results;
      },
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = this.rows.length < defaultRowsAmount ? 0 : this.rows.length;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return this.allRowsCached.slice(startIndexValue, endIndexValue);
      },
      handleScroll() {
        if (window.scrollY > this.scroll && (this.searchInput.length === 0 && this.filterInput.length === 0)) {
          this.scroll += 3300;
          this.rows = [...this.rows, ...this.getSomeRows()];
        }
      },
      resetScroll() {
        this.scroll = 3300;
      }
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  th {
    top: 0;
    position: sticky;
    background: white;
  }

  #search-bar {
    margin: 10px;
    min-width: 160px;
  }

  #album-filter {
    margin: 10px;
    max-width: 70px;
  }
</style>
