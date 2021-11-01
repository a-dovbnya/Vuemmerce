<template>
  <div ref="loader" class="top-margin bottom-margin">
    <breadcrumbs-component :items="path" />
    <h1 v-if="tagTitle" class="title">Товары по тегу: {{ tagTitle }}</h1>
    <pagination-component :items="products">
      <template slot="itemsOnPage"
                slot-scope="{ itemsOnPage: products }">

        <div class="columns is-centered is-multiline">

          <div class="card column is-one-quarter"
               v-for="product in products"
               :key="product.id">
            <products-component :product="product"></products-component>
          </div>
          <div class="section" v-if="products.length === 0">
            <p>{{ noProductLabel }}</p>
          </div>
        </div>
      </template>
    </pagination-component>
  </div>
</template>

<script>
import ProductsComponent from '../Products';
import BreadcrumbsComponent from "../Breadcrumbs";
import PaginationComponent from '../pagination/Pagination'

export default {
  name: "Tags",
  components: {
    ProductsComponent,
    BreadcrumbsComponent,
    PaginationComponent
  },
  data () {
    return {
      noProductLabel: 'No product found by this tag',
      products: []
    };
  },

  mounted() {
    this.pseudoLoadingPage()
  },

  watch: {
    $route(to, from) {
      this.pseudoLoadingPage()
    }
  },

  computed: {
    path() {
      return [
        {
          text: "Home",
          to: { path: "/" },
        },
        {
          text: `Tag - ${this.tag}`,
        },
      ];
    },
    tag() {
      return parseInt(this.$route.params.id)
    },
    tagTitle() {
      const tag = this.$store.getters.getTagById(this.tag)
      return tag ? tag.title : null
    }
  },

  methods: {
    pseudoLoadingPage() {
      const loadingComponent = this.$buefy.loading.open({
        container: this.$refs.loader
      })

      this.$store.dispatch('pseudoFetchProductsByTag', this.tag).then(products => {
        this.products = products;
        loadingComponent.close();
      });
    }
  },
};
</script>

<style lang="scss" scoped>
  .top-margin {
    margin-top: 10px;
  }
  .bottom-margin {
    margin-bottom: 10px;
  }
  .card {
    margin: 10px;
  }
  .title {
    text-align: center;
  }
</style>
