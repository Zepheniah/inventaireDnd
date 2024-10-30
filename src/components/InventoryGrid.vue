<template>
  <div
    class="main-inventory w-full max-w-screen-lg mx-auto p-4 sm:p-6 bg-gray-800 text-white min-h-screen"
  >
    <h2 class="text-2xl font-bold mb-4">Inventaire</h2>
    <button
      @click="$emit('openDrawer')"
      class="bg-red-800 text-yellow-200 font-bold py-2 px-4 rounded-lg border border-yellow-600 hover:bg-red-700 hover:border-yellow-500 transition"
    >
      Créer un Nouvel Objet
    </button>

    <div class="color-filter flex flex-wrap items-center gap-4 mb-4">
      <label>Filtrer par couleur :</label>
      <div class="flex gap-2">
        <span
          v-for="color in colorOptions"
          :key="color"
          :style="{ backgroundColor: color }"
          class="w-6 h-6 rounded-full cursor-pointer"
          @click="$emit('selectColorFilter', color)"
          :class="{ 'ring-2 ring-white': selectedColor === color }"
        ></span>
      </div>
      <button
        class="bg-gray-700 text-white py-1 px-3 rounded"
        @click="$emit('selectColorFilter', null)"
      >
        Réinitialiser
      </button>
    </div>
    <div class="rarity-filter mb-4">
  <label class="block text-sm font-semibold mb-2">Filtrer par Rareté :</label>
  <div class="flex gap-2">
    <button
      v-for="rarity in rarityLevels"
      :key="rarity.level"
      :style="{ backgroundColor: rarity.color }"
      @click="selectRarityFilter(rarity.level)"
      class="py-1 px-3 rounded cursor-pointer text-white"
      :class="{ 'ring-2 ring-yellow-200': selectedRarity === rarity.level }"
    >
      {{ rarity.level }}
    </button>
    <button
      @click="selectRarityFilter(null)"
      class="bg-gray-700 text-white py-1 px-3 rounded"
    >
      Réinitialiser le filtre
    </button>
  </div>
</div>

    <div
      class="tabs flex gap-2 overflow-x-auto border-b border-gray-700 mb-4 pb-2"
    >
      <button
        v-for="category in categories"
        :key="category"
        :class="[
          'tab-item px-4 py-2 text-sm font-medium rounded-lg border border-yellow-600',
          {
            'bg-yellow-800 text-white': category === selectedCategory,
            'bg-gray-800 text-gray-400 hover:bg-yellow-700':
              category !== selectedCategory,
          },
        ]"
        @click="$emit('selectCategory', category)"
      >
        {{ category }}
      </button>
    </div>

    <div
      class="grid grid-cols-3 sm:grid-cols-4 md:grid-cols-5 lg:grid-cols-6 gap-3"
    >
    <div 
  v-for="item in inventory" 
  :key="item.id" 
  class="item rounded-lg cursor-pointer transition aspect-square flex items-center justify-center text-center p-2 sm:p-4"
  :style="{ backgroundColor: item.color || '#4B4E54', borderColor: getColorByRarity(item.rarity), borderWidth: '2px', borderStyle: 'solid' }"
  @click="$emit('selectItem', item)"
>
  <p class="truncate">{{ item.name }}</p>
</div>

    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, PropType, ref } from 'vue'

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
  name: 'InventoryGrid',
  props: {
    inventory: {
      type: Array as PropType<{ id: string; name: string; rarity: string; color: string }[]>,
      required: true
    },
    categories: {
      type: Array,
      required: true,
    },
    selectedCategory: {
      type: String,
      required: true,
    },
    colorOptions: {
      type: Array,
      required: true,
    },
    selectedColor: {
      type: String,
      required: false,
    },
  },
  emits: ['selectCategory', 'selectItem', 'selectColorFilter', 'openDrawer'],
  setup(props) {
    const rarityLevels = [
      { level: 'Commun', color: '#A0AEC0' },
      { level: 'Peu commun', color: '#68D391' },
      { level: 'Rare', color: '#3182CE' },
      { level: 'Très rare', color: '#9F7AEA' },
      { level: 'Légendaire', color: '#D69E2E' }
    ];

    const selectedRarity = ref<string | null>(null);

    function selectRarityFilter(rarity: string | null) {
      selectedRarity.value = selectedRarity.level === rarity.valueOf.level ? null : rarity.value.level;
    }

    // Filtrer l'inventaire par rareté
    const filteredInventory = computed(() => {
  return props.inventory.values.filter(item => {
    const matchesRarity = !selectedRarity.value || item.rarity.level === selectedRarity.value;
    return matchesRarity;
  });
});

    // Fonction pour obtenir la couleur en fonction de la rareté
    const getColorByRarity = (rarity: string) => {
      const rarityData = rarityLevels.find(r => r.level === rarity);
      return rarityData ? rarityData.color : '#A0AEC0'; // Couleur par défaut pour "Commun"
    };
    return {
      rarityLevels,
      getColorByRarity,
      selectedRarity,
      selectRarityFilter,
      filteredInventory
    };
  },
})
</script>

<style scoped></style>
