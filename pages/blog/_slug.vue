<template>
  <div>
    <div class="blog-slug-page">
      <div v-if="!isLoading" class="pt-4 my-20 lg:m-20 lg:mt-32">
        <div class="container mx-auto px-4 lg:px-4 relative">
          <div class="row flex flex-wrap">
            <div class="col w-full md:w-3/4 order-1">
              <div>
                <div class="mb-4">
                  <img
                    :src="post && post._embedded['wp:featuredmedia'][0].media_details.sizes.large.source_url"
                    alt="Post Image"
                  >
                </div>
                <header class="my-2">
                  <!--                <div ref="example-element">{{ this.post }}</div>-->
                  <h1 class="my-2" v-html="post && post.title.rendered" />
                </header>
                <div class="my-8">
                  <div class="blog-date">
                    <time datetime="2019-03-08T17:10:45-03:00">Publicado em
                      {{ moment(post && post.date).format('DD/MM/YYYY') }}
                    </time>
                  </div>
                </div>
                <div class="blog-content" v-html="post && renderedContent" />
              </div>
              <div v-if="!isMore" class="pt-10">
                <button id="btn_read_more_blog_post" class="mx-auto uppercase flex items-center justify-center px-8 py-3 border border-transparent text-base font-medium rounded-md text-white bg-green-shinny md:py-4 md:text-lg md:px-10" @click="readMore" >
                  {{ $t('blog_slug.btn_read_more') }}
                  <!-- {{moreBtntxt}} -->
                </button>
              </div>
            </div>
            <aside class="order-2 max-w-max flex flex-1 px-6 pt-16 md:pt-0">
              <div id="sidebar">
                <!-- <h2> {{ $t('online_courses.our_courses.card_cta') }}</h2> -->
                <!-- <h2 class="md:pb-8 md:mx-4">Course Offer:</h2> -->
                <blog-side-bar class="sticky top-o"/>
              </div>
            </aside>
            <author
              v-if="isMore"
              class="order-3"
              :author="post && post._embedded && post._embedded.author && post._embedded.author.length > 0 && post._embedded.author[0]"
            />
            <comment-list
              v-if="isMore"
              class="order-4"
              :author="post && post._embedded && post._embedded.author && post._embedded.author.length > 0 && post._embedded.author[0]"
              :replies="post && post._embedded && post._embedded.replies && post._embedded.replies.length > 0 && post._embedded.replies[0]"
              :post-id="post && post.id"
            />
            <add-comment
              v-if="isMore"
              class="order-5"
              :post-id="post && post.id"
              :comment-id="0"
            />
          </div>
          <div>
          </div>
        </div>
      </div>
      <div v-else class="container mx-auto min-h-80">
        <loading
          :active.sync="isLoading"
          :can-cancel="false"
          :is-full-page="fullPage"
          :color="color"
        />
      </div>
    </div>
    <div class="container mx-auto px-4 lg:px-4 sticky top-0" id="footer">
      <section-related-posts class="order-6 mb-20" />
      <related-services class="order-7 mb-20" />
    </div>
    <div>
      <section-search-slug />
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import blogData from 'static/data.json'
import BlogSideBar from '../../components/blog/BlogSideBar.vue'
export default {
  components: { BlogSideBar },
  asyncData ({
    params
  }) {
    const postdata = blogData &&
    blogData.filter((post) => {
      return post.slug === params.slug
    })
    return {
      post: postdata[0],
      isLoading: false
    }
  },
  data: () => ({
    text: '',
    hubspotform: '',
    post: null,
    isLoading: true,
    fullPage: true,
    color: '#ff6600',
    isMore: false,
    serviceNum: '0',
    isUserScrolling: false
  }),
  computed: {
    renderedTitle () {
      return this.post.excerpt.rendered.toString().replace(/(<([^>]+)>)/ig, '')
    },
    renderedContent () {
      if (this.isMore) {
        return this.post.content.rendered
      } else {
        return this.post.content.rendered.split('<!--more-->')[0]
      }
    }
  },
  updated () {
    const script = document.createElement('script')
    script.src = 'https://js.hsforms.net/forms/v2.js'
    document.body.appendChild(script)
    if (document.getElementById('hubspotform')) {
      this.hubspotform = document.getElementById('hubspotform').innerHTML
      this.formId = (this.hubspotform).split('formId:')[1].split(',')[0].replace('"', '').replace('"', '')
      script.addEventListener('load', () => {
        if (window.hbspt) {
          window.hbspt.forms.create({
            portalId: '5835830',
            formId: this.formId,
            target: '#hubspotform'
          })
        }
      })
    }
  },
  beforeMount () {
    window.addEventListener('wheel', this.handleScroll)
  },
  unmounted () {
    window.removeEventListener('wheel', this.handleScroll)
  },
  methods: {
    moment (date) {
      return moment(date)
    },
    submit () {
      this.$router.replace({ path: '/blog', query: { query: this.text } })
    },
    readMore () {
      this.isMore = !this.isMore
      if (this.isMore) {
        this.moreBtntxt = 'Read Less'
        document.getElementById('sidebar').className = 'fixed'
        document.getElementById('sidebar').style.bottom = 'unset'
      } else {
        this.moreBtntxt = 'Read More'
      }
    },
    handleScroll (event) {
      const topMargin = document.getElementById('sidebar').style.marginTop
      const top = document.getElementById('sidebar').offsetTop - parseFloat(topMargin === '' ? 0 : topMargin)
      const footTopMargin = document.getElementById('footer').style.marginTop
      const footTop = document.getElementById('footer').offsetTop - parseFloat(footTopMargin === '' ? 0 : footTopMargin)
      const temp = document.getElementById('sidebar').style.getPropertyValue('height') === '' ? 0 : document.getElementById('sidebar').style.getPropertyValue('height')
      const maxY = footTop - temp - 224
      const y = event.pageY
      if (y >= top) {
        if (y <= maxY) {
          document.getElementById('sidebar').className = 'fixed'
          document.getElementById('sidebar').style.bottom = 'unset'
        } else {
          document.getElementById('sidebar').className = 'absolute'
          document.getElementById('sidebar').style.bottom = '0px'
        }
      } else {
        document.getElementById('sidebar').style.position = 'absolute'
      }
    }
  },
  head () {
    return {
      title: this.post?.title.rendered,
      meta: [
        {
          hid: 'description',
          name: 'description',
          // content: this.post?.excerpt.rendered
          content: this.renderedTitle
        },
        {
          hid: 'twitter-card',
          name: 'twitter:card',
          content: 'summary'
        },
        {
          hid: 'og-title',
          property: 'og:title',
          content: this.post.title.rendered
        },
        {
          hid: 'og-description',
          property: 'og:description',
          content: this.renderedTitle
        },
        {
          hid: 'og-image',
          property: 'og:image',
          content: this.post._embedded['wp:featuredmedia'][0].media_details.sizes.large.source_url
        },
        {
          hid: 'og-url',
          property: 'og:url',
          content: this.$route.path
        }
      ]
    }
  }
}
</script>
