<template>
  <div class="index">
    <article-list :article="article" @loadmore="loadmoreArticle"></article-list>
  </div>
</template>

<script>
  import Carrousel from '~/components/article/archive/carrousel'
  import ArticleList from '~/components/article/archive/list'

  export default {
    name: 'tag-article-list',
    validate ({ params }) {
      return !!params.tag_slug
    },
    fetch({ store, params }) {
      return store.dispatch('loadArticles', params)
    },
    head () {
      const slug = this.defaultParams.tag_slug || ''
      const title = slug.toLowerCase().replace(/( |^)[a-z]/g, (L) => L.toUpperCase())
      return {
        title: `${title} | Tag`
      }
    },
    created() {
      if (!this.currentTag) {
        this.$router.back()
      }
    },
    components: {
      Carrousel,
      ArticleList
    },
    computed: {
      article() {
        return this.$store.state.article.list
      },
      currentTag() {
        return this.$store.state.tag.data.data.find((tag, index, arr) => {
          return Object.is(tag.slug, this.$route.params.tag_slug)
        })
      },
      defaultParams() {
        return {
          tag_slug: this.$route.params.tag_slug
        }
      },
      nextPageParams() {
        return Object.assign({
          page: this.article.data.pagination.current_page + 1
        }, this.defaultParams)
      }
    },
    methods: {
      loadmoreArticle() {
        this.$store.dispatch('loadArticles', this.nextPageParams)
      }
    }
  }
</script>
