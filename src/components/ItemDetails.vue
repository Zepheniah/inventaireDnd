<template>
  <div
    v-if="show"
    class="fixed inset-0 sm:inset-y-0 sm:right-0 w-full sm:w-1/2 lg:w-1/3 h-full bg-yellow-900 text-yellow-200 p-6 overflow-y-auto shadow-lg border-l-4 border-yellow-800"
  >
    <div class="drawer-content space-y-4">
      <button
        class="bg-red-700 text-yellow-200 font-bold py-2 px-4 rounded-lg border border-yellow-800 hover:bg-red-600 hover:border-yellow-600 transition"
        @click="$emit('close')"
      >
        Fermer
      </button>
      <h2 class="text-2xl font-bold mb-4">Détails de l'Objet</h2>

      <div v-if="item" class="space-y-4">
        <!-- Bouton de suppression avec confirmation -->
        <button
          class="delete-btn bg-red-500 hover:bg-red-600 text-white py-2 px-4 rounded mb-4"
          @click="confirmDelete"
        >
          Supprimer
        </button>

        <!-- Message de confirmation de suppression -->
        <div
          v-if="showConfirmDelete"
          class="confirmation-dialog bg-gray-800 p-4 rounded space-y-2 text-center"
        >
          <p>Es-tu sûr de vouloir supprimer cet objet ?</p>
          <div class="flex justify-center gap-4">
            <button
              class="confirm-btn bg-red-500 hover:bg-red-600 text-white py-2 px-4 rounded"
              @click="deleteItem"
            >
              Oui, supprimer
            </button>
            <button
              class="cancel-btn bg-gray-600 hover:bg-gray-700 text-white py-2 px-4 rounded"
              @click="showConfirmDelete = false"
            >
              Annuler
            </button>
          </div>
        </div>

        <!-- Détails de l'objet -->
        <div class="item-details space-y-4" v-if="!showConfirmDelete">
          <div>
            <label class="block text-sm font-bold mb-1">Nom :</label>
            <input
              v-model="editableItem.name"
              @input="updateItem"
              class="w-full bg-gray-800 text-yellow-200 rounded p-2 border border-yellow-700"
            />
          </div>
          <div>
            <label class="block text-sm font-semibold mb-1"
              >Description :</label
            >
            <textarea
              v-model="editableItem.description"
              @input="updateItem"
              rows="4"
              class="w-full bg-gray-800 text-white rounded p-2"
            ></textarea>
          </div>
          <div>
    <label class="block text-sm font-semibold mb-1">Rareté :</label>
    <select v-model="editableItem.rarity" @change="updateItem" class="w-full bg-gray-800 text-yellow-200 rounded p-2">
      <option disabled value="">Sélectionner une rareté</option>
      <option v-for="rarity in rarityLevels" :key="rarity.level" :value="rarity.level">{{ rarity.level }}</option>
    </select>
  </div>
          <div>
            <label class="block text-sm font-semibold mb-1">Catégorie :</label>
            <select
              v-model="editableItem.category"
              @change="updateItem"
              class="w-full bg-gray-800 text-white rounded p-2"
            >
              <option
                v-for="category in categories"
                :key="category"
                :value="category"
              >
                {{ category }}
              </option>
            </select>
          </div>

          <div>
            <label class="block text-sm font-semibold mb-1">Quantité :</label>
            <input
              type="number"
              v-model="editableItem.quantity"
              min="1"
              @input="updateItem"
              class="w-full bg-gray-800 text-white rounded p-2"
            />
          </div>

          <div>
            <label class="block text-sm font-semibold mb-1">Couleur :</label>
            <div class="color-options flex items-center gap-2">
              <span
                v-for="color in colorOptions"
                :key="color"
                :style="{ backgroundColor: color }"
                class="color-circle w-6 h-6 rounded-full cursor-pointer"
                @click="setColor(color)"
                :class="{ 'ring-2 ring-white': editableItem.color === color }"
              ></span>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <p>Sélectionnez un objet pour voir les détails.</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, type PropType } from 'vue'

type Item = {
  name: string
  description: string
  category: string
  color: string
  quantity: number
}

export default defineComponent({
  name: 'ItemDetails',
  props: {
    item: {
      type: Object as PropType<Item | null>,
      required: false,
    },
    show: {
      type: Boolean,
      required: true,
    },
    categories: {
      type: Array as PropType<string[]>,
      required: true,
    },
    colorOptions: {
      type: Array as PropType<string[]>,
      required: true,
    },
    rarityLevels: {
    type: Array as PropType<{ level: string; color: string }[]>,
    required: true
  }
  },
  emits: ['close', 'updateItem', 'deleteItem'],
  setup(props, { emit }) {
    const editableItem = ref({ ...props.item })
    const showConfirmDelete = ref(false)

    const updateItem = () => {
      emit('updateItem', { ...editableItem.value })
    }

    const setColor = (color: string) => {
      editableItem.value.color = color
      updateItem()
    }

    watch(
      () => props.item,
      newItem => {
        if (newItem) {
          editableItem.value = { ...newItem }
        }
      },
      { immediate: true },
    )

    function confirmDelete() {
      showConfirmDelete.value = true
    }

    function deleteItem() {
      if (editableItem.value) {
        emit('deleteItem', editableItem.value)
        showConfirmDelete.value = false
      }
    }

    return {
      editableItem,
      colorOptions: props.colorOptions,
      showConfirmDelete,
      updateItem,
      setColor,
      confirmDelete,
      deleteItem,
    }
  },
})
</script>

<style scoped>
.color-circle {
  transition: transform 0.2s;
}

.color-circle.ring-2 {
  transform: scale(1.1);
}
</style>
