<script lang="ts" setup>
import useStates from "./storage/states";
const { resetInputGroupUUIDKeys } = useStates();

const config = useRuntimeConfig();
const title = config.siteTitle

function clearError(error) {
  error.value = null;
  resetInputGroupUUIDKeys();
}
</script>

<template>
  <Head>
    <Title>{{ title }}</Title>
    <Meta name="description" content="Calculator for foreign currency and commodity units" />
    <Meta name="keywords" content="currency, usd, dollar, eur, euro, gold, silver" />

    <!-- <Meta property="og:url" content="https://sharkcoder.com/html/Meta" />
    <Meta property="og:title" content="Döviz ve Altın" />
    <Meta property="og:type" content="" />
    <Meta property="og:image" content="https://sharkcoder.com/public/img/content/tags_main.jpeg" />
    <Meta property="og:site_name" content="Shark Coder" />
    <Meta property="og:description " content="Calculator for foreign currency and commodity units" /> -->
    <Meta property="og:locale" content="en_US" />
    <Meta property="og:locale:alternate" content="tr_TR" />
  </Head>

  <NuxtLayout>
    <NuxtErrorBoundary>
      <NuxtPage />

      <template #error="{ error }">
        <article class="m-4">
          <section class="text-2xl max-sm:text-xl font-bold m-2 font-['Quicksand'] text-[#073058]">
            {{ error }}
          </section>
          <button class="resultButton" @click="clearError(error)">Go back</button>
        </article>
      </template>
    </NuxtErrorBoundary>
  </NuxtLayout>
</template>
