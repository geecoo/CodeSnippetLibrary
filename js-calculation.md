### JS 浮点型数据因精度问题导致的误差


```
var Calc =  {};
Calc = {
	/**
	* 浮点数求和
	*/
	add: function (a, b) {
		var c, d, e;
		try {
			c = a.toString().split(".")[1].length;
		} catch (f) {
			c = 0;
		}
		try {
			d = b.toString().split(".")[1].length;
		} catch (f) {
			d = 0;
		}
		return e = Math.pow(10, Math.max(c, d)), (this.mul(a, e) + this.mul(b, e)) / e;
	},

	/**
	* 浮点数相减
	*/
	sub: function (a, b) {
		var c, d, e;
		try {
			c = a.toString().split(".")[1].length;
		} catch (f) {
			c = 0;
		}
		try {
			d = b.toString().split(".")[1].length;
		} catch (f) {
			d = 0;
		}
		return e = Math.pow(10, Math.max(c, d)), (this.mul(a, e) - this.mul(b, e)) / e;
	},

	/**
	* 浮点数相乘
	*/
	mul: function (a, b) {
		var c = 0,
			d = a.toString(),
			e = b.toString();
		try {
			c += d.split(".")[1].length;
		} catch (f) {}
		try {
			c += e.split(".")[1].length;
		} catch (f) {}
		return Number(d.replace(".", "")) * Number(e.replace(".", "")) / Math.pow(10, c);
	},

	/**
	* 浮点数相除
	*/
	div: function (a, b) {
		var c, d, e = 0,
			f = 0;
		try {
			e = a.toString().split(".")[1].length;
		} catch (g) {}
		try {
			f = b.toString().split(".")[1].length;
		} catch (g) {}
		return c = Number(a.toString().replace(".", "")), d = Number(b.toString().replace(".", "")), this.mul(c / d, Math.pow(10, f - e));
	}


};
```

参考
    
[js浮点数计算误差bug的解释及解决方案](http://www.wdshare.org/article/56e7c94a0582906429d3126e)
    
[Javascript优化后的加减乘除（解决js浮点数计算bug）](https://blog.csdn.net/qinshenxue/article/details/43671763)
    
    
    
