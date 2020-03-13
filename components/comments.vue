<template>
  <div v-if="comments">
    <comment v-for="(comment, i) in comments_limited" :comment="comment" :first="i===0"></comment>
    <a href="#" @click.prevent="show_all = true" v-if="comments.length>5 && !show_all" class="showall">Show All</a>
  </div>
</template>
<script>
    import Comment from "./comment";

    export default {
        props: ['comments'],
        components: { Comment },
        data: () => ({
            show_all: false,
        }),
        computed: {
            comments_limited () {
                let comments = this.comments || []
                if (this.show_all)
                    return comments
                else
                    return comments.slice(0, 5)
            }
        }
    }
</script>
<style>

</style>
