<template>
	<div>
		<div class="page-bar page-header">
			<h1>{{ title }}</h1>
		</div>
		<div class="panel">
			<div class="content first">
				<span id="global-percent">{{ Math.floor(100 * count / total) }}%</span>
				<span id="global-count">{{ count }} / {{ total }}</span>  
				<br>
				<div id="global-bar">
					<div class="bar striked" style="width: {Math.floor(100 * count / total)}%"></div>
				</div>
			</div>
		</div>
		<div v-for="category in categories" v-if="category.id != 6 || progressions[6] != 0" :key="category.id" class="panel">
			<div class="header">
				<h2>{{ $t('category_' + category.name) }}</h2> 
				<div class="right category-bar-wrapper">
					<template v-if="category.id != 6">
						<div class="stats">{{ progressions[category.id] }} / {{ totals[category.id] }}</div>
						<div class="category-bar">
							<div :style="{width: Math.floor(100 * progressions[category.id] / totals[category.id])+ '%'}" class="bar striked"></div>
						</div>
						<div class="stats">{{ Math.floor(100 * progressions[category.id] / totals[category.id]) }}%</div>
					</template>
				</div>
			</div>
			<div class="content">
				<div v-for="trophy in trophies[category.id]" v-if="category.id != 6 || trophy.unlocked" :key="trophy.id" :class="{unlocked: trophy.unlocked, locked: !trophy.unlocked}" class="trophy">
					<img :src="'/image/trophy/big/' + trophy.code + '.png'" class="image">
					<div class="name">{{ trophy.name }}</div>
					<div class="description">{{ trophy.description }}</div>
					<v-tooltip v-if="!trophy.unlocked && trophy.progression != null" :open-delay="0" :close-delay="0" bottom>
						<div slot="activator" class="trophy-bar">
							<div :style="{width: Math.floor(100 * trophy.progression / trophy.threshold) + '%'}" class="bar striked"></div>
						</div>
						{{ trophy.progression }} / {{ trophy.threshold }}
					</v-tooltip>
				</div>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
	import { LeekWars } from '@/model/leekwars'
	import { Component, Vue } from 'vue-property-decorator'

	@Component({ name: 'trophies', i18n: {} })
	export default class Trophies extends Vue {
		trophies: {[key: number]: any} = {}
		progressions: {[key: number]: number} = {}
		totals: {[key: number]: number} = {}
		categories: any = null
		count: number = 0
		total: number = 0
		title: any = null

		created() {
			const farmerID = this.$route.params.id || this.$store.state.farmer.id
			LeekWars.get<any>('trophy/get-farmer-trophies/' + farmerID + '/' + this.$i18n.locale + '/' + this.$store.state.token).then((data) => {
				LeekWars.trophyCategories.forEach((c) => {
					this.trophies[c.id] = []
					this.progressions[c.id] = 0
					this.totals[c.id] = 0
				})
				for (const t in data.data.trophies) {
					if (data.data.trophies.hasOwnProperty(t)) {
						const trophy = data.data.trophies[t]
						this.trophies[trophy.category].push(trophy)
						this.totals[trophy.category]++
						if (trophy.unlocked) {
							this.progressions[trophy.category]++
						}
					}
				}
				for (const category in this.trophies) {
					if (this.trophies.hasOwnProperty(category)) {
						this.trophies[category].sort((a: any, b: any) => {
							return a.index - b.index
						})
					}
				}
				this.categories = LeekWars.trophyCategories
				this.count = data.data.count
				this.total = data.data.total

				if (farmerID === this.$store.state.farmer.id) {
					this.title = this.$t('title_me')
				} else {
					this.title = this.$t('title', [data.data.farmer_name])
				}
				const subtitle = this.count + ' / ' + this.total + ' - ' + Math.floor(100 * this.count / this.total) + '%'
				if (farmerID === this.$store.state.farmer.id) {
					LeekWars.setTitle(this.$t('title_me'), subtitle)
				} else {
					LeekWars.setTitle(this.$t('title_text', data.data.farmer_name), subtitle)
				}
			})
		}
	}
</script>

<style lang="scss" scoped>
	.content:not(.first) {
		padding: 8px;
	}
	#global-percent {
		font-size: 40px;
	}
	#global-count {
		font-size: 30px;
		color: #888;
		float: right;
		line-height: 55px;
	}
	#global-bar {
		height: 12px;
		position: relative;
		background: white;
		border-radius: 3px;
		margin: 5px;
	}
	#global-bar .bar {
		height: 12px;
		background: #008FBB;
		position: absolute;
		border-radius: 3px;
	}
	.category-bar-wrapper {
		width: 70%;
		text-align: right;
		white-space: nowrap;
	}
	.category-bar-wrapper .stats {
		display: inline-block;
		color: white;
		font-size: 16px;
		margin: 0 10px;
	}
	.category-bar {
		display: inline-block;
		height: 12px;
		position: relative;
		background: white;
		border-radius: 3px;
		width: calc(80% - 100px);
		max-width: 300px;
		margin-left: auto;
		margin-top: 12px;
	}
	.category-bar .bar {
		height: 12px;
		background: #30BB00;
		position: absolute;
		border-radius: 3px;
	}
	#app.app .content:nth-child(2) {
		padding: 4px;
	}
	.trophy {
		display: inline-block;
		margin: 5px;
		width: 239px;
		vertical-align: top;
		border-radius: 3px;
		padding: 5px 8px;
	}
	#app.app .trophy {
		width: calc(50% - 25px);
		margin: 3px;
	}
	.trophy.unlocked {
		background: white;
		border-bottom: 3px solid #eee;
	}
	.trophy.locked {
		border-bottom: 3px solid transparent;
		opacity: 0.7;
	}
	.trophy .image {
		width: 55px;
		height: 55px;
		float: left;
		margin-right: 12px;
	}
	#app.app .trophy .image {
		width: 38px;
		height: 38px;
	}
	.trophy.locked .image {
		opacity: 0.4;
	}
	.trophy .name {
		font-size: 16px;
		margin-bottom: 2px;
	}
	.trophy .description {
		color: #888;
		font-size: 14px;
	}
	.trophy .trophy-bar {
		height: 6px;
		position: relative;
		background: white;
		width: 150px;
		margin-left: 67px;
		margin-top: 6px;
	}
	#app.app .trophy .trophy-bar {
		margin-left: 0;
	}
	.trophy .trophy-bar .bar {
		height: 6px;
		position: absolute;
		background: #30BB00;
	}
</style>