<template>
  <div id="app">
    <search></search>
    <filterItems></filterItems>
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
  import Search from "./Search";
  import FilterItems from "./FilterItems";
  import Row from "./Row";

  export default {
    name: 'Table',
    components: {
      Row, Search, FilterItems
    },
    props: ["searchInput"],
    data() {
      return {
        nextSort: 'desc',
        sortKey: 'albumId',
        rows: [],
        scroll: 0,
      };
    },
    async created() {
      window.addEventListener('scroll', this.handleScroll);
      this.scroll = 3300;
      if (localStorage.getItem("allRowsCached") === null) {
        await this.fetchRows();
      }
      this.rows = this.getSomeRows();
    },
    destroyed () {
      window.removeEventListener('scroll', this.handleScroll);
    },
    computed: {
      allRows() {
        return JSON.parse(localStorage.getItem('allRowsCached'));
      }
    },
    methods: {
      async fetchRows() {
        const response = await axios.get('https://jsonplaceholder.typicode.com/albums')
        const allRows = await this.assembleRows(response.data);
        console.log(allRows);
        localStorage.setItem('allRowsCached', JSON.stringify(allRows));
      },
      async assembleRows(albums) {
        let allRows = [];
        const photos = await axios.get('https://jsonplaceholder.typicode.com/photos');
        for (let i = 0; i < albums.length; i++) {
          const albumPhotos = await this.getAlbumPhotos(albums[i], photos.data);
          allRows = [...allRows, ...albumPhotos];
        }
        return allRows;
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
      getSomeRows() {
        const defaultRowsAmount = 25;
        const startIndexValue = this.rows.length < defaultRowsAmount ? 0 : this.rows.length;
        const endIndexValue = startIndexValue + defaultRowsAmount;
        return this.allRows.slice(startIndexValue, endIndexValue);
      },
      handleScroll() {
        if (window.scrollY > this.scroll && (this.searchInput.length === 0 && this.filterInput.length === 0)) {
          this.scroll += 3300;
          this.rows.push(this.getSomeRows());
        }
      },
      resetScroll() {
        this.scroll = 3300;
      },
    }
  }
</script>

<style>
  th {
    top: 0;
    position: sticky;
    background: white;
  }
</style>
