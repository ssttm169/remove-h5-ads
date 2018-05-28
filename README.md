公司的H5页面挨嵌入各种各样的广告，有时有，有时没有，这个很烦人，问过许多大咖，有几种说法和几种解决方法





**1.DNS劫持 。**

HTML5页面被电信运营商进行DNS劫持植入广告，建议打电话给电信运营商。这招我试过，不管用，他们都在踢皮球，到底是运营商或代理商干的，，还是内部人干，很难追求到源头。





**2.HTTP劫持 。** 

可能也有这个原因，建议使用HTTPS， HTTPS成本不算高， 一年几百块至几千块的，能有效拦截嵌入广告。 但不彻底， 照样有嵌入广告的情况，具体原因不详。





**3.第三方包植入广告。**

不排除第三方的包有植入广告的代码，但这个不好找，一个项目的npm包，可能1000多个~~..





难道没有更好的办法了吗? 有~~这个是别人那里扒过来的

```html
<!-- 反运营商劫持 S -->

<style type="text/css">
    html {

        display:none;

    }
    </style>
<script>
    if( self == top ) {
        document.documentElement.style.display = 'block' ;

    } else {

        top.location = self.location ;

    }
    </script>

<!-- 反运营商劫持 E -->

```

但实际运用的过程，不起作用，可能是奸商改了规则~~。





经过反复研究，那些嵌入的代码，的确规则时常变化的，~~但有一样东西是不变的，就是iframe，每段嵌入代码都会iframe



```html
<html>
<body>
...
<!-- 嵌入的广告代码 开始  -->
<div id="xxx_id">
...
<iframe id="xxx" src="xx.com"...></iframe>
</div>
<!-- 嵌入的广告代码 结束  -->
</body>
</html>
```



所以我的代码是这样写的。

css代码

```css
/* 抢先 隐藏 iframe */
body iframe {display: none;}
```



js代码

```js
		var del_times = 0, deTimer = null;
		function adGo() {
			var iframe = document.getElementsByTagName('iframe')[0];
			if(iframe){
				console.log(iframe)
				var bodyNode = {tagName:''}, iframeParent, targetNode = iframe.parentNode;
				while (bodyNode.tagName != 'BODY'){
					bodyNode = targetNode;
					if(bodyNode.tagName != 'BODY'){
						iframeParent = targetNode;
						targetNode = targetNode.parentNode;
					}
				}
				if(iframeParent) //如果iframe有父类
					bodyNode.removeChild(iframeParent);
				else
					bodyNode.removeChild(iframe);
			}
			del_times++;
			if (del_times > 10) window.clearInterval(deTimer)
		}
		
		//抢先 删除 嵌入广告
		(function(){adGo();}())
		
		deTimer = self.setInterval(adGo, 200);
```

把代码加到```<body></body>```



经过一星期运行，效果棒棒的，妈妈再也不要担心我H5的弹出广告了。
