<template>
  <section class="util__container">
    <sui-card-group  :items-per-row="4" class="doubling">
      <sui-card v-for="(blogPost, index) in data.stories" :key="index" class="raised">
        <sui-image :src="blogPost.content.cover" />
        <sui-card-content>
          <sui-card-header>{{blogPost.content.name}}</sui-card-header>
          <sui-card-meta>
            <small>
              {{ blogPost.published_at }}
            </small>
          </sui-card-meta>
          <sui-card-description>
            <p>{{ blogPost.content.intro }}</p>
          </sui-card-description>
        </sui-card-content>
        <sui-card-content extra>
          <nuxt-link  :to="'/' + blogPost.full_slug" class="right floated">
            <sui-icon name="file alternate" /> Read Article
          </nuxt-link>
        </sui-card-content>
      </sui-card>
    </sui-card-group>
  </section>
</template>

<script>
export default {
  head () {
    return {
      title: 'Rahal Blog ' + this.$route.params.language + ' Language'
    }
  },
  data () {
    return { total: 0, data: { stories: [] } }
  },
  asyncData (context) {
    let version = context.query._storyblok || context.isDev ? 'draft' : 'published'

    return context.app.$storyapi.get('cdn/stories', {
      version: version,
      starts_with: `${context.store.state.language}/blog/blockchain`,
      cv: context.store.state.cacheVersion
    }).then((res) => {
      return res
    }).catch((res) => {
      context.error({ statusCode: res.response.status, message: res.response.data })
    })
  }
}
</script>

<style lang="scss">
.blog__overview {
  padding: 0 20px;
  max-width: 600px;
  margin: 40px auto 60px;

  p {
    line-height: 1.6;
  }
}

.blog__detail-link {
  color: #000;
}
</style>
