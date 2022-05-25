<template>
  <main class="Container">
    <article v-if="currentArticle" class="Article">
      <div class="Article_Header">
        <ul class="Breadcrumb">
          <li class="Breadcrumb_Item">
            <nuxt-link to="/" class="Breadcrumb_Link">Home</nuxt-link>
          </li>
          <li v-if="currentArticle.category" class="Breadcrumb_Item">
            <nuxt-link
              :to="`/category/${currentArticle.category.slug}`"
              class="Breadcrumb_Link"
              >{{ currentArticle.category.name }}</nuxt-link
            >
          </li>
        </ul>
        <h1 class="Article_Title">{{ currentArticle.title }}</h1>
      </div>
      <!-- eslint-disable-next-line vue/no-v-html -->
      <div class="Article_Body" v-html="currentArticle.body"></div>
      <section v-if="articles.length > 0" class="Related">
        <h2 class="Related_Heading">Related articles</h2>
        <ul class="Related_List">
          <li
            v-for="article in articles"
            :key="article._id"
            class="Related_Item"
          >
            <NuxtLink :to="`/article/${article.slug}`">{{
              article.title
            }}</NuxtLink>
          </li>
        </ul>
      </section>
    </article>
  </main>
</template>

<script>
import { mapGetters } from 'vuex'
import { formatDate } from 'utils/date'
import { htmlToText } from 'html-to-text'

export default {
  async asyncData({ $config, store, params, redirect }) {
    await store.dispatch('fetchApp', $config)
    await store.dispatch('fetchCurrentArticle', {
      ...$config,
      slug: params.slug,
    })
    const currentArticle = store.getters.currentArticle
    if (!currentArticle) return redirect(302, '/')
    await store.dispatch('fetchArticles', {
      ...$config,
      query: {
        tags: { in: currentArticle.tags },
        slug: {
          ne: currentArticle.slug,
        },
        select: ['_id', 'slug', 'title'],
      },
    })
    return {}
  },
  head() {
    return {
      title: this.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.description,
        },
        {
          hid: 'og:type',
          property: 'og:type',
          content: 'article',
        },
        {
          hid: 'og:title',
          property: 'og:title',
          content: this.title,
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.description,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: this.ogImage,
        },
        {
          name: 'twitter:card',
          content: 'summary_large_image',
        },
      ],
    }
  },
  computed: {
    ...mapGetters(['app', 'currentArticle', 'articles']),
    meta() {
      if (this.currentArticle && this.currentArticle.meta) {
        return this.currentArticle.meta
      }
      return null
    },
    title() {
      if (this.meta && this.meta.title) {
        return this.meta.title
      }
      if (this.currentArticle && this.currentArticle.title) {
        return this.currentArticle.title
      }
      return this.app && (this.app.name || this.app.uid || 'Help center')
    },
    description() {
      if (this.meta && this.meta.description) {
        return this.meta.description
      }
      if (this.currentArticle && this.currentArticle.body) {
        return htmlToText(this.currentArticle.body, {
          selectors: [
            {
              selector: 'img',
              format: 'skip',
            },
          ],
        }).slice(0, 200)
      }
      return ''
    },
    ogImage() {
      if (this.meta && this.meta.ogImage) {
        return this.meta.ogImage.src
      }
      return ''
    },
    publishDate() {
      return this.currentArticle && this.currentArticle._sys.createdAt
        ? formatDate(this.currentArticle._sys.createdAt)
        : ''
    },
    publishDateForAttr() {
      return this.publishDate.replace(/\//g, '-')
    },
  },
}
</script>

<style scoped>
.Breadcrumb {
  display: flex;
  align-items: center;
  min-width: 0;
  margin: 8px 0;
  padding: 0;
  flex-wrap: wrap;
}
.Breadcrumb_Item {
  margin: 0;
  padding: 0;
  list-style: none;
}
.Breadcrumb_Link {
  padding: 0;
  margin: 0 32px 0 0;
  display: block;
  font-size: 1.4rem;
  position: relative;
  text-decoration: none;
  line-height: 1.5;
}
.Breadcrumb_Link:hover {
  text-decoration: underline;
}
.Breadcrumb_Link::before {
  pointer-events: none;
  position: absolute;
  content: '';
  right: -18px;
  top: 7px;
  width: 6px;
  height: 6px;
  border-top: 1px solid #888;
  border-right: 1px solid #888;
  transform: rotate(45deg);
}
.Breadcrumb_Item:last-child .Breadcrumb_Link {
  margin: 0;
}
.Breadcrumb_Item:last-child .Breadcrumb_Link::before {
  content: none;
}
.Article {
  margin: 0 auto;
  padding: 24px 24px 40px 24px;
}
.Article_Header {
  margin: 0 0 24px 0;
}
.Article_Title {
  font-size: 2.4rem;
  line-height: 1.5;
  font-weight: bold;
  margin: 0;
  padding: 0;
}
.Article_Body >>> h1,
.Article_Body >>> h2,
.Article_Body >>> h3,
.Article_Body >>> h4,
.Article_Body >>> h5,
.Article_Body >>> h6 {
  padding: 0;
  margin: 40px 0 24px 0;
  line-height: 1.4;
}
.Article_Body >>> h1 {
  font-size: 2.4rem;
}
.Article_Body >>> h2 {
  font-size: 2.2rem;
}
.Article_Body >>> h3 {
  font-size: 2rem;
}
.Article_Body >>> h4 {
  font-size: 1.8rem;
}
.Article_Body >>> h5 {
  font-size: 1.6rem;
}
.Article_Body >>> h6 {
  font-size: 1.4rem;
}
.Article_Body >>> p {
  margin: 0 0 24px 0;
}
.Article_Body >>> img {
  max-width: 100%;
  height: auto;
  margin: 32px auto;
  display: block;
}
.Article_Body >>> ul,
.Article_Body >>> ol {
  margin: 0;
  padding: 0 0 16px 40px;
}
.Article_Body >>> ul li,
.Article_Body >>> ol li {
  margin: 0 0 4px 0;
  padding: 0;
}
.Article_Body >>> ul li ul,
.Article_Body >>> ul li ol,
.Article_Body >>> ol li ol,
.Article_Body >>> ol li ul {
  padding: 0 0 0 20px;
}
.Article_Body >>> blockquote {
  border-left: 4px solid #ccc;
  padding: 0 0 0 40px;
  margin: 0 0 20px 0;
}
.Article_Body >>> pre {
  background: #333;
  color: #fff;
  border-radius: 4px;
  padding: 16px 20px;
  margin: 0 0 20px 0;
  font-size: 1.4rem;
  line-height: 1.6;
  overflow: auto;
  font-family: 'Segoe UI Emoji', 'Helvetica Neue', Arial,
    'Hiragino Kaku Gothic ProN', 'Hiragino Sans', Meiryo, sans-serif;
}
.Article_Body >>> code {
  border: 1px solid #ddd;
  background: #f8f8f8;
  border-radius: 4px;
  padding: 2px 4px;
  margin: 0 4px;
  color: #e01d5a;
  font-size: 1.4rem;
  font-family: 'Segoe UI Emoji', 'Helvetica Neue', Arial,
    'Hiragino Kaku Gothic ProN', 'Hiragino Sans', Meiryo, sans-serif;
}
.Article_Body >>> pre code {
  border: none;
  background: none;
  border-radius: 0;
  padding: 0;
  margin: 0;
  color: #fff;
}
.Related {
  border-top: 1px solid #e5e5e5;
  margin: 36px 0 0 0;
  padding: 28px 0 0 0;
}
.Related_Heading {
  margin: 0 0 16px 0;
  padding: 0;
  font-size: 1.8rem;
}
.Related_List {
  margin: 0;
  padding: 0;
}
.Related_Item {
  margin: 0 0 8px 28px;
  padding: 0;
}

@media (min-width: 600px) {
  .Article {
    max-width: 700px;
    padding: 60px;
  }
  .Article_Header {
    margin: 0 0 48px 0;
  }
  .Related {
    margin: 56px 0 0 0;
    padding: 44px 0 0 0;
  }
}
</style>
