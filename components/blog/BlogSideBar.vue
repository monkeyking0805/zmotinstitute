<template>
  <a
    href="https://www.hotmart.com/product/curso-comportamento-do-consumidor-de-a-a-z/F64721889V"
    target="_blank"
    class="justify-center items-center"
    >
      <!-- <h3> code injection </h3> -->
      <!-- 1st block -->
            <div class="flex flex-col rounded-lg shadow-lg">
                <div>
                    <img
                        class="h-60 md:h-48 object-cover"
                        :src="require('~/assets/img/online-courses-thumbnails/Consumer_Behavior_From_A_To_Z_pt-br.jpg').src"
                        :alt="$t('homepage.blog.block_1_alt')"
                    />
                </div>
                <div class="w-full px-10 py-8 md:px-4 md:py-2 md:mt-4">
                    <button class="uppercase w-full flex items-center justify-center px-8 py-3 md:px-1 md:py-1 border border-transparent text-base font-medium rounded-md text-white bg-green-shinny">
                    {{ $t('online_courses.our_courses.card_cta') }}
                    </button>
                </div>
            </div>
  </a>
</template>

<script>
export default {
  name: 'BlogSideBar',
  data () {
    return {
      course: [
        {
          imageUrl: require('~/assets/img/online-courses-thumbnails/Consumer_Behavior_From_A_To_Z_pt-br.jpg')
        }
      ]
    }
  }
}
</script>

<style scoped>
h3 {
    margin-top: 0;
}
a:link {
    text-decoration: none;
}
</style>
