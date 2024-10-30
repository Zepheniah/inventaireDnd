<template>
  <div class="inventory-container overflow-y-auto">
    <ItemDrawer
      v-if="showDrawer"
      @close="showDrawer = false"
      @addItem="addItem"
      :rarityLevels="rarityLevels"
      :categories="categories"
      :colorOptions="colorOptions"
    />

    <!-- Inventaire principal avec onglets et grille -->
    <InventoryGrid
      :inventory="filteredInventory"
      :categories="categories"
      :selectedCategory="selectedCategory"
      :colorOptions="colorOptions"
      :selectedColor="selectedColor"
      @selectColorFilter="selectColorFilter"
      @selectItem="openDetailsDrawer"
      @openDrawer="showDrawer = true"
      @selectCategory="selectCategory"
    />

    <ItemDetails
      v-if="showDetailsDrawer"
      :item="selectedItem"
      :show="showDetailsDrawer"
      :categories="categories"
      :colorOptions="colorOptions"
      :rarityLevels="rarityLevels"
      @close="showDetailsDrawer = false"
      @updateItem="updateItem"
      @deleteItem="deleteItem"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue'
import InventoryGrid from './InventoryGrid.vue'
import ItemDrawer from './ItemDrawer.vue'
import ItemDetails from './ItemDetails.vue'
import { v4 as uuidv4 } from 'uuid'

type Item = {
  id: string
  name: string
  description: string
  category: 'Equipable' | 'Consommable' | 'Divers'
  color: string // Propriété pour la couleur
  rarity: { level: string; color: string }
}


export default defineComponent({
  name: 'Inventory',
  components: { InventoryGrid, ItemDrawer, ItemDetails },
  setup() {
    const categories = ref(['Tout', 'Equipable', 'Consommable', 'Divers'])
    const selectedCategory = ref<string>('Tout')
    const selectedColor = ref<string | null>(null) // Couleur de filtre sélectionnée
    const showDrawer = ref(false)
    const showDetailsDrawer = ref(false)
    const selectedItem = ref<Item | null>(null)
      
    const colorOptions = ref([
      '#7D2F2F', // Rouge Brique
      '#4A5A31', // Vert Olive
      '#2C3E50', // Bleu Nuit
      '#8C6D1F', // Jaune Vieilli
      '#4B4E54', // Gris Fumé
      '#5D3A6B', // Violet Mystique
    ]);

    const rarityLevels = ref([
  { level: 'Commun', color: '#A0AEC0' },
  { level: 'Peu commun', color: '#68D391' },
  { level: 'Rare', color: '#3182CE' },
  { level: 'Très rare', color: '#9F7AEA' },
  { level: 'Légendaire', color: '#D69E2E' }
  ]);




    const inventory = ref<Item[]>([])

    // Charger l'inventaire depuis le Local Storage
    onMounted(() => {
      const storedInventory = localStorage.getItem('inventory')
      if (storedInventory) {
        inventory.value = JSON.parse(storedInventory)
      }
    })


    const filteredInventory = computed(() => {
      return inventory.value.filter(item => {
        const matchesCategory = selectedCategory.value === 'Tout' || item.category === selectedCategory.value;
        const matchesColor = selectedColor.value ? item.color === selectedColor.value : true;
        return matchesCategory && matchesColor;
      });
    });


    function saveInventoryToLocalStorage() {
      localStorage.setItem('inventory', JSON.stringify(inventory.value))
    }

    function openDetailsDrawer(item: Item) {
      selectedItem.value = item
      showDetailsDrawer.value = true
    }

    function selectCategory(category: string) {
      selectedCategory.value = category
    }

    function selectColorFilter(color: string | null) {
      // Désactive le filtre si la couleur est déjà sélectionnée
      selectedColor.value = selectedColor.value === color ? null : color
    }

    function addItem(newItem: Item) {
      newItem.id = uuidv4.apply(null).toString()
      inventory.value.push(newItem)
      saveInventoryToLocalStorage()
      showDrawer.value = false
    }

    function updateItem(updatedItem: Item) {
      const index = inventory.value.findIndex(
        item => item.id === updatedItem.id,
      )
      if (index !== -1) {
        inventory.value[index] = { ...updatedItem }
        saveInventoryToLocalStorage()
      }
      selectedItem.value = updatedItem
    }

    function deleteItem(itemToDelete: Item) {
      inventory.value = inventory.value.filter(
        item => item.id !== itemToDelete.id,
      )
      saveInventoryToLocalStorage()
      showDetailsDrawer.value = false
    }

    return {
      categories,
      selectedCategory,
      selectedColor,
      colorOptions,
      rarityLevels,
      showDrawer,
      showDetailsDrawer,
      selectedItem,
      inventory,
      filteredInventory,
      openDetailsDrawer,
      selectCategory,
      selectColorFilter,
      addItem,
      updateItem,
      deleteItem,
    }
  },
})
</script>
<style scoped></style>
