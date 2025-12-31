- 构建用户界面的渐进式js框架
1. 引入vue
2. 创建vue实例
3. 准备元素被vue控制
```html
<div id="app">
	{{message}}
</div>
<script type="module">
	import{createApp} from ‘vue,js’//es模块
	createApp({
		data(){
			return{
				//数据模型
				message:"",
				...
			}
		}
	}).mount("#app");
</script>
```
# 指令

| 指令                    |                           |
| --------------------- | ------------------------- |
| v-for                 | 列表渲染，遍历容器元素或者对象属性         |
| v-bind                | 为html标签绑定属性值，设置href，css样式 |
| v-if/v-else-if/v-else | 条件性的渲染某元素，判定为true渲染       |
| v-show                | 根据条件展示某元素，切换的是display属性的值 |
| v-model               | 在表单上创建双向数据绑定              |
| v-on                  | 为html标签绑定事件               |
```html
<tr v-for="(item,index) in items":key="item.id">{{item}}</tr>
# index可省
# key
# 给元素添加唯一标识,便于vue进行列表正确使用排序重复

<img v-bind:属性名='属性值'>
<img :属性名='属性值'>//简化
//插值表达式不能出现在标签内

v-if="表达式"
基于条件判断，控制移除元素节点
不频繁切换的场景

v-show
基于css.display控制显示隐藏
频繁切换的场景

v-model="变量名"
获取设置表单项数据
<input type='' id='' v-model="searchForm.name">
<script type="module">
	import{createApp} from ‘vue,js’//es模块
	createApp({
		data(){
			return{
				//定义数据模型
				searchForm:{
					name:'',
					gender:'',
					...
				}
			}
		}
	}).mount("#app");
</script>

v-on:事件名="方法名"
简写 @事件名="..."
<script type="module">
	import{createApp} from ‘vue,js’//es模块
	createApp({
		data(){
			...
		},
		methods:{
			...
		}
	}).mount("#app");
</script>
```
# 工程化
- createvue脚手架
- 创建：npm create vue@版本
- 安装依赖：进入项目cd，npm install
## API
- 选项式
	- 可以用包含多个选项的对象来描述组件的逻辑,如:data,methods,mounted等。选项定义的属性都会暴露在函数内部的this上,它会指向当前的组件实例
- 组合式
	- 是Vue3提供的一种基于函数的组件编写方式,通过使用函数来组织和复用组件的逻辑。它提供了一种更灵活、更可组合的方式来编写组件
- < style>标签带有scoped attribute时，css只会影响当前组件元素，和Shadow DOM中的样式类似