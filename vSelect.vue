<template>
	<multi-select :value="localValue"
				  @input="onInput"
				  @search-change="onSearchChange"
				  :placeholder="computedPlaceholder"
				  :track-by="trackBy"
				  :label="label"
				  :show-no-results="showNoResults"
				  :searchable="true"
				  :disabled="disabled"
				  :showLabels="false"
				  :internal-search="internalSearch"
				  :options-limit="optionsLimit"
				  :loading="loading"
				  :custom-label="customLabel"
				  :options="options"
				  @mouseenter.native="hover = true"
				  @mouseleave.native="hover = false">

			<template slot="singleLabel" slot-scope="{ option }">
				<slot name="singleLabel" v-bind:singleLabelOption="option"></slot>
			</template>

			<template slot="option" slot-scope="{ option }">
				<slot name="option" v-bind:option="option"></slot>
			</template>

			<div slot="noResult">Нет данных</div>

			<span slot="placeholder" class="multiselect__placeholder">{{ placeholder }}</span>

			<template slot="caret" slot-scope="{ toggle }">
				<div v-if="checkCanClear" @mousedown.prevent.stop @click.stop="clear()" title="Очистить" class="multiselect__clear">
					<svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="13" height="13" viewBox="0 0 50 50"><g id="surface1"><path style=" " d="M 9.15625 6.3125 L 6.3125 9.15625 L 22.15625 25 L 6.21875 40.96875 L 9.03125 43.78125 L 25 27.84375 L 40.9375 43.78125 L 43.78125 40.9375 L 27.84375 25 L 43.6875 9.15625 L 40.84375 6.3125 L 25 22.15625 Z "></path></g></svg>
				</div>
				<div v-else @mousedown.prevent.stop="toggle()" class="multiselect__select">
				</div>
			</template>
	</multi-select>
</template>

<script>
	import MultiSelect from 'vue-multiselect'
	import Vue from 'Vue'

	export default {
		name: 'vue-select',
		components: {
			MultiSelect,
		},
		props: {
			value: {},
			options: {
				type: Array,
				default: () => [],
			},
			disabled: {
				type: Boolean,
				required: false,
				default: () => false,
			},
			trackBy: {
				type: String,
				required: false,
				default: () => 'Value',
			},
			label: {
				type: String,
				required: false,
				default: () => 'Label',
			},
			returnTracked: {
				type: Boolean,
				required: false,
				default: () => false,
			},
			placeholder: {
				type: String,
				required: false,
				default: () => 'Поиск...',
			},
			optionsLimit: {
				type: Number,
				required: false,
				default: () => 100,
			},
			customLabel: {
				type: Function,
				required: false,
			},
			loading: {
				type: Boolean,
				required: false,
				default: () => false,
			},
			internalSearch: {
				type: Boolean,
				required: false,
				default: () => true,
			},
			showNoResults: {
				type: Boolean,
				required: false,
				default: () => false,
			},
		},
		data () {
			return {
                hover: false,
			}
		},
		methods: {
			onInput (option) {
				this.$emit('input', option != null
					? this.returnTracked
						? option[this.trackBy]
						: option
					: null,
				)
			},
			onSearchChange (searchQuery) {
				this.$emit('search-change', searchQuery)
			},
			compare (o, v) {
				if (v instanceof Object && o instanceof Object) {
					return v[this.trackBy] === o[this.trackBy]
				} else if (o instanceof Object && !(v instanceof Object)) {
					return v === o[this.trackBy]
				} else if (v instanceof Object && !(o instanceof Object)) {
					return o === v[this.trackBy]
				} else {
					return o === v
				}
			},
			clear() {
				this.$emit('input', null)
			},
		},
		computed: {
			localValue() {
				return this.options.find(o => this.compare(o, this.value))
			},
			computedPlaceholder() {
				if (this.localValue) {
					if (this.localValue[this.label]) return this.localValue[this.label]
					if (this.customLabel) return this.customLabel(this.value)
				}
				return this.placeholder
			},
			checkCanClear() {
				return this.localValue && this.hover
			},
		},
	}
</script>
