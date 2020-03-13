<template>
  <div class="comment">
    <hr>
    <span v-html="comment.body"></span>
    <span>By {{comment.owner.display_name}}</span>
  </div>
</template>
<script>
    export default {
        props: ['comment']
    }
</script>
