<script setup>
  const isNavVisible = inject('isNavVisible');
  const closeMenu = () => {
    isNavVisible.value = false;
  };
</script>

<template>
  <nav class="nav" :class="{ 'nav--visible': isNavVisible }">
    <div class="wrapper">
      <!-- User Section -->
      <section class="nav-section user">
        <div class="container">
          <HeaderUserCard />
        </div>
      </section>

      <!-- Links Section -->
      <section class="nav-section">
        <div class="container">
          <NuxtLink
            to="/categories"
            class="app-button w-fit"
            @click="closeMenu"
          >
            <span i-bx-grid-alt></span>
            Todas las Categorías
          </NuxtLink>

          <ul>
            <li>
              <NuxtLink
                to="/"
                class="nav-link nav-link--active"
                @click="closeMenu"
              >
                <span i-bx-home></span> Inicio
              </NuxtLink>
            </li>

            <li>
              <NuxtLink
                to="/products?featured=true"
                class="nav-link"
                @click="closeMenu"
              >
                <span i-carbon-percentage></span> Zonas de Producción
              </NuxtLink>
            </li>

            <li>
              <NuxtLink
                to="/products?dailyBestSells=true"
                class="nav-link"
                @click="closeMenu"
              >
                <span i-bxs-hot></span> Daily Best Deals
              </NuxtLink>
            </li>
          </ul>
        </div>
      </section>

      <!-- Contact Us Section -->
      <section class="nav-section contact-us">
        <div class="container">
          <p class="text-lg font-semibold">Contactenos</p>

          <ul>
            <li>
              <span i-carbon-location-filled></span>
              <p class="font-medium">Dirección:</p>
              <p class="text-blue-200">Calle 1 Carrera 1</p>
            </li>

            <li>
              <span i-bx-support></span>
              <p class="font-medium">Soporte:</p>
              <p class="text-blue-200">123-456</p>
            </li>

            <li>
              <span i-carbon-email></span>
              <p class="font-medium">Email:</p>
              <p class="text-blue-200">soporte@agroventas.com</p>
            </li>

            
          </ul>
        </div>
      </section>

      <!-- Newsletter Section -->
      <section class="nav-section newsletter">
        <div class="container">
          <p class="text-lg font-semibold">Noticias</p>
          <AppNewsletter />
        </div>
      </section>

      

      <!-- Customer Support Section -->
      <section class="customer-support">
        <span i-bx-support class="text-2xl text-green-200"></span>
        <p class="font-lg font-semibold text-green-200">1233-7777</p>
        <p class="font-medium">24/7 Support Center</p>
      </section>
    </div>
  </nav>
</template>

<style scoped lang="scss">
  .nav {
    @apply absolute z-10 w-full bg-white;
    @apply lg:static lg:border-b lg:border-green-200;
    @apply translate-x-full transition-transform;
    @apply lg:translate-x-0;

    &.nav--visible {
      @apply translate-x-0;
    }

    .wrapper {
      @apply flex flex-col lg:container lg:flex-row lg:justify-between;
    }

    .nav-section {
      @apply block border-b border-green-200 py-4 lg:border-0;
      .container {
        @apply flex flex-col gap-2;
        @apply lg:w-full lg:flex-row lg:items-center lg:gap-4 lg:p-0;
      }

      ul {
        @apply flex flex-col gap-2 lg:flex-row lg:items-center lg:gap-4;
      }

      span {
        @apply text-2xl;
      }
    }

    .nav-link {
      @apply text-lg hover:text-green-200;
      @apply flex items-center gap-2;

      &--active {
        @apply font-semibold text-green-200;
      }
    }

    .user {
      @apply text-lg font-semibold lg:hidden;
    }

    .contact-us {
      @apply lg:hidden;

      li {
        @apply flex items-center gap-2 text-green-200;
      }
    }

    .newsletter {
      @apply flex flex-col gap-2 lg:hidden;
    }

    .follow-us {
      @apply lg:hidden;

      ul {
        @apply flex-row text-white;
        li {
          @apply rounded-full bg-green-200 p-2;
        }
      }
    }

    .customer-support {
      @apply hidden items-center gap-2 lg:flex;
    }
  }
</style>
