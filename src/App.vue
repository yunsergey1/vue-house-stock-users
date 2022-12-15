<template>
	<section class="wrap">

		<div class="title">
			<h1>Жилфонд</h1>
			<span>Пользователь</span>
		</div>

		<div class="dashboard">

			<div class="sidebar">
				<h2 class="sidebar-title-1">Поиск сотрудников</h2>

				<input
					type="text"
					v-bind:placeholder="placeholderString"
					v-model="query"
					@input="go()"
				>

				<h2 class="sidebar-title-2">Результаты</h2>
				
				<template v-if="newUsers != ''">
					<div
						class="card-mini"
						:class="{activate: number == idx && isActivate}"
						v-for="(user, idx) in newUsers"
						:key="idx"
						@click="toggleShow(idx), activateCard(idx);"
					>
						<div class="photo"><img src="https://placeimg.com/70/70/animals" alt="Photo"></div>
						<div class="inner">
							<div class="username">{{ user.username }}</div>
							<div class="email">{{ user.email }}</div>
						</div>
					</div>
				</template>

				<p v-else>начните поиск</p>
			</div>

			<div class="article">
				<div class="inner" v-if="show == true">
					<div class="photo"><img src="https://placeimg.com/424/286/animals" alt=""></div>
					<div class="deskription">
						<div class="name">{{ newUsers[number].name }}</div>
						<div class="email"><span>email:</span> {{ newUsers[number].email }}</div>
						<div class="phone"><span>phone:</span> {{ newUsers[number].phone }}</div>

						<h3>О себе:</h3>
						<p class="text1">
							Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
						</p>
					</div>

				</div>
				<p class="text0" v-else>Выберите сотрудника, чтобы посмотреть его профиль</p>

			</div>
		</div>

	</section>
</template>

<script>
export default {
	data() {
		return {
			placeholderString: 'Введите id или имя',
			query: '',
			newQuery: '',
			newUsers: [],
			show: false,
			number: 0,
			isActivate: false,
		}
	},
	methods: {
		go() {
			this.query = this.query.charAt(0).toUpperCase() + this.query.toLowerCase().slice(1)
			console.log(this.query)
			if (this.query == '') {
				this.newUsers = []
				this.show = false
				// console.log(this.newUsers)
			}
			else if (this.query.match(/^[1-9]/)) {
				console.log('Поиск начинается с цифры')
				fetch(`https://jsonplaceholder.typicode.com/users?id=${this.query}`)
					.then(response => response.json())
					.then(json => this.newUsers = json)
			} 
			else if (this.query.match(/^[a-zA-Z_-]/)) {
				console.log('Поиск начинается с буквы')
				if (this.query.match(/,/)) {
					let listQuery = []
					listQuery = this.query.split(',')
					for (let i=0; i<listQuery.length; i++) {
						if (i > 0) {
							// listQuery[i].trim()
							listQuery[i] = listQuery[i].trim().charAt(0).toUpperCase() + listQuery[i].toLowerCase().slice(2)
							console.log(listQuery[i])
							if(listQuery[i].match(/[\w]/)) {
								this.newQuery = `${this.newQuery.trim()}&username=${listQuery[i].trim()}`
							}
						} else {
							this.newQuery = `username=${listQuery[0].trim()}`
						}
					}
					fetch(`https://jsonplaceholder.typicode.com/users?${this.newQuery}`)
						.then(response => response.json())
						// .then(json => console.log(json))
						.then(json => {
							if (json != '') {
								this.newUsers = json
							}
						})
				} else {
					this.newQuery = `username=${this.query.trim()}`
					fetch(`https://jsonplaceholder.typicode.com/users?${this.newQuery}`)
						.then(response => response.json())
						.then(json => {
							if (json != '') {
								this.newUsers = json
							}
						})
				}
				// console.log(this.newUsers)
			} 
		},
		toggleShow(idx) {
			this.show = !this.show;
			this.number = idx;
			
		},
		activateCard(idx) {
			if (idx === this.number) {
				this.isActivate = !this.isActivate
			}
		}
	}
}
</script>

<style scoped lang="scss">

	.wrap {
		padding: 46px 50px;
		max-width: 1366px;
		margin: 0 auto;

		.title {
			display: flex;
			flex-direction: row;
			justify-content: space-between;
			align-items: center;
			padding-bottom: 30px;

			h1 {
				font-style: normal;
				font-weight: 700;
				font-size: 32px;
				line-height: 39px;
				color: #E31F24;
			}
			span {
				font-weight: 400;
				font-size: 16px;
				line-height: 20px;
				color: #333333;
			}
		}

		.dashboard {
			// min-height: calc(100vh - 69px - 46px - 58px);
			min-height: 575px;
			width: 100%;
			overflow: scroll;
			background: #FDFDFD;
			box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
			border-radius: 10px;
			display: grid;
			grid-template-columns: 290px 1fr;

			.sidebar {
				padding: 27px 30px 27px 20px;
				overflow: scroll;

				h2 {
					font-style: normal;
					font-weight: 600;
					font-size: 16px;
					line-height: 140%;
					color: #333333;
				}
				.sidebar-title-1 {
					padding-bottom: 20px;
				}
				.sidebar-title-2 {
					padding-bottom: 10px;
				}
				input {
					display: flex;
					flex-direction: row;
					align-items: center;
					padding: 16px;
					gap: 16px;
					width: 100%;
					height: 46px;
					background: #FFFFFF;
					border: 1.5px solid #E9ECEF;
					border-radius: 8px;
					margin-bottom: 22px;
				}

				p {
					font-style: normal;
					font-weight: 400;
					font-size: 14px;
					line-height: 17px;
					color: #76787D;
				}

				.card-mini {
					background: #FFFFFF;
					height: 70px;
					box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
					border: 1px solid #ffffff;
					border-radius: 10px;
					display: flex;
					flex-direction: row;
					margin-bottom: 18px;
					overflow: hidden;
					cursor: pointer;
					transition: 0.4s;

					.photo {
						background: #FFFFFF;
						border-right: 1px solid #E0E0E0;
						overflow: hidden;
						min-width: 70px;
					}

					.inner {
						padding: 15px;
						overflow: hidden;

						.username {
							font-style: normal;
							font-weight: 600;
							font-size: 14px;
							line-height: 17px;
							color: #333333;
						}

						.email {
							font-style: normal;
							font-weight: 400;
							font-size: 14px;
							line-height: 17px;
							color: #76787D;
							white-space: nowrap; /* Текст не переносится */
							overflow: hidden; /* Обрезаем всё за пределами блока */
							text-overflow: ellipsis;
						}
					}
				}
				.card-mini:hover {
					border: 1px solid #E0E0E0;
					background: #E0E0E0;
				}
				.card-mini.activate {
					border: 1px solid #E0E0E0;
					background: #E0E0E0;
				}
			}

			.article {
				background: #FFFFFF;
				border-radius: 0 10px 10px 0;
				border-left: 1px solid #DEDEDD;
				position: relative;
				padding: 30px 22px;

				.inner {
					width: 100%;
					display: flex;
					flex-direction: row;
					flex-wrap: wrap;
					justify-content: center;
					align-content: flex-start;
					gap: 62px;
					
					.photo {
						overflow: hidden;
						width: 424px;
						height: 286px;
						background-color: lightyellow;
					}
					.deskription {
						max-width: 439px;
					}
					.name {
						font-style: normal;
						font-weight: 600;
						font-size: 16px;
						line-height: 140%;
						color: #000000;
						padding-bottom: 10px;
					}
	
					.email, .phone {
						font-style: normal;
						font-weight: 400;
						font-size: 14px;
						line-height: 140%;
						color: #333333;
						padding-bottom: 10px;
	
						span {
							font-weight: 600;
						}
					}
	
					.phone {
						padding-bottom: 20px;
					}
	
					h3 {
						font-style: normal;
						font-weight: 600;
						font-size: 16px;
						line-height: 140%;
						color: #333333;
						padding-bottom: 25px;
					}
	
					p {
						font-style: normal;
						font-weight: 400;
						font-size: 14px;
						line-height: 17px;
						color: #76787D;
					}
						
						}
					.text0 {
						position: absolute;
						top:50%;
						left:50%;
						transform:translate(-50%, -50%);
					}

			}
		}
	}

	.user-card {
		border: 1px solid lightseagreen;
		margin: 1em 0;
		padding: 1em;
	}
</style>