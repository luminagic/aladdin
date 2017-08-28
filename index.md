# MeoMo iMeter 嵌入版 (aladdin) 集成指南

## 接口函数

Aladdin 采用 单一入口 + dispatch 的设计方式。
即所有javascript函数调用一个单一的入口，此入口根据输入参数，再调用真正的逻辑实现代码。

所有功能按 __Service__ 划分，一个 Service 可以含有多个 __Action__.
可以选择实现部分 Service，但一旦实现某个 Service，其中的所有 Action 都应当被实现。

```javascript
/**
 * 检测指定的 Service 是否被支持。
 *
 * @param {string} service Service 的名字，区分大小写
 */
window.aladdin.hasService(service);

/**
 * 执行指定的 Action。
 *
 * @param {function} resolve 执行成功后调用的回调函数。参数为执行的返回值。
 * @param {function} reject 执行失败后调用的回调函数。参数为关于失败的额外信息。
 * @param {string} service Service 的名字，区分大小写
 * @param {string} action Action 的名字，区分大小写
 * @param {array} args 执行 Action 的参数
 */
window.aladdin.exec(resolve, reject, service, action, args);
```

传入的回调函数 `resolve` 和 `reject` 可能为 `null`，即不需要回调。

__注意__：Action 的执行不强制为异步，但对于不能立即返回的实现，应使用异步方式。

## Services
Aladdin 会试图调用以下 Service：
  - AutoBrightness
  - Auth

### 亮度调整: AutoBrightness

#### Action: setBrightness

调整屏幕/窗口亮度为指定值，并保存原亮度。当窗口切换时，恢复原亮度。重新切换回来时，重新设置亮度为给定值。

例如：测量时，`setBrightness(1)` 会将屏幕设为最亮，如果这时用户切换app，比如接电话，屏幕将恢复原来的亮度。
接完电话后，用户切换回测量app，这时屏幕又变为最亮。

__注意__：切换回测量app时，应记录当时屏幕亮度。

__注意2__： 若程序被终止，则此亮度记录应当被丢弃。因为下次启动时，不会直接进入测量界面。

参数：
  - `brightness`, float: 亮度, 0为最暗，1为最亮


#### Action: clear
恢复屏幕亮度，并且不再自动设置屏幕亮度。即不论用户如何切换app，不再主动调整亮度。

### 生成授权token：Auth

#### Action: getJwtToken
生成一个新的授权token。该token应当为JWT格式。

该token的签名方式应当使用RS256。即header内容为 `{"alg":"RS256","typ":"JWT"}`。

payload应当含有以下字段：
 - `usr`: 用户的id
 - `exp`: 有效期时间戳
 - `iss`: 签发方，请与我们联系，协商此字段的具体内容
 - `rnd`: 一个随机值

返回值，string：JWT token

__注意__：由于此 token 的验证为 offline 进行，所以无法撤销。为保证安全，请不要授权太长时间。
我们的建议是有效期 15 分钟以下。