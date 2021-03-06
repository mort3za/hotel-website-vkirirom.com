<template>
  <fragment>
    <page-header></page-header>
    <div class="page page-search light--text">
      <div class="page-content brand-gradient">
        <div class="position-relative hero">
          <v-overlay :absolute="true" :value="true" :opacity="0">
            <h1
              :class="$vuetify.breakpoint.mdAndUp ? 'display-1' : 'title'"
              class="primary--text font-weight-bold text-capitalize mb-0"
            >
              {{ resort.title }}
            </h1>
          </v-overlay>
          <v-img :aspect-ratio="2880 / 1008" :src="getResponsiveHeroImage(resort.backgroundImage)"> </v-img>
        </div>
        <v-container class="is-limited light--text px-2 px-md-0 py-8" grid-list-md>
          <div class="mb-8 mt-md-10 mb-md-6 pt-md-2 pb-md-10 title font-weight-normal text-center">
            <markdown-block :content="resort.description"></markdown-block>
          </div>

          <v-row dense>
            <v-col cols="12" sm="6" md="4" v-for="resort in categories" :key="resort.id">
              <card-product
                :title="resort.title"
                :description="resort.ctaText > 0 ? `Starting from ${resort.ctaText}$ per night` : resort.ctaText"
                :image="resort.featuredImage"
                :link="'/listing/' + resort.slug"
              ></card-product>
            </v-col>
          </v-row>
        </v-container>
      </div>
      <page-footer></page-footer>
    </div>
  </fragment>
</template>

<script lang="ts">
import Vue from 'vue';
import { PageService } from '@/connection/resources.js';
const PageFooter = () => import('@/components/PageFooter.vue');
const CardProduct = () => import('@/components/CardProduct.vue');
const PageHeader = () => import('@/components/PageHeader.vue');
const MarkdownBlock = () => import('@/components/MarkdownBlock.vue');
import { get } from 'lodash-es';
import store from '@/store';
import {
  transformCloudinaryUrl,
  getFormattedMetaDescription,
  getFormattedMetaTitle,
  removeOtherLanguagesExcept
} from '@/helpers';
import { appTitleTemplate } from '@/constants/app';

async function beforeRouteEnterOrUpdate(to, from, next) {
  const slug = to.params.id;
  const resortPromise = store.dispatch('resort/getItemBySlug', slug);
  const categoriesPromise = store.dispatch('category/getItemsByName', slug);
  await Promise.all([resortPromise, categoriesPromise]);
  next();
}

export default Vue.extend({
  name: 'search-page',
  components: {
    PageFooter,
    PageHeader,
    MarkdownBlock,
    CardProduct
  },
  props: ['slug'],
  async beforeRouteEnter(to, from, next) {
    beforeRouteEnterOrUpdate(to, from, next);
  },
  async beforeRouteUpdate(to, from, next) {
    beforeRouteEnterOrUpdate(to, from, next);
  },
  metaInfo() {
    return {
      title: getFormattedMetaTitle((this as any).resort.title),
      titleTemplate: appTitleTemplate,
      meta: [
        {
          vmid: 'description',
          name: 'description',
          content: getFormattedMetaDescription(
            removeOtherLanguagesExcept('en', (this as any).resort.description).innerText
          )
        }
      ],
      script: [
        {
          vmid: 'jsonld',
          type: 'application/ld+json',
          json: (this as any).resort.custom
        }
      ]
    };
  },
  computed: {
    resort() {
      return (this as any).$store.getters['resort/itemBySlug'](this.slug);
    },
    categories() {
      return (this as any).$store.getters['category/getItemsByName'](this.slug);
    }
  },
  methods: {
    getResponsiveHeroImage(url) {
      // @ts-ignore
      const breakpoint = this.$vuetify.breakpoint;
      const breakpointWidth = breakpoint.thresholds[breakpoint.name];
      return transformCloudinaryUrl(url, `f_auto${breakpointWidth ? `,w_${breakpointWidth}` : ''},c_scale`);
    }
  }
});
</script>

<style lang="scss">
@import '@/styles/utility.scss';
</style>

<style lang="scss" scoped>
.hero ::v-deep .v-overlay__content {
  top: -8vw;
}
</style>
