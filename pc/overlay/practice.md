## 最佳实践
### 描述
我们可以利用[WAI-ARIA](http://www.w3.org/TR/wai-aria/)标准，它对于网页富交互指定了一系列描述性的attribute，通常用于:
1. 描述伪元素，例如利用`div`模拟的`button`。
2. 描述伪元素对应的交互行为，例如浮出层，对话框弹出，下拉列表展开。

本案例用到 :

- [`role`](http://www.w3.org/TR/wai-aria/roles#role_definitions): 用于描述伪元素的真实角色。
- `aria-pressed`: `button`这个`role`对应的`aria`状态，是否被按下,通常只用于切换状态按钮，本例始终为`false`。
- `aria-label`: 类似`title`，为不具备`title`属性的元素添加说明文案。
- `aria-labelledby`: 类似`label`的作用，指定说明文案的ID。
- `aria-hidden`: `dialog`这个`role`对应的`aria`状态，是否被隐藏。

这些状态属性会被读屏软件读到，更多的属性可以查看文档。

### 方案
触发按钮
```
<div id="J_Trigger"  tabindex="0" role="button"  aria-pressed="false" aria-label="回车打开浮层">按钮</div>
```

浮层结构

```
<div id="J_Overlay" role="dialog" tabindex="0" aria-labelledby="J_Header" style="display:none;" aria-hidden="true">
  <a href="javascript:void('close')" class="close" role="button" id="J_Close" title="关闭"></a>
  <div class="overlay-header" id="J_Header">浮层标题</div>
  <div class="overlay-body">
  </div>
  <div role="button" aria-pressed="false" class=" sure" id="J_Sure">确定</div>
</div>
```

脚本，这里采用JQuery的写法，点回车或者空格键触发按钮
```
$('#J_Trigger').on('click keypress ',function(e){
  if(e.type === 'keypress' && e.keyCode !== 13 && e.keyCode !== 32) return;
  $('#J_Overlay').attr('aria-hidden', false).show();
  $('.J_Close').focus();//定位到关闭按钮
});

$('.J_Close').on('click keypress ',function(e){
  if(e.type === 'keypress'  && e.keyCode !== 13 && e.keyCode !== 32) return;
  $('#J_Overlay').attr('aria-hidden', true).hide();
  $('#J_Trigger').focus();//关闭重新定位到trigger
});
```

**一般打开浮层先定位到关闭按钮，这个是每个浮层都有的。**

### demo
<a class="jsbin-embed" href="http://jsbin.com/fibozi/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.4"></script>

### 原则：
> 有焦点，有描述；哪里进，哪里出；能关闭，能手输。