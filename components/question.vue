<template>
  <div v-if="question == null">
    Please select a question to preview.
  </div>
  <div v-else class="question">
    <template v-if="error">
      <b-alert show variant="danger">{{error}}</b-alert>
    </template>
    <template v-else>
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
          <b-card-body>
            <div class="highlight_here" v-html="question_data.body"></div>
            <person :owner="question_data.owner"></person>
          </b-card-body>
          <b-card-footer v-if="question_data.comments && question_data.comments.length">
            <comments :comments="question_data.comments"></comments>
          </b-card-footer>
        </b-card>

        <div style="display: flex;align-items: flex-end;justify-content: space-between">
          <h4 class="answers">{{answer_count}}</h4>
          <b-button-group size="sm">
            <b-button v-for="sortmode in sorts" @click="sort = sortmode" :variant="sortmode === sort ? 'info':'light'">
              {{sortmode}}
            </b-button>
          </b-button-group>
        </div>
        <hr>
        <b-card v-for="answer in sortedAnswers" :key="answer.post_id" :class="{answer:true, negative_score: answer.score<0}"
                :title="answer.score">
          <b-card-body>
            <div v-html="answer.body" class="highlight_here"></div>
            <person :owner="answer.owner"></person>
          </b-card-body>
          <b-card-footer v-if="answer.comments && answer.comments.length">
            <comments :comments="answer.comments"></comments>
          </b-card-footer>
        </b-card>
      </div>
    </template>
  </div>
</template>
<script>
    import Comment from '~/components/comment.vue'
    import Comments from "./comments"
    import {BIconBoxArrowUpRight} from 'bootstrap-vue'
    import Person from "./person";

    export default {
        components: { Person, Comments, Comment, BIconBoxArrowUpRight },
        props: ['question'],
        data: () => ({
            question_data: null,
            sort: 'Active',
            sorts: ['Active', 'Votes', 'Newest'],
            error: false,
        }),
        watch: {
            async question (q) {
                this.question_data = null
                let data = await fetch(`https://api.stackexchange.com/2.2/questions/${q.question_id}?order=desc&sort=activity&site=stackoverflow&filter=!)rFTNPYGo8X-CMe.-.T9`)
                let json = await data.json()
                if (json.error_id) {
                    this.error = json.error_message
                } else {
                    this.error = false
                    this.question_data = json.items[0]
                    this.$nextTick(() => document.querySelectorAll('.highlight_here pre code').forEach((block) => {
                        hljs.highlightBlock(block)
                    }))
                }

            },
        },
        computed: {
            sortedAnswers () {
                let answers = this.question_data.answers || []
                return answers.slice().sort((b, a) => {
                    if (this.sort === 'Active')
                        return a.last_activity_date - b.last_activity_date
                    else if (this.sort === 'Newest')
                        return a.creation_date - b.creation_date
                    else if (this.sort === 'Votes')
                        return a.score - b.score
                })
            },
            answer_count () {
                let count = (this.question_data.answers || []).length
                return (count ? count : 'No') + ' ' + 'Answer' + (count !== 1 ? 's' : '')
            },
        }
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
