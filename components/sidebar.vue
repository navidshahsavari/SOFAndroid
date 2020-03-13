<template>
  <div>
    <h3>New Questions</h3>
    <b-list-group>
      <b-list-group-item class="pointer" @click="selectQuestion(question)" v-for="question in questions_new"
                         :key="question.question_id"
                         :active="question.question_id === selected" v-html="question.title"></b-list-group-item>
    </b-list-group>
    <h3>Most Voted Questions</h3>
    <b-list-group>
      <b-list-group-item class="pointer" @click="selectQuestion(question)" v-for="question in questions_votes"
                         :key="question.question_id"
                         :active="question.question_id === selected" v-html="question.title"></b-list-group-item>
    </b-list-group>
  </div>
</template>
<script>
    export default {
        props: ['questions_new', 'questions_votes'],
        data: () => ({
            selected: null,
        }),
        methods: {
            selectQuestion (question) {
                this.selected = question.question_id
                this.$emit('question_selected', question)
            },
        },
    }
</script>
<style scoped>
  .pointer {
    cursor: pointer;
  }
</style>
