<template>
  <LayoutHeader>
    <template #left-header>
      <Breadcrumbs :items="[{ label: __('Products'), route: { name: 'Products' } }]" />
    </template>
    <template #right-header>
      <Button
        :label="__('Refresh')"
        iconLeft="lucide-refresh-cw"
        :loading="products.list.loading"
        @click="products.reload()"
      />
    </template>
  </LayoutHeader>
  <div class="flex min-h-0 flex-1 flex-col gap-3 px-3 py-3 sm:px-5">
    <ErrorMessage v-if="products.list.error" :message="products.list.error" />
    <div
      v-if="products.list.loading && !products.data"
      class="flex flex-1 items-center justify-center gap-2 text-base text-ink-gray-5"
      role="status"
    >
      <Spinner class="size-4" />
      {{ __('Loading products...') }}
    </div>
    <ListView
      v-else-if="products.data"
      class="min-h-0 flex-1"
      :columns="columns"
      :rows="products.data"
      row-key="name"
      :options="{
        selectable: false,
        showTooltip: true,
        resizeColumn: false,
        emptyState: {
          title: __('No products yet'),
          description: __('Products will appear here once they are added.'),
        },
      }"
    />
    <div v-if="products.data?.length && products.hasNextPage" class="flex justify-center">
      <Button
        :label="__('Load more')"
        :loading="products.list.loading"
        @click="products.next()"
      />
    </div>
  </div>
</template>

<script setup>
import LayoutHeader from '@/components/LayoutHeader.vue'
import { Breadcrumbs, Button, ErrorMessage, Spinner, createListResource } from 'frappe-ui'
import { ListView } from 'frappe-ui/experimental'

const columns = [
  { label: __('Product Name'), key: 'product_name' },
  { label: __('ID'), key: 'name' },
]

const products = createListResource({
  doctype: 'CRM Product',
  fields: ['name', 'product_name'],
  orderBy: 'name asc',
  pageLength: 20,
  auto: true,
})
</script>
