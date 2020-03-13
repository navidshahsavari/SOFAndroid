<template>
  <b-container fluid>
    <b-row>
      <b-col cols="4">
        <sidebar @question_selected="select_question" :questions_new="question_new" :questions_votes="question_vote"></sidebar>
      </b-col>
      <b-col>
        <question :question="selected_question"></question>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
    import Sidebar from '~/components/sidebar.vue'
    import Question from '~/components/question.vue'

    export default {
        components: {
            Sidebar,
            Question,
        },
        data: () => ({
            question_new: [],
            question_vote: [],
            selected_question: null,
        }),
        mounted: async function () {
            let votes = await fetch('https://api.stackexchange.com/2.2/search?pagesize=10&order=desc&sort=votes&tagged=android&site=stackoverflow')
            let newest = await fetch('https://api.stackexchange.com/2.2/search?pagesize=10&order=desc&sort=creation&tagged=android&site=stackoverflow')

            this.question_vote = (await votes.json()).items
            this.question_new = (await newest.json()).items
        },
        methods: {
            select_question (question) {
                this.selected_question = question
            },
        },
    }
</script>

<style>

</style>
