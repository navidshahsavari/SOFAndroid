<template>
  <b-container fluid>
    <b-row>
      <b-col cols="4">
        <sidebar v-if="!error && !loading" @question_selected="select_question" :questions_new="question_new"
                 :questions_votes="question_vote"></sidebar>
        <b-spinner v-else-if="loading"></b-spinner>
        <b-alert show variant="danger" v-else-if="error">{{error}}</b-alert>
      </b-col>
      <b-col cols="7">
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
            loading: 0,
            error: false,
        }),
        mounted: async function () {
            try {
                this.loading++

                let lastWeek = (~~(new Date() / 1000)) - (86400 * 7)
                let votes = await fetch(`https://api.stackexchange.com/2.2/search?pagesize=10&fromdate=${lastWeek}&order=desc&sort=votes&tagged=android&site=stackoverflow`)
                let newest = await fetch('https://api.stackexchange.com/2.2/search?pagesize=10&order=desc&sort=creation&tagged=android&site=stackoverflow')

                votes = (await votes.json())
                newest = (await newest.json())

                if (votes.error_id || newest.error_id) {
                    this.error = votes.error_message || newest.error_message
                }else{
                    this.error = false
                    this.question_vote = votes.items
                    this.question_new = newest.items
                }
            } finally {
                this.loading--
            }
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
