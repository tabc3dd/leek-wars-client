<template>
	<div v-if="LeekWars.battleRoyale.enabled">
		<div class="br">
			<div class="header">
				<div v-ripple class="title" @click="expanded = !expanded">
					{{ $t('garden.category_battle_royale') }}&nbsp;
					<b class="progress">{{ LeekWars.battleRoyale.progress }} / 10</b>
				</div>
				<i v-ripple class="material-icons" @click="quit">clear</i>
			</div>
			<div v-if="expanded" class="content">
				<loader v-if="LeekWars.battleRoyale.progress == 0" />
				<div v-for="leek in LeekWars.battleRoyale.leeks" :key="leek.id" class="leek">
					<leek-image :leek="leek" :scale="0.4" /><br>
					<div>{{ leek.name }}</div>
					<talent :talent="leek.talent" />
					<div class="level">{{ $t('leek.level_n', [leek.level]) }}</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
	import { LeekWars } from '@/model/leekwars'
	import { Component, Prop, Vue } from 'vue-property-decorator'
	@Component({})
	export default class BattleRoyalePopup extends Vue {
		expanded: boolean = false
		quit(e: Event) {
			LeekWars.battleRoyale.leave()
			e.stopPropagation()
		}
	}
</script>

<style lang="scss" scoped>
	.br {
		width: auto;
		min-width: 250px;
		max-width: 600px;
		display: block;
		margin: 0 15px 0 0 !important;
		box-shadow: 0px 3px 5px -1px rgba(0,0,0,0.2), 0px 5px 8px 0px rgba(0,0,0,0.14), 0px 1px 14px 0px rgba(0,0,0,0.12);
		pointer-events: all;
	}
	.content {
		padding: 10px;
		background: #f2f2f2;
	}
	.leek {
		display: inline-block;
		text-align: center;
		font-size: 15px;
		font-weight: 500;
		margin: 0 3px;
	}
	.talent {
		margin: 2px 0;
	}
	.header {
		background: #2a2a2a;
		color: white;
		display: flex;
		border-top-left-radius: 6px;
		border-top-right-radius: 6px;
		user-select: none;
		cursor: pointer;
		.title {
			padding: 10px;
			font-size: 18px;
			flex: 1;
			overflow: hidden;
			text-overflow: ellipsis;
		}
		i {
			padding: 8px;
		}
	}
</style>