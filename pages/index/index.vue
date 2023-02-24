<template>
	<page-meta :page-style="'overflow:'+(show?'hidden':'visible')"></page-meta>
	<view class="container">
		<view class="top">
			<image src="/static/images/fill.webp"></image>
		</view>
		<view class="main">
			<view class="bar">
				<uni-notice-bar showIcon :text="superChat"></uni-notice-bar>
			</view>
			<view class="content">
				<uni-forms ref="form" label-position="top" label-width="100vw" :modelValue="formData" :rules="rules">
					<uni-forms-item v-for="(item,index) in mocks" :key="item.id" :name="'content'+item.id">
						<span slot="label">
						</span>
						<template v-if="item.type ==='input'">
							<uni-section :title="item.title" type="line">
								<view class="uni-px-5 uni-pb-5">
									<uni-easyinput type="text" v-model="formData['content'+item.id]"
										placeholder="请填写内容" />
								</view>
							</uni-section>
						</template>
						<template v-if="item.type==='checkbox'">
							<uni-section :title="item.title" type="line">
								<view class="uni-px-5 uni-pb-5">
									<uni-data-checkbox mode="list" :multiple="item.choice.type=='single'?false:true"
										v-model="formData['content'+item.id]" :localdata="item.content"
										:max="item.choice.max" :min="item.choice.min">
									</uni-data-checkbox>
								</view>
							</uni-section>

						</template>
					</uni-forms-item>
				</uni-forms>
				<!-- <navigator open-type="exit" target="miniProgram">退出</navigator> -->
				<!--  -->
				<button class="form-sumbit" @click="submit">Submit</button>
			</view>
		</view>
		<view>
			<uni-popup ref="popup" background-color="#fff" @change="change">
				<view class="popup-content" :class="{ 'popup-height': type === 'left' || type === 'right' }"><text
						class="text">popup 内容</text></view>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	import mock from '../../mocks/new_file.json'
	export default {
		data() {
			return {
				show: false,
				type: 'center',
				mocks: mock,
				superChat: "感谢您抽出时间填写问卷，我们会在您填写完毕后送出小礼物！",
				formData: {
					content1: null,
					content2: [],
					content3: [],
					content4: [],
					content5: [],
				}, // 表单数据
				rules: {},

			}
		},
		created() {
			// this.$nextTick(() => {
			this.initData()
			// })
		},
		onReady() {
			// 设置自定义表单校验规则，必须在节点渲染完毕后执行
			this.$refs.form.setRules(this.rules)

		},
		methods: {
			change(e) {
				this.show = e.show
				console.log('当前模式：' + e.type + ',状态：' + e.show);
			},
			submit() {
				this.$refs.form.validate().then(res => {
					console.log('表单数据信息：', res);
					this.$refs.popup.open('center')
				}).catch(err => {
					console.log('表单错误信息：', err);
				})
			},
			//初始化数据
			initData() {
				//对表单数据赋空值
				this.mocks.map((item) => {
					switch (item.type) {
						case 'input':
							this.formData['content' + item.id] = null
							break;
						case 'checkbox':
							this.formData['content' + item.id] = []
							break;
					}
				})
				//设置表单校验
				let rule = {};
				this.mocks.map(item => {
					rule['content' + item.id] = {
						rules: [{
							required: true,
							errorMessage: '该选项不能为空'
						}]
					}
					if (item.choice?.type) {
						switch (item.choice.type) {
							case 'multiple':
								rule['content' + item.id].rules.push({
									format: 'array'
								}, {
									validateFunction: function(rule, value, data, callback) {
										if (value.length < 3) {
											callback('请至少勾选三个数字')
										}
										return true
									}
								})
								break;
						}
					}
				})
				this.rules = rule;
			}
		}
	}
</script>

<style>
	@import "index.css";
</style>
