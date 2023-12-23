<template>
	<div>
		<sdPageHeader title="Vue developer job task " class="ninjadash-page-header-main"/>
		<Main class="speak-recoding">
			<RecordButton @recording="triggerRecordingEvent"/>
			<TimerDisplay :time="time"/>
			<NoiseRecordingLevel
				v-show="isRecording"
				:is-recording="isRecording"
			/>
		</Main>
	</div>
</template>

<script lang="ts">
	import { Main } from '../styled'
	import RecordButton from '@/components/speak-recording/RecordButton.vue'
	import TimerDisplay from '@/components/speak-recording/TimerDisplay.vue'
	import NoiseRecordingLevel from '@/components/speak-recording/NoiseRecordingLevel.vue'
	
	export default {
		components: {
			Main,
			RecordButton,
			TimerDisplay,
			NoiseRecordingLevel,
		},
		data() {
			return {
				isRecording: false as boolean,
				time: 0 as number,
				interval: null as any,
			}
		},
		methods: {
			triggerRecordingEvent(isRecording: boolean) {
				this.isRecording = isRecording
				this.updateTimer()
			},
			updateTimer() {
				if (this.isRecording) {
					this.interval =  setInterval(() => {
						this.time ++
					}, 1000)
				} else {
					this.time = 0
					clearInterval(this.interval)
				}
			},
		},
	}
</script>
<style scoped>
.speak-recoding {
	display: flex;
	gap: 1rem;
}
</style>
