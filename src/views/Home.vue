<template>
	<div id="flowChart">
		<div class="operating">
			<div class="btn-group">
				<div class="btn" @click="addCircle" title="起始节点">
					<i class="fa fa-circle-o"></i>
				</div>
				<div class="btn" @click="addRect" title="常规节点">
					<i class="fa fa-square-o"></i>
				</div>
				<div class="btn" @click="addRhombus" title="条件节点" style="transform: rotate(45deg)">
					<i class="fa fa-square"></i>
				</div>
			</div>
			<div class="btn-group">
				<!-- <div class="btn" @click="addArrowLine" title="箭头直线">
					<i class="fa fa-long-arrow-right"></i>
				</div> -->
				<div class="btn" @click="addArrowSmooth" title="箭头曲线">
					<i class="fa fa-long-arrow-right"></i>
				</div>
			</div>
			<div class="btn-group">
				<div class="btn" @click="changeMode('edit')" title="选择模式">
					<i class="fa fa-location-arrow"></i>
				</div>
				<div class="btn" @click="changeMode('drag')" title="拖拽模式">
					<i class="fa fa-arrows"></i>
				</div>
			</div>
			<div class="btn-group">
				<div class="btn" @click="del" style="margin-top: 5px;" title="删除">
					<i class="el-icon-delete"></i>
				</div>
				<div class="btn" @click="save" title="保存">
					<i class="fa fa-save"></i>
				</div>
			</div>
			<div class="btn-group">
				<el-input size="mini" v-model="workflowName" placeholder="请输入流图名称..."></el-input>
			</div>
		</div>
		<div class="info" ref="inputFocus">
			<div class="title">
				<span>{{infoTitle}}属性</span>
			</div>
			<div class="content">
				<el-checkbox v-if="isBlank === true" v-model="checked">网格对齐</el-checkbox>
				<el-form v-else label-position="left" label-width="60px">
					<el-form-item v-if="isNode !== true" label="动作">
						<el-select v-model="action" size="mini" filterable placeholder="绑定动作" value="">
							<el-option
								v-for="item in actionList"
								:key="item.id"
								:label="item.label"
								:value="item.id">
							</el-option>
						</el-select>
					</el-form-item>
					<el-form-item v-if="isNode === true" label="名称">
						<el-input size="mini" v-model="name"></el-input>
					</el-form-item>
					<el-form-item v-if="isNode === true" label="功能">
						<el-select v-model="func" size="mini" filterable placeholder="绑定功能" value="">
							<el-option
								v-for="item in funcList"
								:key="item.id"
								:label="item.label"
								:value="item.id">
							</el-option>
						</el-select>
					</el-form-item>
					<el-form-item v-if="isNode === true" label="账号">
						<el-select v-model="account" size="mini" filterable multiple
								collapse-tags placeholder="绑定账号" value="">
							<el-option
								v-for="item in accountList"
								:key="item.id"
								:label="item.label"
								:value="item.id">
							</el-option>
						</el-select>
					</el-form-item>
					<el-form-item v-if="isNode === true" label="流图">
						<el-select v-model="workflow" size="mini" filterable clearable placeholder="绑定流图" value="">
							<el-option
								v-for="item in workflowList"
								:key="item.id"
								:label="item.label"
								:value="item.id">
							</el-option>
						</el-select>
					</el-form-item>
					<el-form-item v-if="isNode === true" label="类型">
						<el-select v-model="nodeType" size="mini" filterable placeholder="请选择类型" value="">
							<el-option
								v-for="item in nodeTypeList"
								:key="item.id"
								:label="item.label"
								:value="item.id">
							</el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="颜色">
						<el-color-picker v-model="color"></el-color-picker>
					</el-form-item>
				</el-form>
			</div>
		</div>
	</div>
</template>

<script>
import { Component, Vue } from "vue-property-decorator";
import G6 from '@antv/g6';


export default {
	name: "index",
	components: {},
	mounted() {
	  	this.initG6();
	},
	data() {
		return {
			action: '',
			name: '',
			func: '',
			account: '',
			workflow: '',
			nodeType: 0,
			color: '',
			net: '',
			Util: '',
			workflowName: '',
			activation: '', //当前激活的节点
			isNode: false, //当前是节点
			isBlank: true,   //当前是空白区
			checked: true,  //网格对齐
			infoTitle: '画布',//属性标题
			oldColor: '',    //获取节点本身颜色
			type: '',        //有值为编辑状态
			actionList: [
				{id: 1, label: '通过'},
				{id: 2, label: '拒绝'},
			],
			funcList: [],
			accountList: [],
			workflowList: [],
			nodeTypeList: [
				{id: 0, label: '普通节点'},
				{id: 1, label: '入口节点'},
				{id: 2, label: '出口节点'}
			]
		}
	},
	methods: {
		initG6() {
			let self = this;
			self.Util = G6.Util;
			let grid;
			if (self.checked) {
				grid = {
					forceAlign: true, // 是否支持网格对齐
					cell: 25,         // 网格大小
				};
			} else {
				grid = null;
			}
			self.net = new G6.Net({
				id: 'flowChart',      // 容器ID
				mode: 'edit',
				grid: grid,
				/*width: 500,    // 画布宽*/
				height: 800    // 画布高
			});
			self.net.source(
			{
				edges: [
					{
						action: 1,
						color: undefined,
						id: "ac806fbf",
						label: "通过",
						shape: "smoothArrow",
						source: "af197896",
						target: "60ea3558",
					},
					{
						action: 1,
						color: undefined,
						id: "6e001b36",
						label: "通过",
						shape: "smoothArrow",
						source: "60ea3558",
						target: "80d13c36"
					},
					{
						action: 2,
						color: undefined,
						id: "79764613",
						label: "拒绝",
						shape: "smoothArrow",
						source: "60ea3558",
						target: "2addec69",
					},
					{
						action: 1,
						color: undefined,
						controlPoints: [
							{x: 510.1999969482422, y: 250},
							{x: 563, y: 271},
							{x: 633.1285925474206, y: 300}
						],
						id: "241fbf87",
						label: "通过",
						shape: "smoothArrow",
						source: "80d13c36",
						target: "91de231a"
					},
					{
						action: 1,
						color: undefined,
						controlPoints: [
							{x: 510.1999969482422, y: 400},
							{x: 558, y: 356},
							{x: 633.1285925474206, y: 300}
						],
						id: "d5557e46",
						label: "通过",
						shape: "smoothArrow",
						source: "2addec69",
						target: "91de231a"
					},
					{
						action: 2,
						color: undefined,
						controlPoints: [
							{x: 475, y: 234},
							{x: 400, y: 200},
							{x: 325, y: 250},
							{x: 300, y: 309}
						],
						id: "c0f660dd",
						label: "拒绝",
						shape: "smoothArrow",
						source: "80d13c36",
						sourceAnchor: 0,
						target: "60ea3558",
						targetAnchor: 0
					}
				],
				name: "测试流程",
				nodes: [
					{
						account: [],
						color: undefined,
						func: undefined,
						id: "af197896",
						label: "开始",
						nodeType: 0,
						shape: "circle",
						workflow: undefined,
						x: 150,
						y: 325
					},
					{
						account: [],
						color: undefined,
						func: undefined,
						id: "91de231a",
						label: "结束",
						nodeType: 2,
						shape: "rhombus",
						workflow: undefined,
						x: 675,
						y: 300
					},
					{
						account: [],
						color: undefined,
						func: undefined,
						id: "80d13c36",
						label: "节点2",
						nodeType: 1,
						shape: "rect",
						workflow: undefined,
						x: 475,
						y: 250
					},
					{
						account: [],
						color: undefined,
						func: undefined,
						id: "60ea3558",
						label: "节点1",
						nodeType: 1,
						shape: "rect",
						workflow: undefined,
						x: 300,
						y: 325
					},
					{
						account: [],
						color: undefined,
						func: undefined,
						id: "2addec69",
						label: "节点3",
						nodeType: 1,
						shape: "rect",
						workflow: undefined,
						x: 475,
						y: 400
					}
					
				]
			})
			self.net.on('click', (ev) => {
				if (!self.Util.isNull(ev.item)) {
					self.isBlank = false
				} else {
					self.isBlank = true;
					self.infoTitle = '画布'
				}
			});
			self.net.on('itemclick', function (ev) {
				self.isNode = self.Util.isNode(ev.item);   //是否为Node
				self.activation = ev.item;
				console.log(self.activation)
				console.log(self.activation._attrs.model)
				if (self.isNode) {
					/* 激活节点后节点名称input聚焦*/
					self.$nextTick(()=>{
						self.$refs.inputFocus.querySelector('input').focus();
					});
					self.infoTitle = '节点';
					self.name = ev.item.get('model').label;
					self.func = ev.item.get('model').func;
					self.account = ev.item.get('model').account || [];
					self.workflow = ev.item.get('model').workflow;
					self.nodeType = ev.item.get('model').nodeType;
				} else {
					self.infoTitle = '边';
					self.action = ev.item.get('model').action;
				}
				self.color = self.oldColor;
			});
			self.net.on('itemmouseenter', ev => {
				const item = ev.item;
				self.oldColor = item.get('model').color;     //获取节点颜色
				self.net.update(item, {
					color: '#108EE9',
				});
				self.net.refresh();
			});
			self.net.on('itemmouseleave', ev => {
				const item = ev.item;
				self.net.update(item, {
					color: self.oldColor
				});
				self.net.refresh();
			});
			self.net.render();
		},
		addCircle() {
			this.net.beginAdd('node', {
				shape: 'circle',
				nodeType: 0,
				woshinidie: 999
			})
		},//添加起始节点
		addRect() {
			this.net.beginAdd('node', {
				shape: 'rect',
				nodeType: 1,
				woshinidie: 'fenghe'
			})
		},//添加常规节点
		addRhombus() {
			this.net.beginAdd('node', {
				shape: 'rhombus',
				nodeType: 2,
				woshinidie: 888
			})
		}, //添加条件节点
		addArrowSmooth() {
			this.net.beginAdd('edge', {
				shape: 'smoothArrow'
			})
		}, //添加箭头曲线
		// addArrowLine() {
		// 	this.net.beginAdd('edge', {
		// 		shape: 'arrow'
		// 	});
		// }, //添加箭头直线
		addPolyLine() {
			this.net.beginAdd('edge', {
				shape: 'polyLineFlow'
			});
		}, //添加折线
		changeMode(mode) {
			this.net.changeMode(mode)
		}, //拖拽与编辑模式的切换
		del() {
			this.net.del()
		},//删除
		save() {
			/* 验证流图名称*/
			if (this.workflowName !== '') {
				let data = this.net.save();
				if (data.source.nodes.length === 0) {
					this.$message({type: 'error', message: '流图内容不能为空'});
					return false
				}
				/* 验证节点名称*/
				for (let item of data.source.nodes) {
					if (item.label === '' || item.label === null || item.label === undefined) {
						this.$message({type: 'error', message: '节点名称不能为空'});
						return false
					}
				}
				data.source['name'] = this.workflowName;
				/*let json = JSON.stringify(data, null, 2);*/
				this.$emit('saveData', data.source, this.type);
				console.log(data.source)
				console.log(this.type)
			} else {
				this.$message({type: 'error', message: '流图名称不能为空'})
			}
			// /*console.log(saveData, json);*/
		},//保存
		update() {
			if (this.activation.get('type') === 'node') {
				this.net.update(this.activation, {
					label: this.name,
					func: this.func,
					account: this.account,
					workflow: this.workflow,
					nodeType: this.nodeType,
					color: this.color
				});
				} else {
				/* 根据ID取出label*/
				let label = this.actionList.map(item => {
					if (item.id === this.action) {
						return item.label
					}
				}).join('');
				this.net.update(this.activation, {
					label: label,
					color: this.color,
					action: this.action
				});
			}
		},  //更新节点
		clearView() {
			this.type = '';
			this.workflowName = '';
			this.net.changeData()
		},   //清空视图
		source(nodes, edges, name, type) {
			this.type = type;
			this.workflowName = name;
			this.net.changeData(nodes, edges)
		},  //更新数据
	},
	watch: {
		/**
		 * 监听输入框
		 */
		action: function () {
			this.update()
		},
		name: function () {
			this.update()
		},
		func: function () {
			this.update()
		},
		account: function () {
			this.update()
		},
		workflow: function () {
			this.update()
		},
		nodeType: function () {
			this.update()
		},
		color: function () {
			this.update()
		},/**
		* 网格切换
		*/
		checked: function () {
			let _saveData = this.net.save();
			this.net.destroy();  //销毁画布
			this.initG6();
			this.net.read(_saveData);
			this.net.render()
		}
	}
}
</script>
<style lang="scss">
#flowChart {
	border: 1px solid #ebeef5;
	position: relative;
	overflow: hidden;
}
.operating {
	position: absolute;
	z-index: 99;
	background-color: #ffffff;
	padding: 20px 10px;
	box-shadow: 1px 1px 4px 0 #0a0a0a2e;
}
.info {
	position: absolute;
	right: 0;
	z-index: 99;
	box-shadow: 1px 1px 4px 0 #0a0a0a2e;
	.title {
		height: 40px;
		padding-left: 10px;
		border-top: 1px solid #DCE3E8;
		border-bottom: 1px solid #DCE3E8;
		border-left: 1px solid #DCE3E8;
		background: rgb(235, 238, 242);
		line-height: 40px;
		span {
			font-size: 14px;
		}
	}
	.content {
		background: rgba(247, 249, 251, 0.45);
		width: 200px;
		height: 800px;
		border-left: 1px solid #E6E9ED;
		padding: 10px;
	}
}
.btn-group {
	border-right: 1px solid #efefef;
	display: inline-block;
	padding-left: 10px;
	padding-right: 14px;
	&:last-of-type {
		border-right: 0;
	}
	.btn {
		display: inline-block;
		margin: 2px;
		width: 30px;
		height: 30px;
		line-height: 30px;
		text-align: center;
		cursor: pointer;
			border: 1px solid rgba(233, 233, 233, 0);
		i {
			font-size: 20px;
		}
		&:hover {
			border: 1px solid #E9E9E9;
			color: #767A85;
			border-radius: 2px;
			background: #FAFAFE;
		}
	}
	.el-form-item {
		margin-bottom: 0 !important;
	}
}
</style>