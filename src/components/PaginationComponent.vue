<template>
  <div class="pagination-container">
    <ul class="pagination-list">
      <li
        v-for="(page, index) in pagination"
        v-bind:key="`page-${index}`"
        v-bind:class="['page-item', { active: page == checkActivePage() }]"
      >
        <button
          class="app-button app-button-transparent page"
          v-on:click="changePage(page)"
          v-bind:disabled="page == currentPage"
        >
          {{ page }}
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "PaginationComponent",
  props: {
    pages: {
      type: Number,
      default: 0,
    },
    pagesLimit: {
      type: Number,
      default: 5,
    },
  },
  data() {
    return {
      pagination: [],
    };
  },
  methods: {
    changePage(pageNumber) {
      this.$root.$emit("changePage", { pageNumber: pageNumber });
    },
    checkActivePage() {
      if (!this.$route.query.page) return 1;
      return this.$route.query.page;
    },
    buildPagination() {
      const startPage = 1;
      const prevPages = this.pagesLimit - 3;
      const startPaginationupdate = this.pagesLimit;
      const showMorePages = this.pagesLimit - 3;

      // PAGES LESS THAN LIMIT
      if (this.pages <= this.pagesLimit) {
        this.pagination = [...Array(this.pages)].map(
          (pageNum, index) => startPage + index
        );
        return;
      }

      // LAST PAGES
      if (this.currentPage + showMorePages >= this.pages) {
        const page = this.pages - this.pagesLimit + 1;
        this.pagination = [...Array(this.pagesLimit)].map(
          (pageNum, index) => page + index
        );
        this.pagination.unshift(1, "...");
        return;
      }

      // MIDDLE PAGES
      if (this.currentPage >= startPaginationupdate) {
        const page = this.currentPage - prevPages;
        this.pagination = [...Array(this.pagesLimit)].map(
          (pageNum, index) => page + index
        );
        this.pagination.unshift(1, "...");
        this.pagination.push("...", this.pages);
        return;
      }

      // FIRST PAGES
      this.pagination = [...Array(this.pagesLimit)].map(
        (pageNum, index) => startPage + index
      );
      this.pagination.push("...", this.pages);
    },
  },
  created() {
    this.buildPagination();
  },
  computed: {
    currentPage() {
      return !this.$route.query.page ? 1 : Number(this.$route.query.page);
    },
  },
  watch: {
    "$route.query.page": {
      handler(value) {
        if (value) {
          this.buildPagination();
        }
      },
    },
  },
};
</script>

<style scoped>
.pagination-container {
  position: relative;
  width: 100%;
  margin: 20px 0 0;
}

.pagination-list {
  position: relative;
  width: 100%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.page {
  padding: 5px;
  font-size: 0.9rem;
  color: var(--color-medium-grey);
}

.active .page {
  color: var(--color-main);
  font-weight: bold;
}
</style>
