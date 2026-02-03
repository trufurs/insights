<script setup lang="ts">
import { watchEffect } from 'vue'
import { SankeyChartConfig, SankeyFlow } from '../../types/chart.types'
import { ColumnOption, Dimension, DimensionOption, Measure } from '../../types/query.types'
import CollapsibleSection from './CollapsibleSection.vue'
import MeasurePicker from './MeasurePicker.vue'
import DimensionPicker from './DimensionPicker.vue'
import { Plus, X } from 'lucide-vue-next'

const props = defineProps<{
	dimensions: DimensionOption[]
	columnOptions: ColumnOption[]
}>()

const config = defineModel<SankeyChartConfig>({
	required: true,
	default: () => ({
		source_column: {},
		target_column: {},
		value_column: {},
		flows: [],
	}),
})

watchEffect(() => {
	if (!config.value.source_column) {
		config.value.source_column = {} as Dimension
	}
	if (!config.value.target_column) {
		config.value.target_column = {} as Dimension
	}
	if (!config.value.value_column) {
		config.value.value_column = {} as Measure
	}
	if (!config.value.flows) {
		config.value.flows = []
	}
})

function addFlow() {
	if (!config.value.flows) config.value.flows = []
	config.value.flows.push({
		source_column: {} as Dimension,
		target_column: {} as Dimension,
		value_column: {} as Measure,
	})
}

function removeFlow(index: number) {
	config.value.flows?.splice(index, 1)
}
</script>

<template>
	<CollapsibleSection title="Options">
		<div class="flex flex-col gap-4 pt-1">
			<!-- Primary Flow -->
			<div class="space-y-3">
				<div class="text-sm font-medium text-gray-700">Primary Flow</div>
				<DimensionPicker
					label="Source"
					v-model="config.source_column"
					:options="props.dimensions"
				/>
				<DimensionPicker
					label="Target"
					v-model="config.target_column"
					:options="props.dimensions"
				/>
				<MeasurePicker
					label="Value"
					v-model="config.value_column"
					:column-options="props.columnOptions"
				/>
			</div>

			<!-- Additional Flows -->
			<div v-if="config.flows && config.flows.length > 0" class="space-y-4">
				<div
					v-for="(flow, idx) in config.flows"
					:key="idx"
					class="border border-gray-200 rounded-lg p-3 space-y-3"
				>
					<div class="flex items-center justify-between">
						<div class="text-sm font-medium text-gray-700">
							Additional Flow {{ idx + 1 }}
						</div>
						<button
							@click="removeFlow(idx)"
							class="p-1 hover:bg-gray-100 rounded text-gray-500 hover:text-gray-700"
							type="button"
							title="Remove flow"
						>
							<X :size="16" />
						</button>
					</div>
					<DimensionPicker
						label="Source"
						v-model="config.flows[idx].source_column"
						:options="props.dimensions"
					/>
					<DimensionPicker
						label="Target"
						v-model="config.flows[idx].target_column"
						:options="props.dimensions"
					/>
					<MeasurePicker
						label="Value"
						v-model="config.flows[idx].value_column"
						:column-options="props.columnOptions"
					/>
				</div>
			</div>

			<!-- Add Flow Button -->
			<button
				@click="addFlow"
				class="flex items-center justify-center gap-2 p-2 border-2 border-dashed border-gray-300 rounded-lg text-sm text-gray-600 hover:border-gray-400 hover:text-gray-900 hover:bg-gray-50"
				type="button"
			>
				<Plus :size="16" />
				<span>Add Another Flow</span>
			</button>
		</div>
	</CollapsibleSection>
	<CollapsibleSection title="Display">
		<div class="flex flex-col gap-3 pt-1">
			<Toggle v-model="config.showLabels" label="Show Labels" />
			<Toggle v-model="config.showEdgeLabels" label="Show Edge Labels" />
			<Toggle v-model="config.useGreyColor" label="Use Grey Colors" />
			<FormControl
				v-model="config.selectedMode"
				label="Selection Mode"
				type="select"
				:options="[
					{ label: 'Disabled', value: false },
					{ label: 'Single', value: 'single' },
					{ label: 'Multiple', value: 'multiple' },
					{ label: 'Series', value: 'series' },
				]"
				placeholder="Disabled"
			/>
		</div>
	</CollapsibleSection>
	<CollapsibleSection title="Spacing">
		<div class="flex flex-col gap-3 pt-1">
			<FormControl
				v-model="config.nodeGap"
				label="Node Gap"
				type="number"
				min="0"
				max="50"
				placeholder="12"
			/>
			<div class="grid grid-cols-2 gap-2">
				<FormControl
					v-model="config.topPadding"
					label="Top"
					type="number"
					min="0"
					max="200"
					placeholder="0"
				/>
				<FormControl
					v-model="config.bottomPadding"
					label="Bottom"
					type="number"
					min="0"
					max="200"
					placeholder="0"
				/>
			</div>
			<div class="grid grid-cols-2 gap-2">
				<FormControl
					v-model="config.leftPadding"
					label="Left"
					type="number"
					min="0"
					max="200"
					placeholder="0"
				/>
				<FormControl
					v-model="config.rightPadding"
					label="Right"
					type="number"
					min="0"
					max="200"
					placeholder="0"
				/>
			</div>
		</div>
	</CollapsibleSection>
</template>
