<script setup lang="ts">
import { ref, watchEffect } from 'vue'
import { type RecipeResponse } from '../../types/types'
import RecipeCard from '../components/ReceipeCard.vue'
// import SignupForm from '../components/SignupForm.vue'

const recipesPerPage = 12
const currentPage = ref(1)
const totalRecipes = ref(0)
const { data, error, refresh } = await useFetch<RecipeResponse>(() =>
    `https://dummyjson.com/recipes?limit=${recipesPerPage}&skip=${(currentPage.value - 1) * recipesPerPage}`
)

watchEffect(() => {
    if (data.value) {
        totalRecipes.value = data.value.total
    }
})

function goToPage(page: number) {
    currentPage.value = page
    refresh()
}

useSeoMeta({
    title: 'Nuxtcipes',
    description: 'Recipes for you to cook!',
    ogTitle: 'Nuxtcipes',
    ogDescription: 'Recipes for you to cook!',
    ogImage: '/nuxt-course-hero.png',
    ogUrl: `http://localhost:3000`,
    twitterTitle: 'Nuxtcipes',
    twitterDescription: 'Recipes for you to cook!',
    twitterImage: '/nuxt-course-hero.png',
    twitterCard: 'summary',
})

</script>
<template>
    <main>
        <section class="bg-[#f1f1f1] px-[50px]">
            <div class="container flex flex-col lg:flex-row items-center py-20 gap-10">
                <div class="flex-1 order-2 lg:order-1 text-center lg:text-left">
                    <h1 class="text-4xl lg:text-6xl font-extrabold mb-6 text-balance">
                        Master the Kitchen with Ease: Unleash Your Inner Chef Today!
                    </h1>
                    <p class="text-xl lg:text-2xl mb-8 text-balance">
                        Discover recipes helping you to find the easiest way to cook.
                    </p>
                    <UButton to="#recipes" label="Browse Recipes" />
                </div>
                <div class="flex-1 order-1 lg:order-2">
                    <NuxtImg sizes="xs:100vw sm:667px" src="/nuxt-course-hero.png" format="webp" densities="x1"
                        alt="" />
                </div>
            </div>
        </section>
        <section id="recipes" class="py-20 container px-[50px]">
            <h2 class="text-3xl lg:text-5xl mb-2">Discover, Create, Share</h2>
            <p class="text-lg lg:text-xl mb-8">Check out our most popular recipes!</p>
            <div v-if="!error" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-x-4 gap-y-8">
                <RecipeCard v-for="recipe in data?.recipes" :recipe="recipe" />
            </div>
            <p v-else class="text-xl">Opps, something went wrong. Please try again later</p>
            <div class="flex justify-center mt-10" v-if="totalRecipes > recipesPerPage">
                <button v-for="page in Math.ceil(totalRecipes / recipesPerPage)" :key="page" @click="goToPage(page)"
                    :class="[
                        'mx-1 px-4 py-2 rounded border',
                        currentPage === page ? 'bg-green-500 text-white' : 'bg-white text-gray-700'
                    ]">
                    {{ page }}
                </button>
            </div>
        </section>
        <section class="bg-[#f1f1f1] py-20">
            <SignupForm />
        </section>
    </main>
</template>