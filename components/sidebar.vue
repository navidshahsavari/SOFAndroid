<template>
  <div>
    <h3>New Questions</h3>
    <b-list-group>
      <b-list-group-item class="pointer" @click="selectQuestion(question)" v-for="question in questions_new"
                         :key="question.question_id"
                         :active="question.question_id === selected">
        <b-badge variant="info" v-if="!is_seen(question.question_id)">New</b-badge>
        <span v-html="question.title"></span>
      </b-list-group-item>
    </b-list-group>
    <h3 style="margin-top: 1em">Most Voted Questions</h3>
    <b-list-group>
      <b-list-group-item class="pointer" @click="selectQuestion(question)" v-for="question in questions_votes"
                         :key="question.question_id"
                         :active="question.question_id === selected">
        <b-badge variant="info" v-if="!is_seen(question.question_id)">New</b-badge>
        <span v-html="question.title"></span>
      </b-list-group-item>
    </b-list-group>
  </div>
</template>
<script>
    import Lockr from 'lockr'

    export default {
        props: ['questions_new', 'questions_votes'],
        data: () => ({
            selected: null,
            seen_questions: {},
        }),
        created () {
            let seen = Lockr.get('seen')
            for (let q of (seen || []))
                this.seen_questions[q] = true
        },
        methods: {
            selectQuestion (question) {
                this.selected = question.question_id
                this.set_seen(question.question_id)
                this.$emit('question_selected', question)
            },
            is_seen (id) {
                return !!this.seen_questions[id]
            },
            set_seen (id) {
                if(!this.is_seen(id)) {
                    this.seen_questions[id] = true
                    let seen = Lockr.get('seen') || []
                    seen.push(id)
                    Lockr.set('seen', seen)
                }
            },
        },
    }
</script>
<style scoped>
  .pointer {
    cursor: pointer;
  }
</style>
