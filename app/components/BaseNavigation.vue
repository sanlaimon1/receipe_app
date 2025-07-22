<script setup lang="ts">
import { ref } from 'vue'

const isMenuOpen = ref(false)

const toggleMenu = () => {
    isMenuOpen.value = !isMenuOpen.value
}
</script>
<template>
    <header class="py-4 border-b">
        <nav class="flex container items-center justify-between">
            <NuxtLink to="/" class="flex gap-1 items-center">
                <NuxtImg width="56px" densities="x1" src="/icon-green.png" format="webp" alt="" />
                <span class="text-3xl font-bold">Nuxtcipes</span>
            </NuxtLink>

            <button @click="toggleMenu" class="lg:hidden text-2xl">
                ☰
            </button>

            <ul :class="[
                'lg:flex gap-6 text-xl font-bold capitalize',
                isMenuOpen ? 'block' : 'hidden',
                'absolute lg:static bg-white top-[70px] right-4 p-4 rounded shadow-md lg:shadow-none lg:p-0 lg:bg-transparent',
            ]">
                <li>
                    <NuxtLink to="/" class="block py-2 px-4 text-[18px]" @click="isMenuOpen = false">
                        Home
                    </NuxtLink>
                </li>
                <li>
                    <NuxtLink to="/about" class="block py-2 px-4 text-[18px]" @click="isMenuOpen = false">
                        About
                    </NuxtLink>
                </li>
            </ul>
        </nav>
    </header>
</template>