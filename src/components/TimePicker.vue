<script setup>
	import {
		ref,
		onMounted,
		onUnmounted
	} from 'vue';

	import {
		debounce
	} from 'lodash';

	import moment from 'moment';

	const fillZero = (el) => `0${el}`.slice(-2);

	const isHouerRef = (e) => (e.currentTarget || e.target).classList.contains('selectionBoxHouer')

	const houerList = Array.from({
		length: 24
	}).map((_, index) => index + 1);

	const minuteList = Array.from({
		length: 60
	}).map((_, index) => index + 1);

	const houer = ref(0);
	const minute = ref(0);
	const houerRef = ref();
	const minuteRef = ref();

	const handleScroll = debounce(function(event) {
		const cur = Math.ceil((event.target.scrollTop + 90) / 30) - 2;
		if (isHouerRef(event)) {
			houer.value = cur;
		} else {
			minute.value = cur;
		}
		scrollTo(cur, event.target);
	}, 100)

	onMounted(() => {
		let now = moment();
		houer.value = now.hour();
		minute.value = now.minute();
		houerRef.value.addEventListener('scroll', handleScroll);
		minuteRef.value.addEventListener('scroll', handleScroll);
	});

	onUnmounted(() => {
		houerRef.value.removeEventListener('scroll', handleScroll);
		minuteRef.value.removeEventListener('scroll', handleScroll);
	})

	const scrollTo = (val, houerRef) => {
		houerRef.scrollTo(0, (val - 1) * 30)
	}

	const handleMouseEnter = () => {
		scrollTo(houer.value, houerRef.value);
		scrollTo(minute.value, minuteRef.value);
	}

	const onClick = (e) => {
		if (e.target.getAttribute('data-index')) {
			if (isHouerRef(e)) {
				houer.value = Number(e.target.getAttribute('data-index'));
				scrollTo(houer.value, houerRef.value);
			} else {
				minute.value = Number(e.target.getAttribute('data-index'));
				scrollTo(minute.value, minuteRef.value);
			}
		}
	}
</script>

<template>
	<div class="time" @mouseenter="handleMouseEnter">
		<span class="timeItem">{{ fillZero(houer) }}</span> : <span class="timeItem">{{ fillZero(minute) }}</span>
		<div class="selectionBox">
			<div class="line line1"></div>
			<div class="line line2"></div>
			<div class="selectionBoxItem selectionBoxHouer" @click="onClick" ref="houerRef">
				<div></div>
				<div></div>
				<div></div>
				<div :data-index="el" :class="{
					active:houer === el
				}" v-for="el in houerList">{{ fillZero(el) }}</div>
				<div></div>
				<div></div>
				<div></div>
			</div>
			<div class="selectionBoxItem selectionBoxMinute" @click="onClick" ref="minuteRef">
				<div></div>
				<div></div>
				<div></div>
				<div :data-index="el" :class="{
					active:minute === el
				}" v-for="el in minuteList">{{ fillZero(el) }}</div>
				<div></div>
				<div></div>
				<div></div>
			</div>
		</div>
	</div>
</template>

<style scoped>
	.time {
		cursor: pointer;
		position: relative;
	}

	.time:hover .selectionBox {
		display: flex;
	}


	.selectionBox {
		position: absolute;
		right: 0;
		bottom: 100%;
		display: none;
		flex-direction: row;
		background-color: #fff;
		border: solid 1px #f2f2f2;
	}

	.line {
		width: 100%;
		height: 1px;
		background-color: #f2f2f2;
		position: absolute;
		top: 90px;
		left: 0;
	}

	.line2 {
		width: 100%;
		height: 1px;
		background-color: #f2f2f2;
		position: absolute;
		top: 120px;
		left: 0;
	}


	.selectionBox .selectionBoxItem {
		width: 50px;
		height: 210px;
		overflow-y: auto;
		padding: 0 12px;
	}

	.selectionBox .selectionBoxItem div {
		height: 30px;
		line-height: 30px;
		text-align: center;
		cursor: pointer;
	}

	.selectionBox .selectionBoxItem .active {
		font-weight: bold;
	}

	/* 	.selectionBox .selectionBoxItem div:hover {
		background-color: #f2f2f2;
	} */

	.time .timeItem {
		background: #f2f2f2;
		padding: 4px;
		border-radius: 4px;
	}
</style>