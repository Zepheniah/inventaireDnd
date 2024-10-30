<template>
  <div
    class="fixed inset-0 sm:inset-y-0 sm:right-0 w-full sm:w-1/2 lg:w-1/3 h-full bg-yellow-900 text-yellow-200 p-6 overflow-y-auto shadow-lg border-l-4 border-yellow-800"
  >
    <button
      class="close-drawer-btn text-white bg-red-500 hover:bg-red-600 rounded py-2 px-4"
      @click="$emit('close')"
    >
      Fermer
    </button>
    <h3 class="text-xl font-semibold mb-4">Créer un Nouvel Objet</h3>
    <form @submit.prevent="submitForm" class="space-y-4">
      <label class="block">
        Nom de l'objet :
        <input
          v-model="newItem.name"
          required
          class="mt-1 block w-full bg-gray-800 text-white rounded p-2"
        />
      </label>
      <label class="block">
        Description :
        <textarea
          v-model="newItem.description"
          required
          class="mt-1 block w-full bg-gray-800 text-white rounded p-2 h-24"
        ></textarea>
      </label>
      <label class="block">
  Rareté :
  <select v-model="newItem.rarity" required class="mt-1 block w-full bg-gray-800 text-white rounded p-2">
    <option disabled value="">Sélectionner la rareté</option>
    <option v-for="rarity in rarityLevels" :key="rarity.level" :value="rarity.level">{{ rarity.level }}</option>
  </select>
</label>


      <label class="block">
        Catégorie :
        <select
          v-model="newItem.category"
          required
          class="mt-1 block w-full bg-gray-800 text-white rounded p-2"
        >
          <option disabled value="">Sélectionner une catégorie</option>
          <option
            v-for="category in categories"
            :key="category"
            :value="category"
          >
            {{ category }}
          </option>
        </select>
      </label>
      <label class="block">
        Quantité :
        <input
          type="number"
          v-model="newItem.quantity"
          min="1"
          required
          class="mt-1 block w-full bg-gray-800 text-white rounded p-2"
        />
      </label>
      <button
        type="submit"
        class="bg-green-500 hover:bg-green-600 w-full py-2 rounded"
      >
        Ajouter
      </button>
      <label class="block text-sm font-semibold mb-1">Couleur :</label>
      <div class="color-options flex items-center gap-2">
        <span
          v-for="color in colorOptions"
          :key="color"
          :style="{ backgroundColor: color }"
          class="color-circle"
          @click="newItem.color = color"
          :class="{ active: newItem.color === color }"
        ></span>
      </div>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, type PropType, ref } from 'vue'

type Item = {
  id: string;
  name: string;
  description: string;
  category: string;
  color: string;
  quantity: number;
  rarity: string; // Ajout du champ rareté
};


export default defineComponent({
  name: 'ItemDrawer',
  props: {
    categories: {
      type: Array as PropType<string[]>,
      required: true,
    },
    colorOptions: {
      type: Array as PropType<string[]>,
    },
  rarityLevels: {
    type: Array as PropType<{ level: string; color: string }[]>,
    required: true,
  }
  },
  emits: ['close', 'addItem'],
  setup(_, { emit }) {
    const newItem = ref<Item>({
      id: '',
      name: '',
      description: '',
      category: '',
      color: '',
      quantity: 1,
      rarity: ''
    })

    const submitForm = () => {
      emit('addItem', { ...newItem.value })
      newItem.value = {
        id: '',
        name: '',
        description: '',
        category: '',
        color: '',
        quantity: 1,
        rarity: ''
      } // Reset form
    }

    return {
      newItem,
      submitForm,
    }
  },
})
</script>

<style scoped>
.color-circle {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  cursor: pointer;
  border: 2px solid transparent;
  transition: transform 0.2s;
}

.color-circle.active {
  border-color: #fff;
  transform: scale(1.1);
}
</style>
