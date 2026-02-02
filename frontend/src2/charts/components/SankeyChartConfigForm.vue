<script setup lang="ts">
import { watchEffect } from 'vue'
import { SankeyChartConfig } from '../../types/chart.types'
import { ColumnOption, Dimension, DimensionOption, Measure } from '../../types/query.types'
import CollapsibleSection from './CollapsibleSection.vue'
import MeasurePicker from './MeasurePicker.vue'
import DimensionPicker from './DimensionPicker.vue'

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
})
</script>

<template>
	<CollapsibleSection title="Options">
		<div class="flex flex-col gap-3 pt-1">
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
	</CollapsibleSection>
	<CollapsibleSection title="Display">
		<div class="flex flex-col gap-3 pt-1">
			<Toggle v-model="config.showLabels" label="Show Labels" />
			<Toggle v-model="config.useGreyColor" label="Use Grey Colors" />
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
					placeholder="Auto (5%)"
				/>
				<FormControl
					v-model="config.bottomPadding"
					label="Bottom"
					type="number"
					min="0"
					max="200"
					placeholder="Auto (5%)"
				/>
			</div>
			<div class="grid grid-cols-2 gap-2">
				<FormControl
					v-model="config.leftPadding"
					label="Left"
					type="number"
					min="0"
					max="200"
					placeholder="Auto (5%)"
				/>
				<FormControl
					v-model="config.rightPadding"
					label="Right"
					type="number"
					min="0"
					max="200"
					placeholder="Auto (5%)"
				/>
			</div>
		</div>
	</CollapsibleSection>
</template>
