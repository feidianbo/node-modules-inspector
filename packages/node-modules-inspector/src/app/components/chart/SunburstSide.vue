<script setup lang="ts">
import type { GraphBaseOptions } from 'nanovis'
import type { PackageNode } from 'node-modules-tools'
import type { ChartNode } from '../../types/chart'
import { colorToCssBackground } from 'nanovis'

defineProps<{
  selected?: ChartNode
  options: GraphBaseOptions<PackageNode | undefined>
}>()

const emit = defineEmits<{
  (e: 'select', node: ChartNode | null): void
}>()
</script>

<template>
  <div flex="~ col gap-4">
    <ChartNavBreadcrumb
      border="b base" py2
      :selected="selected"
      :options="options"
      @select="emit('select', $event)"
    />
    <div v-if="selected" grid="~ cols-[250px_1fr] gap-1">
      <template v-for="child of selected.children" :key="child.id">
        <button
          ws-nowrap text-nowrap text-left overflow-hidden text-ellipsis text-sm
          hover="bg-active" rounded px2
          @click="emit('select', child)"
        >
          <span v-if="child.meta && child.meta === selected?.meta" text-primary>(self)</span>
          <DisplayPackageSpec v-else-if="child.meta" :pkg="child.meta" />
          <span v-else>{{ child.id }}</span>
        </button>

        <button
          relative flex="~ gap-1 items-center"
          hover="bg-active" rounded
          @click="emit('select', child)"
        >
          <div
            h-5 rounded shadow border="~ base"
            :style="{
              background: colorToCssBackground(options.getColor?.(child) || '#000'),
              width: `${child.size / selected.size * 100}%`,
            }"
          />
          <DisplayFileSizeBadge text-xs :bytes="child.size" :total="selected.size" :percent-ratio="3" />
          <div
            v-if="child.children.length > 0"
            v-tooltip="`${child.children.length} dependencies`"
            :title="`${child.children.length} dependencies`"
            text-xs op-fade
          >
            ({{ child.children.length }})
          </div>
        </button>
      </template>
    </div>
  </div>
</template>
