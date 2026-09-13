# B站网页投稿的模板化一键填写<br>Auto_Form-Filling_for_Bilibili_Web_Upload

You can also call it:
 - One-click-Selection-and-Filling-for-Bilibili-Web-Upload
 - One-click template selection and auto-fill for submissions

 **本项目由千问Qwen、豆包Doubao等AI辅助完成**

## 视频文件名
```JavaScript
document.querySelector('.title-text').textContent;
```

## 创作声明

```JavaScript
[...document.querySelectorAll('.bcc-option')].find(el => el.textContent.trim() === '内容为转载');
```
使用示例
```JavaScript
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }
    async function doClick() {
        const chuangZuoShengMingBTN = [...document.querySelectorAll('.bcc-option')].find(el => el.textContent.trim() === '内容为转载');
        if (chuangZuoShengMingBTN) {
            chuangZuoShengMingBTN.click();
            await sleep(100); // 等待
            console.log('✔️【内容为转载】点击成功！');
        } else {
            console.log('❌没找到【内容为转载】按钮');
        }
    }
```

## 转载来源

```JavaScript
// 简易输入框输入方法
function setInputValue(input, value) {
    // 使用原生 setter 绕过框架的 getter/setter
    const nativeInputValueSetter = Object.getOwnPropertyDescriptor(
        window.HTMLInputElement.prototype, 'value'
    ).set;
    nativeInputValueSetter.call(input, value);

    // 触发 input 事件（Vue 监听的就是这个）
    input.dispatchEvent(new Event('input', { bubbles: true }));
    // 触发 change 事件（某些表单验证依赖这个）
    input.dispatchEvent(new Event('change', { bubbles: true }));
}
const inputs = document.querySelectorAll('input.input-val');
setInputValue(inputs[1], 'test');
```

## 分区

```JavaScript
// 展开下拉菜单
document.querySelector('.select-controller').click();
// 选择分区
[...document.querySelectorAll('.drop-list-v2-item')].find(el => el.title.trim() === '资讯').click();
```

## 标签

```JavaScript
// 输入标签方法
async function addTag(input, text) {
    // 1. 先聚焦
    input.focus();

    // 2. 用 execCommand 插入文本（走原生输入路径，触发完整事件链）
    document.execCommand('insertText', false, text);

    // 3. 兜底再触发一次 input 事件
    // input.dispatchEvent(new Event('input', { bubbles: true }));

    // 4. 模拟回车：keydown -> keypress -> keyup
    const eventInit = {
        key: 'Enter',
        code: 'Enter',
        keyCode: 13,
        which: 13,
        bubbles: true,
        cancelable: true
    };
    input.dispatchEvent(new KeyboardEvent('keydown', eventInit));
    // input.dispatchEvent(new KeyboardEvent('keypress', eventInit));
    // input.dispatchEvent(new KeyboardEvent('keyup', eventInit));
}
// 创作声明选转载或其他，由于“转载来源”输入框，“标签”输入框的索引编号会变化，请根据实际需求调试修改
const inputs = document.querySelectorAll('input.input-val');
const allVideoTags = [...videoTags, ...videoTags2]
allVideoTags.forEach((text, i) => {
    setTimeout(() => addTag(inputs[2], text), i * 1000);
});
```

## 简介

蓝色状态的@其他人，在元素检查中可以发现是```<span>``` ```</span>```包裹的一系列内容，但是通过```.innerHTML```方法修改无法真正插入，曲线救国方法是插入```@某UP主全名```，等脚本运行完鼠标点到UP主名字后面，触发浮动选窗，手动点击。
```JavaScript
document.querySelector('.ql-editor').innerHTML = '<p>line1</p><p>line2</p>';
```

## 合集

```JavaScript
// 展开下拉菜单
document.querySelector('.season-enter').click();
// 选择合集
[...document.querySelectorAll('.season-content > div')].find(el => el.textContent.trim() === '这里填入目标合集的完整名称').click();
```

## 打开封面编辑界面

需要等到投稿系统呈现出推荐的封面，点击才生效。可以在页面上自制一个按钮，将整个半自动输入脚本绑定按钮，看到推荐封面出来了再手动触发脚本。
```JavaScript
document.querySelector('.cover-img').click();
```
让封面编辑“完成”按钮延长到左边方便点击
```JavaScript
document.querySelector('.button.submit.button.submit').style.width='700px';
```
