<template>
  <appwrite-list-view collection-name="prompts">
    <template #before-item="{ item }: { item: { favorite: boolean; $id: number } }">
      <button-icon
        :icon="item.favorite ? IconStarFilled : IconStarEmpty"
        transparent
        loading-inline
        :icon-color="item.favorite ? '#ffdf00' : 'white'"
        :loading="loading.is('favorite', item.$id)"
        icon-class="w-10 h-10 -m-1"
        class="w-10 h-10"
        @click="onFavorite(item.$id, item.favorite)"
      />
    </template>
  </appwrite-list-view>
  <appwrite-list-view collection-name="tags" class="mt-10"></appwrite-list-view>
</template>

<script setup lang="ts">
import { provide } from 'vue'
import { databaseId, useDatabasesStore } from '@/stores/database'
import { useLoadingStore } from '@/stores/loading'
import AppwriteListView from '@/views/AppwriteListView.vue'
import IconStarFilled from '@/components/icons/IconStarFilled.vue'
import IconStarEmpty from '@/components/icons/IconStarEmpty.vue'
import ButtonIcon from '@/components/ButtonIcon.vue'
import { Query } from 'appwrite'

const { collections } = useDatabasesStore()
const { databases } = useDatabasesStore()
const loading = useLoadingStore()

provide('appwriteListQuery-' + collections.prompts, [
  Query.orderDesc('favorite'),
  Query.orderAsc('name'),
])

async function onFavorite(documentId, currentFavorite) {
  loading.set('favorite', documentId)
  await databases.updateDocument(databaseId, collections.prompts, documentId, {
    favorite: !currentFavorite,
  })
  loading.finished('favorite', documentId)
}
</script>
