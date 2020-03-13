<template>
  <div v-if="question == null">
    Please select a question to preview.
  </div>
  <div v-else>
    <h3 v-html="question.title"></h3>
    <b-spinner label="Loading..." v-if="question_data === null"></b-spinner>
    <div v-else>
      <b-badge v-for="tag in question_data.tags">{{tag}}</b-badge>
      <div v-html="question_data.body"></div>
      <comment v-for="comment in question_data.comments" :comment="comment"></comment>

      <h4>Answers</h4>
      <hr>
      <div v-for="answer in question_data.answers">
        <div v-html="answer.body"></div>
        <comment v-for="comment in answer.comments" :comment="comment"></comment>
      </div>
    </div>
  </div>
</template>
<script>
    import Comment from '~/components/comment.vue'

    export default {
        components: { Comment },
        props: ['question'],
        data: () => ({
            question_data: null,
        }),
        watch: {
            async question (q) {
                this.question_data = null
                let data = await fetch(`https://api.stackexchange.com/2.2/questions/${q.question_id}?order=desc&sort=activity&site=stackoverflow&filter=!)rFTNPYGo8X-CMe.-.T9`)
                this.question_data = (await data.json()).items[0]
            },
        },
    }
</script>
