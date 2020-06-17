<template>
  <tr>
    <td> {{ row.albumId }} </td>
    <td> {{ row.albumTitle }} </td>
    <td> {{ row.photoTitle }} </td>
    <td><img :src="`${row.photoThumbnailUrl}`"></td>
  </tr>
</template>

<script>
  export default {
    name: "Row",
    props: ['row']
  };
</script>
