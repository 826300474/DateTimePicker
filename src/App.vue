<script setup>
	import TimePicker from '../src/components/TimePicker.vue';
	import {
		ref,
		computed,
		reactive,
		watch
	} from 'vue';

	import moment from 'moment';

	const icon_Map = [{
		icon: '<<',
		actionKey: 'preYear',
	}, {
		icon: '<',
		actionKey: 'preMonth',
	}, {
		icon: '>',
		actionKey: 'nextMonth',
	}, {
		icon: '>>',
		actionKey: 'nextYear',
	}]

	const curtime = ref(moment().startOf('day'));

	const state = reactive({
		value: [],
	})

	const onIconClick = (e) => {
		switch (e.target.getAttribute('data-key')) {
			case 'preYear':
				curtime.value = moment(curtime.value).add(-1, 'year');
				break;
			case 'preMonth':
				curtime.value = moment(curtime.value).add(-1, 'month');
				break;
			case 'nextMonth':
				curtime.value = moment(curtime.value).add(1, 'month');
				break;
			case 'nextYear':
				curtime.value = moment(curtime.value).add(1, 'year');
				break;
			default:
				break;
		}
	}

	const onDayClick = (e) => {
		const curTime = dayList.value[e.target.getAttribute('data-index')].date;
		if (e.target.classList.contains('active')) {
			const index = state.value.findIndex(el => el.isSame(curTime));
			state.value.splice(index, 1);
			return;
		}
		if (state.value.length === 2) {
			return;
		}
		if (state.value.length === 1) {
			if (curTime.isBefore(state.value[0])) {
				state.value.unshift(curTime);
			} else {
				state.value.push(curTime);
			}
			return;
		}
		state.value.push(curTime);
	}

	const dayList = computed(() => {
		let firstDayOfMonth = moment(curtime.value).clone().startOf('month');
		let lastDayOfMonth = moment(curtime.value).clone().endOf('month');
		let daysInMonth = lastDayOfMonth.diff(firstDayOfMonth, 'days') + 1;
		const res = [...(new Array(firstDayOfMonth.day()).fill({})), {
			value: 1,
			date: firstDayOfMonth
		}];
		for (var i = 1; i < daysInMonth; i++) {
			res.push({
				value: i + 1,
				date: moment(curtime.value).clone().startOf('month').add(i, 'day')
			});
		}
		return res;
	});
</script>

<template>
	<div class="box">
		<div class="top" @click="onIconClick">
			<span class="icon" :data-key="icon_Map[0].actionKey">{{ icon_Map[0].icon }}</span>
			<span class="icon" :data-key="icon_Map[1].actionKey">{{ icon_Map[1].icon }}</span>
			<span>{{ curtime.format('YYYY年 MM月') }}</span>
			<span class="icon" :data-key="icon_Map[2].actionKey">{{ icon_Map[2].icon }}</span>
			<span class="icon" :data-key="icon_Map[3].actionKey">{{ icon_Map[3].icon }}</span>
		</div>
		<div class="mid">
			<div class="item title">
				<span>S</span>
				<span>M</span>
				<span>T</span>
				<span>W</span>
				<span>T</span>
				<span>F</span>
				<span>S</span>
			</div>
			<div class="item" @click="onDayClick">
				<span class="day" :data-index="i" :class="{ 
						current : el.date && moment().startOf('day').isSame(el.date),
						include : el.date && state.value.length === 2 && el.date.isBetween(state.value[0], state.value[1], null, '[]'),
						includeFirst : el.date && state.value.length === 2 && state.value[0].isSame(el.date),
						includeLast : el.date && state.value.length === 2 && state.value[1].isSame(el.date),
						active:el.date && state.value.findIndex(v => v.isSame(el.date)) > -1
					}" v-for="(el,i) in dayList" :key="el.date">{{ el.value ? el.value:null }}</span>
			</div>
		</div>
		<div class="bottom">
			<div class="li">
				开始时间
				<TimePicker />
			</div>
			<div class="li">
				结束时间
				<TimePicker />
			</div>
		</div>
	</div>
</template>

<style>
	.box {
		width: 280px;
		padding: 20px;
	}

	.top {
		font-weight: bold;
		display: flex;
		justify-content: space-between;
	}

	.mid {
		margin-top: 20px;
	}

	.icon {
		cursor: pointer;
	}

	.item {
		display: flex;
		flex-wrap: wrap;
		margin-top: 12px;
		width: 280px;
	}

	.item span {
		text-align: center;
		flex: 0 0 auto;
		width: 38px;
		height: 38px;
		border: solid 1px rgba(0, 0, 0, 0);
		line-height: 40px;
		box-sizing: border-box;
		margin-bottom: 10px;
		border-radius: 50%;
		position: relative;
	}

	.item span:hover {
		color: purple;
	}

	.title {
		color: #808080;
	}


	.item .day {
		cursor: pointer;
	}

	.item .current {
		border: solid 1px purple;
	}

	.item .active {
		background-color: purple;
		color: #fff;
	}

	.item .active:hover {
		color: #fff;
	}



	.item .include::after {
		content: "";
		position: absolute;
		top: 0;
		left: 0;
		width: 38px;
		height: 38px;
		background-color: purple;
		opacity: 0.1;
		z-index: -1;
	}

	.item .includeFirst:after {
		content: "";
		position: absolute;
		top: 0;
		left: 19px;
		width: 19px;
		height: 38px;
		background-color: purple;
		opacity: 0.1;
		z-index: -1;
	}

	.item .includeLast:after {
		content: "";
		position: absolute;
		top: 0;
		left: 0;
		width: 19px;
		height: 38px;
		background-color: purple;
		opacity: 0.1;
		z-index: -1;
	}

	.bottom {
		margin-top: 12px;
		padding-top: 12px;
		border-top: solid 1px #f2f2f2;
	}

	.bottom .li {
		display: flex;
		justify-content: space-between;
		margin-bottom: 20px;
	}
</style>