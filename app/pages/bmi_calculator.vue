<script setup>
import { ref } from 'vue'
import { mealPlans } from '~/data/mealPlans'

const sex = ref('female')
const age = ref(21)
const height = ref(170)
const heightUnit = ref('cm')
const weight = ref()
const bmi = ref(null)
const feet = ref(5)
const inches = ref(10)
const bmiCategory = ref('');
const weightUnit = ref('kg');
const selectedGoal = ref('');
const tdee = ref(null);


const calculateBMI = () => {
    console.log(weightUnit.value)
    let weight_data = weight.value;
    let heightInMeters = heightUnit.value === 'cm' ? height.value / 100 : (feet.value * 0.3048) + (inches.value * 0.0254)

    if (weightUnit.value === 'lbs') {
        if (weight.value > 0) {
            weight_data = weight.value / 2.20462
        }
    }
    bmi.value = weight_data / (heightInMeters * heightInMeters)
    // if (heightUnit.value === 'cm') {
    //     if (height.value > 0) {
    //         const heightInMeters = height.value / 100
    //         bmi.value = weight_data / (heightInMeters * heightInMeters)
    //     }
    // } else {
    //     if (feet.value > 0 && inches.value > 0) {
    //         const heightInMeters = (feet.value * 0.3048) + (inches.value * 0.0254)
    //         bmi.value = weight_data / (heightInMeters * heightInMeters)
    //     }
    // }

    if (bmi.value) {
        if (bmi.value < 18.5) {
            bmiCategory.value = 'Underweight'
        } else if (bmi.value < 24.9) {
            bmiCategory.value = 'Normal weight'
        } else if (bmi.value < 29.9) {
            bmiCategory.value = 'Overweight'
        } else {
            bmiCategory.value = 'Obesity'
        }
    }
    calculateTDEE(weight_data, heightInMeters*100, age.value, sex.value)
}
const calculateTDEE = (weightKg, heightCm, age, sex) => {
    if(bmi.value < 30) {
        tdee.value = sex === 'male'
      ? 66.5 + 13.75 * weightKg + 5.003 * heightCm - 6.755 * age
      : 655.1 + 9.563 * weightKg + 1.850 * heightCm - 4.676 * age
    } else if(bmi.value >= 30) {
        tdee.value = sex === 'male'
      ? 3.4 * weightKg + 15.3 * heightCm - 6.8 * age - 961
      : 2.4 * weightKg + 9.3 * heightCm - 4.7 * age - 65
    }
    // console.log(bmr)
    // const activityMultiplier = 1.4 // Light activity
    tdee.value = Math.round(tdee.value)
}
const goals = [
  { label: 'Gain weight', icon: '📈' },
  { label: 'Lose weight', icon: '📉' },
  { label: 'Maintain weight', icon: '👀' },
  { label: 'Build muscle', icon: '💪' },
]

const goal = computed(() => {
  if (bmiCategory.value === 'Underweight') return 'gain'
  if (bmiCategory.value === 'Overweight') return 'lose'
  if (bmiCategory.value === 'Normal weight') return 'maintain'
  if (bmiCategory.value === 'Obesity') return 'lose'
})
const dailyCalories = computed(() => {
    console.log(tdee.value)
  if (goal.value === 'gain') return tdee.value + 500
  if (goal.value === 'lose') return tdee.value - 500
  return tdee.value
})
const meals = computed(() => mealPlans[goal.value])


const activeClass =
    'bg-blue-500 text-white px-4 py-2 rounded-full shadow-md font-semibold'
const inactiveClass =
    'bg-white text-gray-700 border border-gray-300 px-4 py-2 rounded-full hover:bg-gray-100'
</script>
<template>
    <div class="max-w-4xl mx-auto mt-10 p-6 bg-white rounded-lg shadow-md">
        <h1 class="text-3xl font-bold text-center mb-2">Body Mass Index (BMI) Calculator</h1>
        <p class="text-center text-gray-500 mb-8">For accurate calculation, we need some basic info about you</p>
  
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
            <!-- 1. Sex -->
            <div class="bg-gray-50 p-4 rounded-lg shadow-sm mb-6">
                <div class="mb-2 font-semibold">1. What is your sex?</div>
                <div class="flex space-x-4">
                    <button
                        :class="sex === 'male' ? activeClass : inactiveClass"
                        @click="sex = 'male'"
                    >
                        👨 Male
                    </button>
                    <button
                        :class="sex === 'female' ? activeClass : inactiveClass"
                        @click="sex = 'female'"
                    >
                        👩 Female
                    </button>
                </div>
            </div>
  
            <!-- 2. Age -->
            <div class="bg-gray-50 p-4 rounded-lg shadow-sm mb-6">
                <div class="mb-2 font-semibold">2. How old are you?</div>
                <input
                    v-model.number="age"
                    type="number"
                    min="0"
                    class="w-24 border-b-2 border-gray-300 focus:outline-none focus:border-blue-500 text-2xl text-center"
                />
                <span class="ml-2 text-gray-500">Years</span>
            </div>
  
            <!-- 3. Height -->
            <div class="bg-gray-50 p-4 rounded-lg shadow-sm">
                <div class="mb-2 font-semibold">3. How tall are you?</div>
                <input
                    v-if="heightUnit === 'cm'"
                    v-model.number="height"
                    type="number"
                    min="0"
                    class="w-24 border-b-2 border-gray-300 focus:outline-none focus:border-blue-500 text-2xl text-center"
                />
                <span class="ml-2 text-gray-500" v-if="heightUnit === 'cm'">{{ heightUnit }}</span>
                <div v-if="heightUnit === 'ft/inch'">
                    <input
                        v-model.number="feet"
                        type="number"
                        min="0"
                        class="w-24 border-b-2 border-gray-300 focus:outline-none focus:border-blue-500 text-2xl text-center"
                    />
                    <span class="ml-2 text-gray-500">ft</span>
                    <input
                        v-model.number="inches"
                        type="number"
                        min="0"
                        class="w-24 border-b-2 border-gray-300 focus:outline-none focus:border-blue-500 text-2xl text-center"
                    />
                    <span class="ml-2 text-gray-500">in</span>
                </div>
                
                <div class="flex gap-2 mt-6">
                    <button class="px-2 py-1 rounded-full border border-gray-300" :class="heightUnit === 'cm' ? activeClass : inactiveClass" @click="heightUnit = 'cm'">cm</button>
                    <button class="px-2 py-1 rounded-full border border-gray-300" :class="heightUnit === 'ft/inch' ? activeClass : inactiveClass" @click="heightUnit = 'ft/inch'">ft/inch</button>
                </div>
            </div>
  
            <!-- 4. Weight -->
            <div class="bg-gray-50 p-4 rounded-lg shadow-sm">
                <div class="mb-2 font-semibold">4. How much do you weigh?</div>
                <input
                    v-model.number="weight"
                    type="number"
                    :min="weightUnit === 'kg' ? '0' : '100'"
                    :placeholder="weightUnit === 'kg' ? '0' : '100'"
                    class="w-24 border-b-2 border-gray-300 focus:outline-none focus:border-blue-500 text-2xl text-center"
                />
                <span class="ml-2 text-gray-500">{{ weightUnit }}</span>
                <div class="flex gap-2 mt-6">
                    <button class="px-2 py-1 rounded-full border border-gray-300" :class="weightUnit === 'kg' ? activeClass : inactiveClass" @click="weightUnit = 'kg'">kg</button>
                    <button class="px-2 py-1 rounded-full border border-gray-300" :class="weightUnit === 'lbs' ? activeClass : inactiveClass" @click="weightUnit = 'lbs'">lbs</button>
                </div>
            </div>
        </div>
  
        <div class="text-center">
            <button
                @click="calculateBMI"
                class="bg-gradient-to-r from-pink-500 to-orange-400 text-white font-bold py-3 px-6 rounded-full shadow-lg hover:opacity-90 transition"
            >
                Calculate your BMI
            </button>
  
            <!-- <div v-if="bmi" class="mt-4 text-xl font-semibold text-blue-600">
                Your BMI is: {{ bmi.toFixed(1) }}
            </div>
            <div v-if="bmiCategory" class="mt-4 text-xl font-semibold text-blue-600">
                Your BMI Category is: {{ bmiCategory }}
            </div> -->
            <!-- <div v-if="bmi && bmiCategory" class="max-w-2xl mx-auto space-y-6 mt-10">

                <div class="bg-green-100 rounded-xl p-6 shadow">
                    <div class="flex justify-between items-center mb-2">
                        <h2 class="text-xl font-semibold text-gray-700">Your BMI:</h2>
                        <span class="text-2xl font-bold text-green-700">{{ bmi.toFixed(1) }}</span>
                    </div>
                    <p class="text-gray-800 font-medium">
                        According to your BMI, you might be <span class="font-semibold" :class="bmiCategory === 'Normal weight' ? 'text-green-600' : 'text-red-600'">{{bmiCategory}}</span>.
                    </p>
                    <hr class="my-3" />
                    <p class="text-sm text-gray-600">
                        <strong class="text-gray-800">Our recommendation:</strong><br />
                        Try to gain some weight in a healthy manner until you weigh between 
                        <span class="font-semibold">125.4 lb</span> and <span class="font-semibold">160.3 lb</span>.
                        <br />And we can help you do just that!
                    </p>
                </div>

                <div class="flex items-center space-x-4">
                    <img src="https://img.icons8.com/emoji/48/apple-emoji.png" alt="mascot" class="w-10 h-10" />
                    <div class="bg-white shadow px-4 py-2 rounded-full text-gray-800 font-semibold">
                        What’s your main goal?
                    </div>
                </div>

                <div class="bg-teal-50 rounded-xl p-4">
                    <p class="text-center text-teal-700 font-medium mb-4">Recommended for you</p>

                    <div class="space-y-3">
                        <div
                            v-for="(goal, i) in goals"
                            :key="i"
                            class="flex items-center justify-between p-4 bg-white rounded-lg shadow-sm cursor-pointer hover:bg-gray-50"
                            :class="{ 'ring-2 ring-teal-400': selectedGoal === goal.label }"
                            @click="selectedGoal = goal.label"
                        >
                            <span class="text-lg">
                                <span class="mr-2">{{ goal.icon }}</span>{{ goal.label }}
                            </span>
                        </div>
                    </div>
                </div>
            </div> -->
            <div class="max-w-3xl mx-auto py-6 space-y-6" v-if="bmiCategory">
                <div class="text-center">
                    <h2 class="text-3xl font-bold">Your BMI is {{ bmi.toFixed(1) }}</h2>
                    <p class="text-xl mt-2 text-gray-700">
                        Category: <span class="font-semibold capitalize" :class="bmiCategory === 'Normal weight' ? 'text-green-600' : 'text-red-600'">{{ bmiCategory }}</span>
                    </p>
                    <p class="mt-1 text-lg text-blue-600">
                        Recommended Daily Calorie Intake: <strong>{{ dailyCalories }} kcal</strong>
                    </p>
                </div>

                <div class="bg-white shadow rounded p-6">
                    <h3 class="text-2xl font-semibold mb-4">Weekly Meal Plan ({{ goal }})</h3>
                    <div class="space-y-3">
                        <div v-for="(dayMeals, day) in meals" :key="day" class="border-b pb-2">
                            <p class="font-bold">{{ day }}</p>
                            <ul class="ml-4 list-none">
                                <li v-for="meal in dayMeals" :key="meal">{{ meal }}</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>
  
  