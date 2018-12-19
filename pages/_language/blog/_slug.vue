<template>
  <section class="util__container">
    <div v-editable="story.content" class="blog">
      <h1>{{ story.content.name }}</h1>
      <div class="blog__body" v-html="body">
      </div>
    </div>
    <p>hi</p>
  </section>
</template>

<script>
import marked from 'marked'

export default {
  head () {
    return {
      title: 'Rahal Blog - ' + this.$route.params.slug
    }
  },
  data () {
    return { story: { content: { body: '' } } }
  },
  computed: {
    body () {
      return marked(this.story.content.body)
    }
  },
  mounted () {
    this.$storybridge.on(['input', 'published', 'change'], (event) => {
      if (event.action == 'input') {
        if (event.story.id === this.story.id) {
          this.story.content = event.story.content
        }
      } else {
        window.location.reload()
      }
    })
  },
  asyncData (context) {
    let version = context.query._storyblok || context.isDev ? 'draft' : 'published'
    let endpoint = `cdn/stories/${context.params.language}/blog/${context.params.slug}`

    return context.app.$storyapi.get(endpoint, {
      version: version,
      cv: context.store.state.cacheVersion
    }).then((res) => {
      return res.data
    }).catch((res) => {
      context.error({ statusCode: res.response.status, message: res.response.data })
    })
  }
}
</script>


<style lang="scss">
.blog {
  padding: 0 20px;
  max-width: 600px;
  margin: 40px auto 100px;

  img {
    width: 100%;
    height: auto;
  }
}

.blog__body {
  line-height: 1.6;
}
</style>
