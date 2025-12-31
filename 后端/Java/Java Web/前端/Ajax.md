- 数据交换
	- 给服务器发送请求，获取服务器响应的数据
- 异步交互
	- 在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术
- Axios
	- 封装ajax
	- 引入Axios的js
	- 发送请求获取响应结果
```js
axios({
	method:'GET',//请求方式GET/POST
	url:''//路径
}).then((result)=>{//成功回调
	console.log(result.data);//请求数据post
}).catch((err)=>{//失败回调
	alert(err);
})
params:get发送请求时携带的url参数

axios.请求方式(url[,data[,config]])
```
- async
	- 声明一个异步方法
- await
	- 等待异步任务执行
	- 只在async中有效
	- 取代then
```js
methods:{
	async search(){
		let result = await axios.get();
		this.employees = result.data.data;
	}
}
```
- 加载网页时查询勾子方法