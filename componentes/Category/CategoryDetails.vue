<script setup>
  // Strapi
  const isLoading = ref(true);
  const categoryDetails = reactive({});
  const productsList = ref([]);

  const getCategory = async (categoryID) => {
    isLoading.value = true;
    const data = await useStrapi().getCategory(categoryID);
    Object.assign(categoryDetails, data);
    isLoading.value = false;
    productsList.value = categoryDetails.attributes.Products.data;
  };

  onMounted(() => {
    const categoryID = useRoute().params.id; // Getting the Category ID from the url
    getCategory(categoryID);
  });

  // Pagination
  const productsCount = computed(() => productsList.value.length);
  const resultsPerPage = ref(10);
  const currentPage = ref(1);
  const numberOfPages = computed(() =>
    Math.ceil(productsCount.value / resultsPerPage.value)
  );
  const nextPage = computed(() => currentPage.value + 1);
  const previousPage = computed(() => currentPage.value - 1);
  const startIndex = computed(
    () => (currentPage.value - 1) * resultsPerPage.value
  );
  const endIndex = computed(() => currentPage.value * resultsPerPage.value);

  // Search Filters
  // ---- Results Per Page
  const showFilters = [
    resultsPerPage.value,
    resultsPerPage.value + 10,
    resultsPerPage.value + 20,
  ];

  const updateResultsPerPage = (newValue) => {
    resultsPerPage.value = newValue;
  };

  // ---- Sort
  const sortFilters = [
    'Nombre - Descendente',
    'Nombre - Ascendente',
    'Precio - Menor a Mayor',
    'Precio - Mayor a Menor',
    'Calificación - Baja a Alta',
    'Calificación - Alta a Baja',
  ];
  const sortResults = (selector) => {
    if (selector === 'Nombre - Descendente') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Name < b.attributes.Name) {
          return -1;
        }
        if (a.attributes.Name > b.attributes.Name) {
          return 1;
        }
        return 0;
      });
    } else if (selector === 'Name - Ascending') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Name > b.attributes.Name) {
          return -1;
        }
        if (a.attributes.Name < b.attributes.Name) {
          return 1;
        }
        return 0;
      });
    } else if (selector === 'Price - Low to High') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Price_Current < b.attributes.Price_Current) {
          return -1;
        }
        if (a.attributes.Price_Current > b.attributes.Price_Current) {
          return 1;
        }
        return 0;
      });
    } else if (selector === 'Price - High to Low') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Price_Current > b.attributes.Price_Current) {
          return -1;
        }
        if (a.attributes.Price_Current < b.attributes.Price_Current) {
          return 1;
        }
        return 0;
      });
    } else if (selector === 'Rating - Low to High') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Rating_Value < b.attributes.Rating_Value) {
          return -1;
        }
        if (a.attributes.Rating_Value > b.attributes.Rating_Value) {
          return 1;
        }
        return 0;
      });
    } else if (selector === 'Rating - High to Low') {
      productsList.value.sort((a, b) => {
        if (a.attributes.Rating_Value > b.attributes.Rating_Value) {
          return -1;
        }
        if (a.attributes.Rating_Value < b.attributes.Rating_Value) {
          return 1;
        }
        return 0;
      });
    }
  };
  onMounted(() => {
    sortResults(sortFilters[0]);
  });
</script>

<template>
  <section class="app-section">
    <div class="wrapper">
      <CategoryBanner
        v-if="!isLoading"
        :category-name="categoryDetails.attributes.Name"
        :category-img-url="categoryDetails.attributes.Image.data.attributes.url"
      />
      <div v-else class="h-12 w-full animate-pulse rounded-full bg-gray-100" />

      <div class="category-products">
        <!-- Summary -->
        <div v-if="!isLoading" class="summary">
          <!-- Results Count -->
          <p class="text-gray-200">
            Found
            <span class="font-medium text-green-200">
              {{ categoryDetails.attributes.Products.data.length }}
            </span>
            Items!
          </p>

          <!-- Controls -->
          <div class="flex flex-col gap-2 2xs:flex-row">
            <BaseDropdown
              :values="showFilters"
              class="basis-full"
              @value-changed="updateResultsPerPage"
            >
              <template #icon>
                <span i-carbon-show-data-cards></span>
              </template>
              <template #caption> Show: </template>
            </BaseDropdown>

            <BaseDropdown
              :values="sortFilters"
              class="basis-full"
              @value-changed="sortResults"
            >
              <template #icon> <span i-bx-sort></span> </template>
              <template #caption> Sort: </template>
            </BaseDropdown>
          </div>
        </div>

        <div v-else class="summary">
          <div class="h-8 w-80 animate-pulse rounded-full bg-gray-100" />
          <div class="h-8 w-80 animate-pulse rounded-full bg-gray-100" />
        </div>

        <!-- Results -->
        <div v-if="!isLoading" class="products-grid">
          <ProductCard
            v-for="product in productsList.slice(startIndex, endIndex)"
            :key="product.id"
            :product="product"
          />

          <!-- Pagination -->
          <div class="pagination-controls">
            <button v-if="previousPage" @click="currentPage--">
              <span block i-bx-left-arrow-alt></span>
            </button>

            <p class="text-gray-200">
              Page
              <span>{{ currentPage }}</span>
              of
              <span>{{ numberOfPages }}</span>
            </p>

            <button v-if="nextPage <= numberOfPages" @click="currentPage++">
              <span block i-bx-right-arrow-alt></span>
            </button>
          </div>
        </div>

        <div v-else class="products-grid">
          <ProductSkeleton v-for="index in resultsPerPage" :key="index" />
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped lang="scss">
  .wrapper {
    @apply container flex flex-col gap-4;
  }

  .category-products {
    @apply flex flex-col gap-4;
  }

  .summary {
    @apply flex flex-col gap-2 lg:flex-row lg:items-center lg:justify-between;
  }
  .products-grid {
    @apply grid grid-cols-1 gap-4 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5;

    @media (min-width: 480px) and (max-width: 768px) {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }
  }

  .pagination-controls {
    @apply col-span-full flex items-center justify-center gap-4;

    p {
      span {
        @apply font-medium text-blue-200;
      }
    }

    button {
      @apply flex w-fit items-center justify-center  p-2;
      @apply rounded-full bg-gray-100 transition-colors  hover:bg-green-200 hover:text-white;

      span {
        @apply text-xl;
      }
    }
  }
</style>
