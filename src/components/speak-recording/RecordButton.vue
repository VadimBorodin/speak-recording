<template>
	<Button
		:class="{ recording: isRecording }"
		type="primary"
		@click="toggleRecording"
	>
		{{ isRecording ? 'Stop Recording' : 'Start Recording' }}
	</Button>
</template>

<script>
	import Button from '@/components/buttons/Buttons.vue'
	
	export default {
		components: {
			Button,
		},
		data() {
			return {
				isRecording: false,
			}
		},
		methods: {
			toggleRecording() {
				this.isRecording = !this.isRecording
				this.$emit('recording', this.isRecording)
			},
		},
	}
</script>
