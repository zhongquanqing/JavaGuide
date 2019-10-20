# 移动端cas登陆相关文档

<div style="float:right">

|作者|日期|
|----|---|
|郑烨锟|2019年5月20日|

</div>

## cas登陆流程与配置说明

	cas登陆需要配置才能对移动端页面进行cas登陆验证

#### cas登陆流程

1. 验证要访问的页面是否需要cas登陆验证
2. 如果不需要cas登陆验证，则直接访问该页面；否则判断是否已cas登陆
3. 如果尚未cas登陆，则跳转至cas登陆验证页面；否则进行下一步验证或直接访问该页面

> 注：部分页面除了cas登陆外还有其他验证，例如：cas登陆验证完，访问个人中心还需要走个人中心验证流程。

#### cas登陆路由配置

1. 当前实现为根据路由配置进行判断某个页面是否需要cas登陆，具体由PC端可定制化配置和开发人员进行代码修改
2. 若是开发人员配置，意思指开发人员直接在程序的路由文件中的指定页面路由添加cas登陆验证的判断
3. 若为实施配置，则为可定制化配置，看下个标题：cas登陆验证可配置化

#### cas登陆验证可配置化

> 注：尚未实现

## cas登陆相关接口
    
    此为cas登陆流程和部分实现逻辑

#### cas登陆验证接口

> /cas/login?client_name=iboa2&code=A0`admin`a0z9`356A192B7913B04C54574D18C28D46E6395428AB`a0z9`0123`

*请求方式*：GET

*传入参数*

```js
用户名：admin
sha1加密大写密码：356A192B7913B04C54574D18C28D46E6395428AB
验证码：0123
```

> 注：验证码需要根据各项目的配置进行判断需不需要

*返回参数*

```js
// 若登陆成功，则会返回重定向的PC成功页
<html>
	...
</html>
// 若登陆失败，则会报错
状态码500：表示账号、密码等错误
```

> 注：该接口还会返回Cookie用作之后的cas是否已登陆的验证，前端无需处理，也处理不了。

#### 验证是否已登陆接口

> /mainWeb/initLogin

	该接口可验证是否已进行cas登陆，但不唯一

*请求方式*：GET

*传入参数*

```js
无
```

> 注：该接口会根据Cookie验证是否已进行cas登陆，并且该接口在多个模块中均拥有，例如：`/formengineWebService/initLogin`，并且作用一致

*返回参数*

```js
// 若已登陆，返回登陆ID
"00000001-0000-0000-0010-000000000001"
// 若未登录，返回重定向的cas登录页
<html>
	...
</html>
```

> 这些返回都是在请求success响应中处理

## 移动端验证流程

1. 在vue-router中存在`meta`的`isNeedLogin`属性，判断该页面是否需要cas登陆验证，其中的isPersonalHomePage属性是判断是否需要个人中心流程验证的

2. 每个页面进入之前都要判断当前页面是否需要cas登陆，而是否已cas登陆，则通过<a href="#ibase/微信公众号/接口文档/移动端cas登陆相关文档?id=验证是否已登陆接口">/mainWeb/initLogin</a>接口判断

## vue-router中实现

```js
router.beforeEach((to, from, next) => {

	// 如果是要进入个人中心首页或相关页面，需要验证配置和人脸识别
	if (!to.meta.isNeedLogin && to.meta.isPersonalHomePage) {
		if ((/^true$/i).test(store.getters.getVerifyState)) {
			// 完成人脸识别表示已完成个人设置
			next();
		} else {
			// 开始个人设置
			next({ path: '/preApprovenew', query: { isPersonalHomeCheck: true } });
		}
	}

	if (from.path !== 'checkLogin' && to.meta.isNeedLogin) {
		fetch('/mainWeb/initLogin').then(response => {
			if (/html/i.test(response)) {
				// 返回若是html页面，则表示未cas登陆
				next({ path: '/checkLogin', query: { isTo: to.path }});
			} else {
				// cas登陆成功
				if (to.meta.isPersonalHomePage) {
					if ((/^true$/i).test(store.getters.getVerifyState)) {
						// 完成人脸识别表示已完成个人设置
						next();
					} else {
						// 开始个人设置
						next({ path: '/preApprovenew', query: { isPersonalHomeCheck: true } });
					}
				} else {
					next();
				}
			}
		}).catch(error => {
			console.log(error);
			next({ path: '/checkLogin', query: { isTo: to.path }});
		});
	} else {
		next();
	}

});
```
