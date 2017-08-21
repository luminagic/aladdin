# MeoMo iMeter 嵌入版 (aladdin) 集成指南

## 接口函数

Aladdin 采用 单一入口 + dispatch 的设计方式。
即所有javascript函数调用一个单一的入口，此入口根据输入参数，再调用真正的逻辑实现代码。

```javascript
window.aladdin.hasObject(object);

window.aladdin.exec(resolve, reject, object, method, args);
```

## 亮度调整

```javascript
/**
 * 调整屏幕/窗口亮度为指定值，并保存原亮度。当窗口切换时，恢复原亮度。重新切换回来时，重新设置亮度为给定值。
 * 若此功能不被支持，window.AutoBrightness 应与 false 等价 (null, undefined 等)
 * 
 * 例如：测量时，setBrightness(1) 会将屏幕设为最亮，如果这时用户切换app，比如接电话，屏幕将恢复原来的亮度。
 * 接完电话后，用户切换回测量app，这时屏幕又变为最亮。
 * 
 * 注意：切换回测量app时，应记录当时屏幕亮度。
 * @param {float} brightness - 亮度, 0为最暗，1为最亮
 * @return {Promise} 表明操作是否完成，以及是否成功。
 */
AutoBrightness.__proto__.setBrightness = function (brightness) {
    // To be implemented
}

/**
 * 恢复屏幕亮度，并且不再自动设置屏幕亮度。即不论用户如何切换app，不再主动调整亮度。
 * 
 * @return {Promise} 表明操作是否完成，以及是否成功。
 */
AutoBrightness.__proto__.clear = function () {
    // To be implemented
}
```

## 生成授权token

```javascript
/**
 * 生成一个新的授权token。该token应当为JWT格式。
 * 
 * 该token的签名方式应当使用RS256。即header内容为 {"alg":"RS256","typ":"JWT"}。
 * payload应当含有以下字段：
 *  - user: 用户的id
 *  - exp: 时间戳
 *  - iss: 签发方
 *  - rand: 一个随机值
 * 
 * @return {Promise} 表明操作是否完成，以及是否成功。若成功，可以从中获得token字符串。
 */
Auth.__proto__.getJwtToken = function () {
    // To be implemented
}
```
