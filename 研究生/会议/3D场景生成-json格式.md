## 电学

### 电路场景自动测试

1. 大模型生成程序计算电路中的电压、电流等等状态数值。
2. 程序计算结果与场景期望结果比较。

```json
{
	"prompt": "", //提示词
	"scene": {
		"device": [
			{
				"id": "", 
				"name": "", 
				"between": ["", "A", "", "C"], 
				"props": {
					"volt": 3.4,
					"resistance": 0,
					"resistance-precent": 0.5,
					"is-closed": true,
				}
			},
			...
		],
		"node": ["A", "B", "C", "D", ...]
	}
}
```
