<template>
	<!-- <transition name="modal"> -->
		<div :class="[OPEN ? 'modal-in' : 'modal-out', 'edit-tags']" @keydown.esc="closeModalTag()" tabindex="0">
			<h3 class="h3 underline mb-2">Edit tags</h3>
			<form @submit.prevent>

				<label for="addTag" class="label-add-tag">
					<i class="mdi mdi-plus" v-if="!focusInput"></i>
					<i class="mdi mdi-close" v-else @click.prevent="closeIcon()"></i>
					<input type="text" v-model.trim="inputAddTag" placeholder="Create new tag" class="input-add-tag" @focus="focusInput = true">
					<i class="mdi mdi-check icon-check" @click.prevent="createNewTag()" :class="{'opacity-1': focusInput}"></i>
				</label>

				<ul class="mt-2">
					<li v-for="tag in GET_TAGS" :key="tag.id">
						<label :for="tag.id">
							<!-- <i class="fas fa-trash" @click="deleteTag(tag.id)">	 -->
							<i class="mdi mdi-delete icon-delete" @click="deleteTag(tag.id)"></i>
							<input type="text" :value="tag.name" :id="tag.id" class="input-edit-tag" @focus="focusEdit(tag.id, $event)" @blur="inputValue"/>
							<!-- BITNO! Nije htelo da apdejtuje tag sa v-model tj sa  two way binding, mora sa :value! -->
							<i :class="tag.id == currKey ? 'mdi mdi-check' : 'mdi mdi-pencil'" @click="updateTag(tag, tag.id)"></i>
						</label>
					</li>
				</ul>
			</form>

			<button class="mt-3 btn__underline" @click.prevent="closeModalTag()">Done</button>
		</div>
	<!-- </transition> -->
</template>

<script>
export default {
	name: 'edit-tags',

	data() {
		return {
			inputAddTag: '',
			currKey: '',
			input: '',
			focusInput: false,
			open: true
			// focusEdit: ''
		}
	},

	computed: {
		GET_TAGS () {
			return this.$store.getters.GET_TAGS
		},

		OPEN_EDIT_TAGS: {
			get() {
				return this.$store.getters['ui/GET_OPEN_EDIT_TAGS']
			},
			set(newVal) {
				// setTimeout(() => {
					this.$store.commit('ui/SET_OPEN_EDIT_TAGS', newVal)
				// }, 1000);
			}
		},

		OPEN: {
			get() {
				return this.$store.getters['ui/OPEN']
			},
			set(newVal) {
				this.$store.getters['ui/SET_OPEN', newVal]
			}
		}
	},

	methods: {

		createNewTag() {
			let arrTags = this.GET_TAGS.filter(tag => tag.name == this.inputAddTag) // ako ne andje nista onda je [], ako nadje onda ima nesto
			console.log(arrTags);
			if(this.inputAddTag != '' && arrTags.length == 0) {
				this.$store.dispatch('ADD_TAG', this.inputAddTag)
				this.inputAddTag = ''

			} else if (this.inputAddTag != '' && arrTags.length > 0) {
				this.$store.dispatch('ui/ACT_NOTIFICATION', {
					display: true,
					text: 'Tag already exist. Try different.',
					alertClass: 'warning'
				})

			} else if (this.inputAddTag == '') {
				this.$store.dispatch('ui/ACT_NOTIFICATION', {
					display: true,
					text: 'You must enter some tag name',
					alertClass: 'warning'
				})
			}
		},

		deleteTag(idTag) {
			console.log('DELETE TAG', idTag);
			if(confirm('We’ll delete this label and remove it from all of your Keep notes. Your notes won’t be deleted.')) {
				this.$store.dispatch('DELETE_TAG', idTag) // We’ll delete this label and remove it from all of your Keep notes. Your notes won’t be deleted.
			}
			
		},

		updateTag(tag, key) {
			if(this.currKey == key && tag.name != this.input) {
				console.log(this.input);
				this.$store.dispatch('UPDATE_TAG', {
					idTag: tag.id,
					newTagName: this.input
				})
			}
		},

		focusEdit(currKey, e) {
			this.currKey = currKey
			// this.input = e.target.value
			// console.log(this.input);
		},

		inputValue(e) {
			// console.log(e.target.value);
			this.input = e.target.value
		},
		
		closeIcon() {
			this.inputAddTag = ''
			this.focusInput = false
		},

		closeModalTag() {
			this.OPEN = false
			setTimeout(() => {
				this.OPEN_EDIT_TAGS = false
			}, 500);
		}
	},

	
}
</script>