<template>
  <div v-if="question == null">
    Please select a question to preview.
  </div>
  <div v-else class="question">
    <div style="display: flex">
      <h3 v-html="question.title"></h3>
      <b-link :href="question.link" target="_blank">
        <b-icon-box-arrow-up-right></b-icon-box-arrow-up-right>
      </b-link>
    </div>
    <b-spinner label="Loading..." v-if="question_data === null"></b-spinner>
    <div v-else class="mw">
      <b-badge v-for="tag in question_data.tags">{{tag}}</b-badge>

      <b-card :class="{answer:true, negative_score: question_data.score<0}"
              :title="question_data.score">
        <b-card-body v-html="question_data.body" class="highlight_here"></b-card-body>
        <b-card-footer v-if="question_data.comments && question_data.comments.length">
          <comments :comments="question_data.comments"></comments>
        </b-card-footer>
      </b-card>

      <h4 class="answers">Answers</h4>
      <hr>
      <b-card v-for="answer in question_data.answers" :class="{answer:true, negative_score: answer.score<0}"
              :title="answer.score">
        <b-card-body v-html="answer.body" class="highlight_here"></b-card-body>
        <b-card-footer v-if="answer.comments && answer.comments.length">
          <comments :comments="answer.comments"></comments>
        </b-card-footer>
      </b-card>
    </div>
  </div>
</template>
<script>
    import Comment from '~/components/comment.vue'
    import Comments from "./comments"
    import {BIconBoxArrowUpRight} from 'bootstrap-vue'

    export default {
        components: { Comments, Comment, BIconBoxArrowUpRight },
        props: ['question'],
        data: () => ({
            question_data: null,
        }),
        watch: {
            async question (q) {
                this.question_data = null
                let data = await fetch(`https://api.stackexchange.com/2.2/questions/${q.question_id}?order=desc&sort=activity&site=stackoverflow&filter=!)rFTNPYGo8X-CMe.-.T9`)
                this.question_data = (await data.json()).items[0]
                this.$nextTick(() => document.querySelectorAll('.highlight_here pre code').forEach((block) => {
                    hljs.highlightBlock(block)
                }))
            },
        },
    }
</script>
<style>
  .question {
    position: relative;
  }

  .question .badge {
    margin-right: 0.5em;
  }

  .question .mw, .question img, .question pre {
    max-width: 100%;
  }

  .question .answers {
    margin-top: 2em;
  }

  .answer .card-title {
    position: absolute;
    top: 0;
    left: 0;
    color: green;
    font-size: 10em;
    opacity: 0.3;
    z-index: -1;
    line-height: 0.7em;
  }

  .answer.negative_score .card-title {
    color: red;
  }

  .question .card-body, .question .card {
    background: none;
  }

  .question .hljs {
    background: #f0f0f075;
  }
</style>
