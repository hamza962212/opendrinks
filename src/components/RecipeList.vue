<template>
  <div id="recipe-list" class="container">
    <b-card-group deck>
      <div v-for="(o, i) in paginatedItems" v-bind:key="i" class="mb-2 p-1">
        <b-card
          :title="o.name"
          :img-src="o.image ? require(`@/assets/recipes/${o.image}`) : null"
          :img-alt="o.name"
          img-top
          style="max-width: 20rem;"
        >
          <b-card-text>
            {{ o.description }}
          </b-card-text>

          <b-button :href="'/recipe/' + o.filename" variant="primary">View Recipe</b-button>
          <FavoriteStar
            class="mt-2 float-right"
            @favorite="favorited(o.name)"
            :isFavorited="favorites.includes(o.name)"
          ></FavoriteStar>
        </b-card>
      </div>
    </b-card-group>

    <b-container>
      <b-row>
        <b-col cols="12" md="10">
          <b-pagination
            @change="onPageChanged"
            :total-rows="rows"
            :per-page="selected"
            v-model="currentPage"
          ></b-pagination>
        </b-col>
        <b-col cols="12" md="2">
          <b-form-select v-model="selected" :options="options" v-on:change="getSelectedItem">
          </b-form-select>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import FavoriteStar from './FavoriteStar.vue';

export default {
  name: 'RecipeList',
  props: {
    title: String,
    items: Array,
  },
  components: {
    FavoriteStar,
  },
  data() {
    return {
      currentPage: 1,
      perPage: 10,
      selected: 10,
      pageNumber: 0,
      options: [{ value: 10, text: '10' }, { value: 25, text: '25' }, { value: 50, text: '50' }],
      favorites: [],
    };
  },
  watch: {
    items() {
      this.pageNumber = 0;
    },
  },
  mounted() {
    window.document.title = this.title;
    this.favorites = JSON.parse(window.localStorage.getItem('favorites')) || [];
  },
  computed: {
    rows() {
      return this.items.length;
    },
    paginatedItems() {
      return this.items.slice(this.pageNumber * this.perPage, (this.pageNumber + 1) * this.perPage);
    },
  },
  methods: {
    onPageChanged(page) {
      this.pageNumber = page - 1;
    },
    getSelectedItem(event) {
      this.perPage = event;
    },
    favorited(name) {
      const index = this.favorites.indexOf(name);
      if (index !== -1) {
        this.favorites.splice(index, 1);
      } else {
        this.favorites.push(name);
      }
      window.localStorage.setItem('favorites', JSON.stringify(this.favorites));
      this.$emit('favoriteClick', name);
    },
  },
};
</script>

<style scoped>
.card-deck .card {
  margin-right: 0;
}
.card-text {
  display: block;
  display: -webkit-box;
  -webkit-line-clamp: 4;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  min-height: 6rem;
}
.card-img-top {
  height: 212px;
  object-fit: cover;
}
</style>
