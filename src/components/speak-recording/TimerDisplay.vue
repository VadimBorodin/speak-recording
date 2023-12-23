<template>
	<div class="timer">{{ formattedTime }}</div>
</template>

<script>
	export default {
		props: {
			time: Number,
		},
		computed: {
			formattedTime() {
				const hours = Math.floor(this.time / 3600)
				const minutes = Math.floor((this.time % 3600) / 60)
				const seconds = this.time % 60
				
				const formattedHours = hours > 0 ? `${hours}:` : ''
				const formattedMinutes = `${minutes < 10 && hours > 0 ? '0' : ''}${minutes}:`
				const formattedSeconds = `${seconds < 10 ? '0' : ''}${seconds}`
				
				return `${formattedHours}${formattedMinutes}${formattedSeconds}`
			},
		},
	};
</script>
<style >
.timer {
	font-size: 1.7rem;
	font-weight: 700;
}
</style>
