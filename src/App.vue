<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import SHA256 from 'crypto-js/sha256'

const propane = ref([])
const butane = ref([])
const logged_in = ref(false)

const gas_type = ref('')
const gas_size = ref(0)
const gas_icon = ref('')

//const todos_asc = computed(() => todos.value.sort((a, b) => {
//  return b.created_at - a.created_at
//}))

const incrementCount = (gas) => {
  if (gas.type === 'Propane' || gas.type === 'Patio-Propane') {
    const index = propane.value.findIndex(item => item === gas);
    propane.value[index].count++;
  } else if (gas.type === 'Butane') {
    const index = butane.value.findIndex(item => item === gas);
    butane.value[index].count++;
  }
}

const decrementCount = (gas) => {
  if (gas.type === 'Propane' || gas.type === 'Patio-Propane') {
    const index = propane.value.findIndex(item => item === gas);
    if (propane.value[index].count > 0) {
      propane.value[index].count--;
    }
  } else if (gas.type === 'Butane') {
    const index = butane.value.findIndex(item => item === gas);
    if (butane.value[index].count > 0) {
      butane.value[index].count--;
    }
  }
}

const addNewGas = () => {
  if (gas_type.value === 'Propane' || gas_type.value === 'Patio-Propane') {
    propane.value.push({
      type: gas_type.value,
      size: gas_size.value,
      image: gas_icon.value,
      count: 0
    });
  } else if (gas_type.value === 'Butane') {
    butane.value.push({
      type: gas_type.value,
      size: gas_size.value,
      image: gas_icon.value,
      count: 0
    });
  }
  gas_type.value = ''
  gas_size.value = 0
  gas_icon.value = ''
}

const removeGas = (gas) => {
  if (confirm('Are you sure you want to delete this gas?') !== true) {
    return
  }
  if (gas.type === 'Propane' || gas.type === 'Patio-Propane') {
    const index = propane.value.findIndex(item => item === gas);
    propane.value.splice(index, 1);
  } else if (gas.type === 'Butane') {
    const index = butane.value.findIndex(item => item === gas);
    butane.value.splice(index, 1);
  }
}

watch(propane, (newVal) => {
  localStorage.setItem('propane', JSON.stringify(newVal))
}, { deep: true })

watch(butane, (newVal) => {
  localStorage.setItem('butane', JSON.stringify(newVal))
}, { deep: true })

watch(logged_in, (newVal) => {
  localStorage.setItem('logged_in', newVal)
})

onMounted(() => {
  if (sessionStorage.getItem('logged_in') === 'true') {
    logged_in.value = true
  }
  if (!logged_in.value) {
    while (logged_in.value === false) {
      if (stringToHashConversion(prompt('Enter password')) !== "fb38db63d3fd45f1f804a00c6d96b4b57f7d82fc013ee324044d500f6c3669ba"){
        alert('Incorrect password')
      } else {
        sessionStorage.setItem('logged_in', true)
        logged_in.value = true
      }
    }
  }
  if (logged_in.value) {
    propane.value = JSON.parse(localStorage.getItem('propane')) || []
    butane.value = JSON.parse(localStorage.getItem('butane')) || []
  }
})

function stringToHashConversion(string) {
  return SHA256(string).toString();
}

</script>

<template>
  <header class="app-header">
    <h1>Gas Inventory</h1>
  </header>S
  <main class="app">
    <!--<h1>Welcome!</h1>-->
    <section class="gas-lists">
      <section class="gas-type">
        <h2>Propane</h2>
        <div class="propane">
          <div v-for="gas in propane">
            <div class="gas-container">
              <label class="gas-label">{{ gas.size }} {{ gas.type }}</label>
              <div class="image-container">
                <img class="gas-image" :src=gas.image alt="Gas icon">
              </div>
              <div class="button-container"> 
                <button @click=incrementCount(gas)>+</button>
                <span class="gas-count">{{ gas.count }}</span>
                <button @click=decrementCount(gas)>-</button>
                <button class="delete" @click=removeGas(gas)>Delete</button>
              </div> 
            </div>
          </div>
        </div>
      </section>

      <section class="gas-type">
        <h2>Butane</h2>
        <div class="butane">
          <div v-for="gas in butane">
            <div class="gas-container">
              <label class="gas-label">{{ gas.size }} {{ gas.type }}</label>
              <div class="image-container">
                <img class="gas-image" :src=gas.image alt="Gas icon">
              </div>
              <div class="button-container"> 
                <button @click=incrementCount(gas)>+</button>
                <span class="gas-count">{{ gas.count }}</span>
                <button @click=decrementCount(gas)>-</button>
                <button class="delete" @click=removeGas(gas)>Delete</button>
              </div> 
            </div>
          </div>
        </div>
      </section>
    </section>

    <div class="line"></div>

    <section class="add-gas">
        <form @submit.prevent="addNewGas">
          <label for="gas_type" class="select-label">Gas Type
            <select v-model="gas_type" required>
              <option value="Patio-Propane">Patio Propane</option>
              <option value="Propane">Propane</option>
              <option value="Butane">Butane</option>
            </select>
          </label>

          <label for="gas_size" class="select-label">Gas Size
            <select v-model="gas_size" required>
              <option value="5kg">5kg</option>
              <option value="6kg">6kg</option>
              <option value="7kg">7kg</option>
              <option value="13kg">13kg</option>
              <option value="15kg">15kg</option>
            </select>
          </label>
          
          <label for="gas_icon" class="select-label">Gas Icon
            <select v-model="gas_icon" required>
              <option value=src\assets\cylinder_patio_5kg_1.jpg>5kg Patio</option>
              <option value=src\assets\cylinder_patio_13kg_1.jpg>13kg Patio</option>
              <option value=src\assets\cylinder_propane_6kg-.jpg>6kg Propane</option>
              <option value=src\assets\cylinder_propane_13kg_1_3.jpg>13kg Propane</option>
              <option value=src\assets\cylinder_butane_7kg_1.jpg>7kg Butane</option>
              <option value=src\assets\cylinder_butane_15kg_1.jpg>15kg Butane</option>
            </select>
          </label>
          <input type="submit" value="Add New Gas">
        </form>
    </section>
  </main>
</template>