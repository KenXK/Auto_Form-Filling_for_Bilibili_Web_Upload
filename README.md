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

## 展开更多设置
```JavaScript
document.querySelectorAll('.label')[1].click();
```

## 打开章节编辑页面
```JavaScript
document.querySelectorAll('.bcc-button.button.bcc-button--blueLine.small')[3].click();
```
**注意，如果在封面编辑窗口关闭前就打开章节编辑窗口，会导致关闭这两个窗口、回到投稿编辑页面时，页面无法上下滚动**

**解决办法（任选一）：**
 - **先打开章节编辑，再打开封面编辑**
 - **确保关闭封面编辑后，再打开章节编辑**

## 章节编辑
由于章节编辑窗口实际是`ifram`包含的另一个`Document`，本项目采用**另起油猴脚本**的方案
```JavaScript
// ==UserScript==
// @name         B站章节编辑iframe处理
// @namespace    https://github.com/KenXK/Auto_Form-Filling_for_Bilibili_Web_Upload
// @version      1.0
// @description  try to take over the world!
// @author       Github@KenXK
// @match        https://www.bilibili.com/web/player/index.html?source_type=editor&scene=chapter&from=publish&cid=*&title=*
// @run-at       document-idle
// @grant        none
// ==/UserScript==

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

(async function() {
    'use strict';

    await sleep(1000)

    // 根据你的需要修改触发条件
    if (document.querySelector('.editor-head-title').innerText.includes("稿件名称包含的关键词")) {
        console.log('章节iframe匹配成功')
        const zhangJieWenBenBianJiQiBTN = document.querySelector('.chapter-manager-text-editor-btn');
        if (zhangJieWenBenBianJiQiBTN) {
            zhangJieWenBenBianJiQiBTN.click();
            await sleep(100); // 等待
            console.log('✔️【章节文本编辑器】点击完成！');
        } else {
            console.log('❌没找到【章节文本编辑器】按钮');
        }

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
            // input.dispatchEvent(new KeyboardEvent('keydown', eventInit));
            // input.dispatchEvent(new KeyboardEvent('keypress', eventInit));
            // input.dispatchEvent(new KeyboardEvent('keyup', eventInit));
        }

        const textArea = document.querySelector('.text-area');
        if (textArea) {
            addTag(textArea, "00:00:00 第一个章节名称\n00:30:00 第二个章节名称");
            await sleep(300); // 等待
            console.log('✔️【章节文本】输入完成！');
        } else {
            console.log('❌没找到【章节文本】输入区');
        }

        const zhuanHuanChengZhangJieLieBiaoBTN = document.querySelector('.save-button');
        if (zhuanHuanChengZhangJieLieBiaoBTN) {
            zhuanHuanChengZhangJieLieBiaoBTN.click();
            await sleep(100); // 等待
            console.log('✔️【转换成章节列表】点击完成！');
        } else {
            console.log('❌没找到【转换成章节列表】按钮');
        }

        const boFangQiZhanShiZhangJieBTN = document.querySelector('.chapter-manager-checkbox');
        if (boFangQiZhanShiZhangJieBTN) {
            boFangQiZhanShiZhangJieBTN.click();
            await sleep(100); // 等待
            console.log('✔️【播放器展示章节】点击完成！');
        } else {
            console.log('❌没找到【播放器展示章节】按钮');
        }

        const baoCunBTN = document.querySelector('.bili-button.chapter-manager-save-btn.bili-button--primary.middle');
        if (baoCunBTN) {
            baoCunBTN.click();
            await sleep(100); // 等待
            console.log('✔️【章节保存】点击完成！');
        } else {
            console.log('❌没找到【章节保存】按钮');
        }
    }
})();
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
