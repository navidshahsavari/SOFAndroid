<template>
  <div class="person">
    <b-card :img-src="owner.profile_image" img-left img-width="64" img-height="64">
      <b-link :href="owner.link">{{owner.display_name}}</b-link>
      <span class="reputation">{{owner.reputation}}</span>
    </b-card>
  </div>
</template>
<script>
    export default {
        props: ['owner']
    }
</script>
<style>
  .person {
    display: flex;
    justify-content: flex-end;
  }

  .person .reputation {
    display: block;
  }

  .person .card-body {
    padding: 8px;
    text-align: center;
  }
</style>
