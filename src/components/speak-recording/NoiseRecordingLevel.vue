<template>
	<div class="noise-recording-wrapper">
		<ol>
			<dt>
				<span
					v-for="pick in pickToHuman"
				>
					{{ pick }}
				</span>
			</dt>
		</ol>
		<div class="noise-recording">
			<div
				:style="{ width: meterWidth, backgroundColor: meterColor }"
				class="meter"
			/>
		</div>
	</div>
</template>

<script>
	const PickValues = {
		MIN: 0.25,
		MID: 0.5,
		HIGH: 0.75,
		HIGHEST: 0.99,
	}
	export default {
		props: {
			isRecording: Boolean,
		},
		data() {
			return {
				audioContext: null,
				source: null,
				analyser: null,
				meterWidth: '0%',
				meterColor: '#808080',
				pickToHuman: {
					[PickValues.MIN]: 'Low',
					[PickValues.MID]: 'Medium',
					[PickValues.HIGH]: 'High',
					[PickValues.HIGHEST]: 'Highest',
				}
			}
		},
		methods: {
			async initializeAudio() {
				try {
					const stream = await navigator.mediaDevices.getUserMedia({ audio: true })
					this.audioContext = new (window.AudioContext || window.webkitAudioContext)()
					this.source = this.audioContext.createMediaStreamSource(stream)
					this.analyser = this.audioContext.createAnalyser()
					
					this.source.connect(this.analyser)
					this.analyser.connect(this.audioContext.destination)
					
					this.analyser.fftSize = 256
					const bufferLength = this.analyser.frequencyBinCount
					const dataArray = new Uint8Array(bufferLength)
					
					const updateMeter = () => {
						this.analyser.getByteFrequencyData(dataArray)
						
						const amplitude = dataArray.reduce((acc, value) => acc + value, 0) / bufferLength
						const scaledAmplitude = amplitude / 128
						
						this.meterWidth = `${ scaledAmplitude * 100 }%`
						
						if (scaledAmplitude >= PickValues.MIN && scaledAmplitude < PickValues.MID) {
							this.meterColor = '#4CAF50'
						} else if (scaledAmplitude >= PickValues.MID && scaledAmplitude < PickValues.HIGH) {
							this.meterColor = '#2196F3'
						} else if (scaledAmplitude >= PickValues.HIGH) {
							this.meterColor = '#FF5733'
						} else {
							this.meterColor = '#808080'
						}
						
						requestAnimationFrame(updateMeter)
					}
					
					updateMeter()
				} catch (error) {
					console.error('Error accessing microphone:', error)
				}
			},
			stopAudio() {
				if (this.audioContext) {
					this.source.disconnect()
					this.analyser.disconnect()
				}
			},
		},
		watch: {
			isRecording() {
				this.isRecording
					? this.initializeAudio()
					: this.stopAudio()
			},
		},
	}
</script>

<style scoped>
ol {
	min-width: 100%;
	padding: 0 50px;
	> dt {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
}

.noise-recording-wrapper {
	display: flex;
	flex-direction: column;
	align-items: center;
	width: 100%;
}

.noise-recording {
	width: 100%;
	height: 10px;
	background-color: #f0f0f0;
	border-radius: 8px;
}

.meter {
	height: 100%;
	transition: width 0.1s, background-color 0.1s;
	border-radius: 8px;
}
</style>
