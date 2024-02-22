<script setup>
import { ref, onMounted, watch } from 'vue'
import { db } from './data/guitars'
import Header from './components/Header.vue'
import Guitar from './components/Guitar.vue'
import Footer from './components/Footer.vue'

// functions
const saveLocalStorage = () => {
  localStorage.setItem('car', JSON.stringify(car.value))
}

// states
const guitars = ref([])
const car = ref([])
const guitar = ref({})

// listener
watch(car, () => {
  saveLocalStorage()
}, { deep: true })

// init states
onMounted(() => {
  guitars.value = db
  guitar.value = db[3]

  const carStoraged = localStorage.getItem('car')
  if (carStoraged) car.value = JSON.parse(carStoraged)
})

// events
const addToCar = (guitar) => {
  const carHasGuitar = car.value.findIndex(product => product.id === guitar.id)
  if (carHasGuitar >= 0) car.value[carHasGuitar].cantidad++
  else {
    guitar.cantidad = 1
    car.value.push(guitar)
  }
}

const cleanCar = () => {
  car.value = []
}

const quantityDecrement = (id) => {
  const index = car.value.findIndex(product => product.id === id)
  if (car.value[index].cantidad <= 1) return
  car.value[index].cantidad--
}

const quantityIncrement = (id) => {
  const index = car.value.findIndex(product => product.id === id)
  if (car.value[index].cantidad >= 5) return
  car.value[index].cantidad++
}

const deleteProduct = (id) => {
  car.value = car.value.filter(product => product.id !== id)
}
</script>

<template>
  <Header :car="car" :guitar="guitar" @add-to-car="addToCar" @clean-car="cleanCar" @quantity-decrement="quantityDecrement"
    @quantity-increment="quantityIncrement" @delete-product="deleteProduct" />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitar v-for="guitar in guitars" :guitar="guitar" @add-to-car="addToCar" />
    </div>
  </main>
  <Footer />
</template>
