<template>
  <div v-if="question == null">
    Please select a question to preview.
  </div>
  <div v-else>
    {{question.title}}
  </div>
</template>
<script>
    export default {
        props: ['question']
    }
</script>
