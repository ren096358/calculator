<template>
  <div class="calculator">
		<div class="header">
			<div class="countProcess">{{ processToString }}</div>
			<div class="currentNumber" v-if="!counting">{{ formatTextNumber }}</div>
			<div class="resultNumber" v-else>{{ formatResult }}</div>
		</div>
		<div class="body" @click="concatNumber">
			<div>7</div>
			<div>8</div>
			<div>9</div>
			<div class="countSymbol" @click="addToProcess('÷')">÷</div>
			<div>4</div>
			<div>5</div>
			<div>6</div>
			<div class="countSymbol" @click="addToProcess('×')">×</div>
			<div>1</div>
			<div>2</div>
			<div>3</div>
			<div class="countSymbol" @click="addToProcess('+')">+</div>
			<div>0</div>
			<div>00</div>
			<div>.</div>
			<div class="countSymbol" @click="addToProcess('-')">-</div>
			<div class="deleteSymbol" @click="resetData">AC</div>
			<div class="deleteSymbol" @click="deleteLastNumber">⌫</div>
			<div id="resultSymbol" class="countSymbol" @click="finalResult">
				<div></div>
				<div>=</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
  name: "Calculator",
  data() {
    return {
      textNumber: '',
      result: 0,
      process: [],
      counting: false,
    }
	},
	computed: {
		formatTextNumber() {
			if (!this.textNumber.includes('.')) {
				return this.textNumber.replace(/(\d{1,3})(?=(\d{3})+$)/g, "$1,")
			} else {
				return this.textNumber.replace(/(\d{1,3})(?=(\d{3})+\.)/g, "$1,");
			}
		},
		textNumberToNumber() {
			return Number(this.textNumber)
		},
		processToString() {
			let str = ''
			this.process.forEach(function (obj) {
				if (obj.mathSymbol !== null) {
					str += (obj.mathSymbol + ' ')
				}
				if (obj.number !== null) {
					str += (obj.number + ' ')
				}
			})
			return str
		},
		formatResult() {
			if (this.result % 1 === 0) {
				return (this.result.toString()).replace(/(\d{1,3})(?=(\d{3})+$)/g, "$1,")
			} else {
				return (this.result.toString()).replace(/(\d{1,3})(?=(\d{3})+\.)/g, "$1,");
			}
		}
	},
	methods: {
		concatNumber(event) {
			let text = event.target.textContent
			let temp = this.textNumber + text
			if (!isNaN(temp)) {
				this.textNumber += text
				this.counting = false
			}
		},
		addToProcess(mathSymbol) {
			//直接輸入符號，未帶入數字
			if (this.textNumber === '') {
				if (this.process.length === 0) {
					return
				} else {
					let obj = this.process.pop()
					obj.mathSymbol = mathSymbol
					this.process.push(obj)
				}
			} else {
				let obj = this.process.pop()
				//第一次輸入
				if (obj === undefined) {
					obj = {
						mathSymbol: null
					}
				}
				obj.number = this.textNumberToNumber
				this.process.push(obj)

				obj = {
					number: null,
					mathSymbol: mathSymbol
				}
				this.process.push(obj)

			}
			this.textNumber = ''
			this.caculate()
		},
		deleteLastNumber() {
			this.textNumber = this.textNumber.substring(
				0,
				this.textNumber.length - 1
			);
		},
		resetData() {
			this.textNumber = ''
			this.result = 0
			this.process = []
			this.counting = false
		},
		caculate() {
			if (this.process.length === 0) {
				return
			}
			this.result = this.process[0].number
			let self = this
			this.counting = true
			this.process.some(function (obj, index) {
				if (index === 0) {
					return false
				}
				if (obj.number !== null) {
					switch (obj.mathSymbol) {
						case '+':
							self.result += obj.number
							break
						case '-':
							self.result -= obj.number
							break
						case '×':
							self.result *= obj.number
							break
						case '÷':
							if (obj.number === 0) {
								self.result = '無法除以零'
								self.process = []
								return true
							}
							self.result /= obj.number
							break
						default:
              console.log('無效的運算符號')
							break
					}
				}
			})
		},
		finalResult() {
			if (this.textNumber !== '') {
				let obj = this.process.pop()
				obj.number = this.textNumberToNumber
				this.process.push(obj)
			}
			this.caculate()
			this.textNumber = ''
			this.process = []
		}
	}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
}

body {
    background-color: #E8E8E8;
}

.calculator {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 350px;
    height: 525px;
    background-color: #062145;
    border-radius: 20px 20px 20px 20px;
    color: #FFFFFF;
}

.header {
    height: 109px;
    background-color: #041936;
    border-radius: 20px 20px 0 0;
    text-align: right;
    padding: 16px 16px 8px 16px;
}

.countProcess {
    min-height: 19px;
    font-size: 16px;
    color: #00C4FF;
    overflow: scroll;
    white-space: nowrap;
}

.countProcess::-webkit-scrollbar {
    display: none;
}

.currentNumber {
    font-size: 56px;
    overflow: scroll;
}

.currentNumber::-webkit-scrollbar {
    display: none;
}

.resultNumber {
    font-size: 56px;
    overflow: scroll;
}

.resultNumber::-webkit-scrollbar {
    display: none;
}

.body {
    height: 416px;
    padding: 8px;
    background-color: #062145;
    border-radius: 0 0 20px 20px;
    font-size: 24px;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
}

.body>div {
    width: 72px;
    height: 64px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 5px;
    border-radius: 16px 16px 16px 16px;
}

.body>div:hover {
    cursor: pointer;
    border: 1px solid white;
}



.countSymbol {
    background-color: #041936;
}

#resultSymbol {
    background-color: #6C00FF;
    width: 155px;
    background: linear-gradient(to right, #00C4FF, #6C00FF);
}

#resultSymbol>div {
    display: flex;
    flex: 0 0 50%;
    justify-content: center;
    align-items: center;
}

.deleteSymbol {
    color: #00C4FF;
}
</style>
