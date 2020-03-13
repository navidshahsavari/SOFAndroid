<template>
  <div class="comment">
    <hr v-if="!first">
    <span v-if="comment.score > 0" class="score">{{comment.score}}</span>
    <span v-html="comment.body" class="body"></span>
    <span class="owner">By <span v-html="comment.owner.display_name"></span></span>
  </div>
</template>
<script>
    export default {
        props: ['comment', 'first']
    }
</script>
<style scoped>
  .score {
    color: orange;
  }

  .owner {
    color: #a2a2a2;
  }

  hr {
    margin: 0.3em;
  }

  .comment {
    font-size: 0.8em;
  }
</style>
