---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
rent table

id
orderNo ^6eZzj6LJ

ceres ^r0rzHWc8

receipt table ^gOjZlN4t

payway1  xx amt
payway2 xx amt ^emjLNUnv

order iterm
insurace 3
rent 2
milage 33 ^DdwN90ky

order 
amt ^2iKfnP4K

orderItemId ^Jnxx32Gu

14195 ^cYAuyE6n

14196 ^TUGVxIL0

發票一定是一種payway
orderItem也是只有三種，可以拆掉
但是ride也可能30元裡面有10是credit 3 and 20 是 credit1

組起來會是 

rent: [{payway1 20}., {payway3, 10}
insurace:[payway1 10]


=> 因此，會需要另一個 table 記錄這個組合，這樣會比較好撈
但是目前不存在這個表，因此是 in mem 的方法去計算出來

正確作法是，在ceres那邊需要透過 credit 去 dedut 和搞出不同的
order item下的payway的 source of truth 那邊，就需要去
順便把這個結構存起來


好奇...我需要這個結構做什麼？ -> 結果需要的 XD
我覺得....
還是不要一直想要幫前端把東西組好，這會讓系統過度複雜
-> 想到 Jack說的，該給的給他們就好，後端應該提供的比較面相資料面的結構。


 ^kQGNkGMM


# Embedded files
9de50ad27fc226d005f29440663c1857dfcb94a8: [[IMG-rent_receipt_item.excalidraw-20241003105606797.png]]
2a3fc11543c9317f942edb9893fbf4e8b11b1d83: [[IMG-rent_receipt_item.excalidraw-20241003105607694.png]]
5e48886d0a3cbee4ae43f02e959180145d878d7b: [[IMG-rent_receipt_item.excalidraw-20241003105608145.png]]

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.0.25",
	"elements": [
		{
			"id": "zNCMHVhRJ25iTJw4bib8r",
			"type": "rectangle",
			"x": -415.8091125488281,
			"y": -213.89706420898438,
			"width": 131.13961791992188,
			"height": 147.435791015625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"seed": 95971217,
			"version": 25,
			"versionNonce": 2077000511,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "6eZzj6LJ"
				}
			],
			"updated": 1715425714541,
			"link": null,
			"locked": false
		},
		{
			"id": "6eZzj6LJ",
			"type": "text",
			"x": -400.82926177978516,
			"y": -190.17916870117188,
			"width": 101.17991638183594,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 264620913,
			"version": 52,
			"versionNonce": 1470075953,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714541,
			"link": null,
			"locked": false,
			"text": "rent table\n\nid\norderNo",
			"rawText": "rent table\n\nid\norderNo",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 92,
			"containerId": "zNCMHVhRJ25iTJw4bib8r",
			"originalText": "rent table\n\nid\norderNo",
			"lineHeight": 1.25
		},
		{
			"type": "rectangle",
			"version": 184,
			"versionNonce": 586662751,
			"isDeleted": false,
			"id": "MtnctZIR0501k6whyYZO7",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -58.41123962402344,
			"y": -219.692626953125,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 206.93991088867188,
			"height": 147.435791015625,
			"seed": 186494719,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [],
			"updated": 1715425714541,
			"link": null,
			"locked": false
		},
		{
			"id": "rX4zOWSTUk3wKIE_177Ef",
			"type": "rectangle",
			"x": -153.04852294921875,
			"y": -335.4087371826172,
			"width": 481.289794921875,
			"height": 446.69337463378906,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"seed": 1841428959,
			"version": 30,
			"versionNonce": 2078270993,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1715425714542,
			"link": null,
			"locked": false
		},
		{
			"id": "r0rzHWc8",
			"type": "text",
			"x": -102.0953369140625,
			"y": -319.7366027832031,
			"width": 51.37994384765625,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1201097727,
			"version": 14,
			"versionNonce": 520486783,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "ceres",
			"rawText": "ceres",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 17,
			"containerId": null,
			"originalText": "ceres",
			"lineHeight": 1.25
		},
		{
			"id": "gOjZlN4t",
			"type": "text",
			"x": -40.53460693359375,
			"y": -200.82318115234375,
			"width": 123.3116455078125,
			"height": 24.26544189453125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 503952369,
			"version": 175,
			"versionNonce": 1006304241,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "receipt table",
			"rawText": "receipt table",
			"fontSize": 19.412353515625,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 16,
			"containerId": null,
			"originalText": "receipt table",
			"lineHeight": 1.25
		},
		{
			"id": "emjLNUnv",
			"type": "text",
			"x": -40.23760986328125,
			"y": -151.36898803710938,
			"width": 162.67990112304688,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2001403583,
			"version": 71,
			"versionNonce": 1379383199,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "payway1  xx amt\npayway2 xx amt",
			"rawText": "payway1  xx amt\npayway2 xx amt",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 42,
			"containerId": null,
			"originalText": "payway1  xx amt\npayway2 xx amt",
			"lineHeight": 1.25
		},
		{
			"type": "rectangle",
			"version": 220,
			"versionNonce": 1475892689,
			"isDeleted": false,
			"id": "_iK_vSAZxYXDiH0M6lzjw",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -50.25370788574219,
			"y": -51.588165283203125,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 206.93991088867188,
			"height": 147.435791015625,
			"seed": 726042609,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [],
			"updated": 1715425714542,
			"link": null,
			"locked": false
		},
		{
			"id": "DdwN90ky",
			"type": "text",
			"x": -17.527587890625,
			"y": -29.922393798828125,
			"width": 108.47988891601562,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 244325169,
			"version": 86,
			"versionNonce": 45968319,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "order iterm\ninsurace 3\nrent 2\nmilage 33",
			"rawText": "order iterm\ninsurace 3\nrent 2\nmilage 33",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 92,
			"containerId": null,
			"originalText": "order iterm\ninsurace 3\nrent 2\nmilage 33",
			"lineHeight": 1.25
		},
		{
			"id": "tydJ3_MCndxuBrVVQgQE7",
			"type": "rectangle",
			"x": 43.824951171875,
			"y": -306.94927978515625,
			"width": 175.67059326171875,
			"height": 68.28701782226562,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"seed": 1981619903,
			"version": 31,
			"versionNonce": 586118065,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "2iKfnP4K"
				}
			],
			"updated": 1715425714542,
			"link": null,
			"locked": false
		},
		{
			"id": "2iKfnP4K",
			"type": "text",
			"x": 101.36027526855469,
			"y": -297.80577087402344,
			"width": 60.599945068359375,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1698389407,
			"version": 24,
			"versionNonce": 1555536863,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "order \namt",
			"rawText": "order \namt",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 42,
			"containerId": "tydJ3_MCndxuBrVVQgQE7",
			"originalText": "order \namt",
			"lineHeight": 1.25
		},
		{
			"id": "A7EgHi82VjTubi00UPu3_",
			"type": "image",
			"x": -403.45611572265625,
			"y": 181.81283569335938,
			"width": 576,
			"height": 101,
			"angle": 0,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1035761695,
			"version": 28,
			"versionNonce": 554161553,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"status": "pending",
			"fileId": "9de50ad27fc226d005f29440663c1857dfcb94a8",
			"scale": [
				1,
				1
			]
		},
		{
			"id": "DfzKZ98AeOz2e8i1CuGPy",
			"type": "image",
			"x": -381.37939453125,
			"y": 336.8756408691406,
			"width": 1104,
			"height": 98,
			"angle": 0,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1767330463,
			"version": 49,
			"versionNonce": 876005375,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"status": "pending",
			"fileId": "2a3fc11543c9317f942edb9893fbf4e8b11b1d83",
			"scale": [
				1,
				1
			]
		},
		{
			"id": "Jnxx32Gu",
			"type": "text",
			"x": 177.34661865234375,
			"y": 212.40701293945312,
			"width": 118.67988586425781,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1064709087,
			"version": 41,
			"versionNonce": 1702427505,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "orderItemId",
			"rawText": "orderItemId",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 17,
			"containerId": null,
			"originalText": "orderItemId",
			"lineHeight": 1.25
		},
		{
			"id": "-bMElVflIUqAfVsQPOc0l",
			"type": "freedraw",
			"x": 138.3641357421875,
			"y": 250.94015502929688,
			"width": 14.74859619140625,
			"height": 2.9454345703125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 572973279,
			"version": 36,
			"versionNonce": 121335839,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-0.3743896484375
				],
				[
					0.370849609375,
					-0.74884033203125
				],
				[
					0.74169921875,
					-0.74884033203125
				],
				[
					2.22509765625,
					-0.74884033203125
				],
				[
					4.60357666015625,
					-0.74884033203125
				],
				[
					6.6112060546875,
					-0.74884033203125
				],
				[
					6.9820556640625,
					-0.74884033203125
				],
				[
					7.3529052734375,
					-0.74884033203125
				],
				[
					8.4654541015625,
					-0.007080078125
				],
				[
					10.47308349609375,
					1.45489501953125
				],
				[
					11.21478271484375,
					1.82574462890625
				],
				[
					11.58563232421875,
					2.19659423828125
				],
				[
					11.211181640625,
					2.19659423828125
				],
				[
					10.46234130859375,
					2.19659423828125
				],
				[
					9.3426513671875,
					2.19659423828125
				],
				[
					8.22296142578125,
					2.19659423828125
				],
				[
					6.208251953125,
					2.19659423828125
				],
				[
					5.08856201171875,
					2.19659423828125
				],
				[
					4.714111328125,
					2.19659423828125
				],
				[
					4.3397216796875,
					2.19659423828125
				],
				[
					6.7181396484375,
					2.19659423828125
				],
				[
					7.83074951171875,
					2.19659423828125
				],
				[
					11.104248046875,
					2.19659423828125
				],
				[
					14.37774658203125,
					2.19659423828125
				],
				[
					14.74859619140625,
					1.82220458984375
				],
				[
					14.74859619140625,
					1.82220458984375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				14.74859619140625,
				1.82220458984375
			]
		},
		{
			"id": "mz4RUDnHgzZ5yNxgol5U-",
			"type": "freedraw",
			"x": 141.60552978515625,
			"y": 265.9775695800781,
			"width": 14.0283203125,
			"height": 3.88330078125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 861228127,
			"version": 24,
			"versionNonce": 814376273,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.74169921875,
					0
				],
				[
					1.48345947265625,
					0
				],
				[
					3.49102783203125,
					0
				],
				[
					4.97442626953125,
					0
				],
				[
					5.7161865234375,
					0
				],
				[
					6.0870361328125,
					0
				],
				[
					8.4654541015625,
					0.54559326171875
				],
				[
					9.5780029296875,
					1.28729248046875
				],
				[
					10.31976318359375,
					1.65814208984375
				],
				[
					11.06146240234375,
					2.02899169921875
				],
				[
					12.5447998046875,
					3.1416015625
				],
				[
					13.657470703125,
					3.512451171875
				],
				[
					14.0283203125,
					3.88330078125
				],
				[
					14.0283203125,
					3.88330078125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				14.0283203125,
				3.88330078125
			]
		},
		{
			"id": "o8EpJcOy-rM0GdjZ7TG6Z",
			"type": "image",
			"x": -251.036865234375,
			"y": 391.6457824707031,
			"width": 165,
			"height": 47,
			"angle": 0,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1781967633,
			"version": 51,
			"versionNonce": 1068897343,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"status": "pending",
			"fileId": "5e48886d0a3cbee4ae43f02e959180145d878d7b",
			"scale": [
				1,
				1
			]
		},
		{
			"id": "Oq_L2KfzOQ7xcSEg9za45",
			"type": "freedraw",
			"x": 463.31048583984375,
			"y": 421.2405090332031,
			"width": 11.21484375,
			"height": 4.0579833984375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 996236145,
			"version": 22,
			"versionNonce": 1301517105,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2.0076904296875,
					0
				],
				[
					4.3861083984375,
					1.28729248046875
				],
				[
					5.4986572265625,
					1.65814208984375
				],
				[
					5.8695068359375,
					1.65814208984375
				],
				[
					6.6112060546875,
					2.02899169921875
				],
				[
					6.9820556640625,
					2.02899169921875
				],
				[
					8.4654541015625,
					2.77069091796875
				],
				[
					8.8363037109375,
					3.1416015625
				],
				[
					10.47314453125,
					3.6871337890625
				],
				[
					10.843994140625,
					3.6871337890625
				],
				[
					11.21484375,
					4.0579833984375
				],
				[
					11.21484375,
					4.0579833984375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				11.21484375,
				4.0579833984375
			]
		},
		{
			"id": "vHHUtejwSMjS3cjooQ0ML",
			"type": "freedraw",
			"x": 419.41778564453125,
			"y": 418.0597229003906,
			"width": 1.6688232421875,
			"height": 16.495849609375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1422976031,
			"version": 18,
			"versionNonce": 898053215,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.370849609375
				],
				[
					-0.3743896484375,
					2.74932861328125
				],
				[
					-0.3743896484375,
					3.12017822265625
				],
				[
					-0.3743896484375,
					5.1278076171875
				],
				[
					-0.3743896484375,
					7.5062255859375
				],
				[
					-0.3743896484375,
					9.51385498046875
				],
				[
					-0.3743896484375,
					10.25555419921875
				],
				[
					-1.11962890625,
					11.73895263671875
				],
				[
					-1.6688232421875,
					13.74658203125
				],
				[
					-1.6688232421875,
					14.48828125
				],
				[
					-1.6688232421875,
					16.125
				],
				[
					-1.6688232421875,
					16.495849609375
				],
				[
					-1.6688232421875,
					16.495849609375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-1.6688232421875,
				16.495849609375
			]
		},
		{
			"id": "Yj3A14Es4-6LXgoVZ436M",
			"type": "freedraw",
			"x": 438.99102783203125,
			"y": 423.0341491699219,
			"width": 12.5447998046875,
			"height": 12.71246337890625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1291017727,
			"version": 54,
			"versionNonce": 821822737,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					0
				],
				[
					-0.748779296875,
					0
				],
				[
					-1.1231689453125,
					0
				],
				[
					-2.242919921875,
					1.11260986328125
				],
				[
					-2.6173095703125,
					1.48345947265625
				],
				[
					-2.99169921875,
					1.85430908203125
				],
				[
					-3.3662109375,
					2.59600830078125
				],
				[
					-4.1114501953125,
					3.33770751953125
				],
				[
					-4.66064453125,
					4.97442626953125
				],
				[
					-5.0350341796875,
					5.7161865234375
				],
				[
					-5.5841064453125,
					7.3529052734375
				],
				[
					-5.5841064453125,
					7.7237548828125
				],
				[
					-5.5841064453125,
					8.0946044921875
				],
				[
					-5.5841064453125,
					8.8363037109375
				],
				[
					-5.5841064453125,
					9.2071533203125
				],
				[
					-5.2132568359375,
					9.94891357421875
				],
				[
					-4.4715576171875,
					10.69061279296875
				],
				[
					-4.1007080078125,
					11.06146240234375
				],
				[
					-3.7298583984375,
					11.43231201171875
				],
				[
					-3.3590087890625,
					11.80316162109375
				],
				[
					-2.9881591796875,
					11.80316162109375
				],
				[
					-2.6173095703125,
					11.80316162109375
				],
				[
					-1.8756103515625,
					12.17401123046875
				],
				[
					-0.7630615234375,
					12.17401123046875
				],
				[
					1.6153564453125,
					12.17401123046875
				],
				[
					1.9862060546875,
					12.17401123046875
				],
				[
					3.993896484375,
					12.17401123046875
				],
				[
					5.1064453125,
					11.0543212890625
				],
				[
					5.84814453125,
					10.30902099609375
				],
				[
					6.218994140625,
					9.93463134765625
				],
				[
					6.58984375,
					9.93463134765625
				],
				[
					6.960693359375,
					9.185791015625
				],
				[
					6.960693359375,
					8.43695068359375
				],
				[
					6.960693359375,
					7.691650390625
				],
				[
					6.960693359375,
					7.3172607421875
				],
				[
					6.960693359375,
					6.94281005859375
				],
				[
					6.960693359375,
					6.1939697265625
				],
				[
					6.960693359375,
					5.44512939453125
				],
				[
					6.5863037109375,
					5.07073974609375
				],
				[
					6.5863037109375,
					4.6962890625
				],
				[
					5.83740234375,
					3.94744873046875
				],
				[
					5.4630126953125,
					3.57305908203125
				],
				[
					4.714111328125,
					2.453369140625
				],
				[
					4.714111328125,
					2.07891845703125
				],
				[
					4.3397216796875,
					1.330078125
				],
				[
					3.5909423828125,
					0.9556884765625
				],
				[
					2.100341796875,
					-0.16400146484375
				],
				[
					1.7259521484375,
					-0.5384521484375
				],
				[
					1.7259521484375,
					-0.5384521484375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				1.7259521484375,
				-0.5384521484375
			]
		},
		{
			"id": "6cvFHYR7OKPWBY84jI-cA",
			"type": "freedraw",
			"x": -34.42523193359375,
			"y": 423.2303161621094,
			"width": 0,
			"height": 14.83056640625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 419901535,
			"version": 14,
			"versionNonce": 1887391871,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.370849609375
				],
				[
					0,
					5.4700927734375
				],
				[
					0,
					7.8485107421875
				],
				[
					0,
					10.22698974609375
				],
				[
					0,
					12.60546875
				],
				[
					0,
					13.718017578125
				],
				[
					0,
					14.459716796875
				],
				[
					0,
					14.83056640625
				],
				[
					0,
					14.83056640625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				0,
				14.83056640625
			]
		},
		{
			"id": "Uj8tuJxDQ37SVVZExuNf6",
			"type": "freedraw",
			"x": -13.41131591796875,
			"y": 425.8084411621094,
			"width": 18.03631591796875,
			"height": 19.25225830078125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1929644479,
			"version": 63,
			"versionNonce": 1446722289,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.37445068359375,
					0
				],
				[
					-0.74884033203125,
					0
				],
				[
					-1.494140625,
					0
				],
				[
					-1.8685302734375,
					0
				],
				[
					-2.61737060546875,
					0.74169921875
				],
				[
					-2.9918212890625,
					1.112548828125
				],
				[
					-2.9918212890625,
					1.854248046875
				],
				[
					-2.9918212890625,
					2.96685791015625
				],
				[
					-2.9918212890625,
					4.60357666015625
				],
				[
					-2.9918212890625,
					5.34527587890625
				],
				[
					-2.9918212890625,
					6.45782470703125
				],
				[
					-2.9918212890625,
					7.1995849609375
				],
				[
					-2.9918212890625,
					9.2071533203125
				],
				[
					-1.33367919921875,
					11.95648193359375
				],
				[
					-0.96282958984375,
					12.32733154296875
				],
				[
					-0.59197998046875,
					13.06903076171875
				],
				[
					0.52056884765625,
					14.18157958984375
				],
				[
					1.2623291015625,
					14.92327880859375
				],
				[
					1.6331787109375,
					14.92327880859375
				],
				[
					3.1165771484375,
					14.92327880859375
				],
				[
					4.7532958984375,
					14.92327880859375
				],
				[
					8.586669921875,
					14.0567626953125
				],
				[
					9.69921875,
					14.0567626953125
				],
				[
					10.070068359375,
					14.0567626953125
				],
				[
					10.811767578125,
					13.682373046875
				],
				[
					13.19024658203125,
					12.21319580078125
				],
				[
					14.67364501953125,
					11.46435546875
				],
				[
					15.04449462890625,
					11.0899658203125
				],
				[
					15.04449462890625,
					10.71551513671875
				],
				[
					15.04449462890625,
					9.9666748046875
				],
				[
					15.04449462890625,
					9.59228515625
				],
				[
					15.04449462890625,
					7.95196533203125
				],
				[
					15.04449462890625,
					7.5775146484375
				],
				[
					15.04449462890625,
					6.08343505859375
				],
				[
					15.04449462890625,
					5.3345947265625
				],
				[
					15.04449462890625,
					4.58929443359375
				],
				[
					15.04449462890625,
					4.21490478515625
				],
				[
					15.04449462890625,
					3.466064453125
				],
				[
					15.04449462890625,
					1.97552490234375
				],
				[
					15.04449462890625,
					0.8558349609375
				],
				[
					15.04449462890625,
					0.48138427734375
				],
				[
					15.04449462890625,
					0.10699462890625
				],
				[
					14.67010498046875,
					-0.2674560546875
				],
				[
					13.5504150390625,
					-0.641845703125
				],
				[
					11.164794921875,
					-2.11102294921875
				],
				[
					7.8841552734375,
					-3.20574951171875
				],
				[
					6.76446533203125,
					-3.20574951171875
				],
				[
					6.01922607421875,
					-3.58013916015625
				],
				[
					5.644775390625,
					-3.95458984375
				],
				[
					4.52154541015625,
					-3.95458984375
				],
				[
					3.40185546875,
					-4.3289794921875
				],
				[
					3.02740478515625,
					-4.3289794921875
				],
				[
					1.90771484375,
					-4.3289794921875
				],
				[
					1.5333251953125,
					-4.3289794921875
				],
				[
					1.15887451171875,
					-4.3289794921875
				],
				[
					-0.335205078125,
					-3.9581298828125
				],
				[
					-1.08050537109375,
					-3.5872802734375
				],
				[
					-1.08050537109375,
					-3.5872802734375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-1.08050537109375,
				-3.5872802734375
			]
		},
		{
			"id": "j0ti0MIl7djS-mfXrvjCv",
			"type": "freedraw",
			"x": 132.11669921875,
			"y": 420.8553771972656,
			"width": 4.289794921875,
			"height": 14.39910888671875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 328378687,
			"version": 19,
			"versionNonce": 710863007,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.37445068359375,
					-0.3743896484375
				],
				[
					-1.494140625,
					0.3673095703125
				],
				[
					-1.8685302734375,
					1.4798583984375
				],
				[
					-2.24298095703125,
					2.96331787109375
				],
				[
					-2.61737060546875,
					5.34173583984375
				],
				[
					-3.3662109375,
					6.45428466796875
				],
				[
					-3.9154052734375,
					8.832763671875
				],
				[
					-3.9154052734375,
					9.574462890625
				],
				[
					-3.9154052734375,
					10.68701171875
				],
				[
					-3.9154052734375,
					11.79962158203125
				],
				[
					-3.9154052734375,
					12.54132080078125
				],
				[
					-4.289794921875,
					13.65386962890625
				],
				[
					-4.289794921875,
					14.02471923828125
				],
				[
					-4.289794921875,
					14.02471923828125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-4.289794921875,
				14.02471923828125
			]
		},
		{
			"id": "gSB9BIR1dXNRjad_uCCxy",
			"type": "freedraw",
			"x": 143.2137451171875,
			"y": 425.1487731933594,
			"width": 16.35321044921875,
			"height": 20.63946533203125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2012793663,
			"version": 64,
			"versionNonce": 1574483153,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					0.370849609375
				],
				[
					-0.74884033203125,
					1.4833984375
				],
				[
					-0.74884033203125,
					2.595947265625
				],
				[
					-1.12322998046875,
					3.337646484375
				],
				[
					-1.12322998046875,
					4.079345703125
				],
				[
					-1.4976806640625,
					4.82110595703125
				],
				[
					-1.4976806640625,
					5.19195556640625
				],
				[
					-1.4976806640625,
					6.30450439453125
				],
				[
					-1.4976806640625,
					6.67535400390625
				],
				[
					-1.4976806640625,
					7.04620361328125
				],
				[
					-1.4976806640625,
					8.15875244140625
				],
				[
					-1.4976806640625,
					8.90045166015625
				],
				[
					-1.1268310546875,
					9.64215087890625
				],
				[
					-0.75592041015625,
					10.3839111328125
				],
				[
					0.35662841796875,
					11.1256103515625
				],
				[
					0.72747802734375,
					11.4964599609375
				],
				[
					1.46917724609375,
					12.2381591796875
				],
				[
					1.84002685546875,
					12.6090087890625
				],
				[
					2.95257568359375,
					13.7215576171875
				],
				[
					4.43597412109375,
					14.4632568359375
				],
				[
					5.548583984375,
					14.8341064453125
				],
				[
					6.290283203125,
					15.57586669921875
				],
				[
					6.6611328125,
					15.57586669921875
				],
				[
					7.773681640625,
					16.31756591796875
				],
				[
					8.88623046875,
					16.31756591796875
				],
				[
					9.257080078125,
					16.31756591796875
				],
				[
					9.99884033203125,
					15.572265625
				],
				[
					11.46087646484375,
					13.18670654296875
				],
				[
					11.83172607421875,
					12.812255859375
				],
				[
					11.83172607421875,
					12.4378662109375
				],
				[
					12.20257568359375,
					11.31817626953125
				],
				[
					12.94427490234375,
					10.198486328125
				],
				[
					12.94427490234375,
					9.44964599609375
				],
				[
					12.94427490234375,
					9.0751953125
				],
				[
					13.31512451171875,
					7.58111572265625
				],
				[
					13.86065673828125,
					5.19549560546875
				],
				[
					13.86065673828125,
					4.82110595703125
				],
				[
					13.86065673828125,
					4.4466552734375
				],
				[
					13.86065673828125,
					3.32696533203125
				],
				[
					13.11541748046875,
					2.5816650390625
				],
				[
					12.74102783203125,
					2.207275390625
				],
				[
					12.74102783203125,
					1.83282470703125
				],
				[
					12.36651611328125,
					1.45843505859375
				],
				[
					11.99212646484375,
					1.083984375
				],
				[
					10.87249755859375,
					-0.03570556640625
				],
				[
					10.49798583984375,
					-0.41009521484375
				],
				[
					9.74920654296875,
					-1.158935546875
				],
				[
					9.0003662109375,
					-1.53338623046875
				],
				[
					7.50982666015625,
					-2.653076171875
				],
				[
					6.760986328125,
					-2.653076171875
				],
				[
					6.01568603515625,
					-3.0274658203125
				],
				[
					4.89599609375,
					-3.0274658203125
				],
				[
					3.25567626953125,
					-3.57659912109375
				],
				[
					2.51043701171875,
					-3.57659912109375
				],
				[
					0.49566650390625,
					-3.57659912109375
				],
				[
					-0.6240234375,
					-4.3218994140625
				],
				[
					-1.74725341796875,
					-4.3218994140625
				],
				[
					-2.4925537109375,
					-4.3218994140625
				],
				[
					-2.4925537109375,
					-4.3218994140625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-2.4925537109375,
				-4.3218994140625
			]
		},
		{
			"id": "AdiLjJTLWSaa2oEp0ATFv",
			"type": "freedraw",
			"x": 104.5023193359375,
			"y": 263.8380432128906,
			"width": 6.10125732421875,
			"height": 19.12750244140625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1418885855,
			"version": 21,
			"versionNonce": 282492095,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1.11968994140625,
					1.112548828125
				],
				[
					-2.360595703125,
					6.5826416015625
				],
				[
					-2.360595703125,
					7.3243408203125
				],
				[
					-2.73504638671875,
					8.43695068359375
				],
				[
					-3.10943603515625,
					9.54949951171875
				],
				[
					-3.48388671875,
					10.66204833984375
				],
				[
					-3.8582763671875,
					12.669677734375
				],
				[
					-4.60711669921875,
					13.7822265625
				],
				[
					-4.60711669921875,
					14.153076171875
				],
				[
					-5.3524169921875,
					15.636474609375
				],
				[
					-5.726806640625,
					16.7490234375
				],
				[
					-6.10125732421875,
					18.75665283203125
				],
				[
					-6.10125732421875,
					19.12750244140625
				],
				[
					-6.10125732421875,
					19.12750244140625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-6.10125732421875,
				19.12750244140625
			]
		},
		{
			"id": "SRxMIF8HXj2hZ0p_ftNEa",
			"type": "freedraw",
			"x": 117.528564453125,
			"y": 274.2897033691406,
			"width": 18.6995849609375,
			"height": 14.13525390625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 642866399,
			"version": 60,
			"versionNonce": 1802236593,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					-0.3743896484375
				],
				[
					-1.12322998046875,
					0.3673095703125
				],
				[
					-1.4976806640625,
					0.7381591796875
				],
				[
					-1.8720703125,
					1.1090087890625
				],
				[
					-1.8720703125,
					1.8507080078125
				],
				[
					-1.8720703125,
					3.3341064453125
				],
				[
					-1.8720703125,
					3.7049560546875
				],
				[
					-1.8720703125,
					4.0758056640625
				],
				[
					-1.8720703125,
					5.18841552734375
				],
				[
					-1.501220703125,
					6.30096435546875
				],
				[
					-1.13037109375,
					6.67181396484375
				],
				[
					-0.759521484375,
					7.04266357421875
				],
				[
					-0.017822265625,
					7.78436279296875
				],
				[
					4.1864013671875,
					9.88470458984375
				],
				[
					6.1939697265625,
					10.43023681640625
				],
				[
					7.6773681640625,
					10.43023681640625
				],
				[
					8.41912841796875,
					10.8011474609375
				],
				[
					8.78997802734375,
					10.8011474609375
				],
				[
					9.90252685546875,
					10.8011474609375
				],
				[
					11.91009521484375,
					10.8011474609375
				],
				[
					15.18359375,
					10.8011474609375
				],
				[
					15.92535400390625,
					10.8011474609375
				],
				[
					16.29620361328125,
					10.05230712890625
				],
				[
					16.29620361328125,
					9.6778564453125
				],
				[
					16.29620361328125,
					9.303466796875
				],
				[
					16.29620361328125,
					8.55816650390625
				],
				[
					16.29620361328125,
					7.809326171875
				],
				[
					16.29620361328125,
					7.4349365234375
				],
				[
					15.74700927734375,
					5.79461669921875
				],
				[
					14.9981689453125,
					5.0457763671875
				],
				[
					14.623779296875,
					4.67132568359375
				],
				[
					13.87493896484375,
					3.5516357421875
				],
				[
					13.87493896484375,
					3.17724609375
				],
				[
					13.50048828125,
					2.80279541015625
				],
				[
					13.1260986328125,
					2.42840576171875
				],
				[
					12.75164794921875,
					2.053955078125
				],
				[
					12.75164794921875,
					1.6795654296875
				],
				[
					12.37725830078125,
					0.93426513671875
				],
				[
					12.0028076171875,
					0.55987548828125
				],
				[
					11.62841796875,
					0.1854248046875
				],
				[
					11.25396728515625,
					0.1854248046875
				],
				[
					10.87957763671875,
					-0.18896484375
				],
				[
					7.74871826171875,
					-0.56341552734375
				],
				[
					6.6290283203125,
					-0.56341552734375
				],
				[
					4.6142578125,
					-1.48699951171875
				],
				[
					4.2398681640625,
					-1.86138916015625
				],
				[
					3.49456787109375,
					-2.23583984375
				],
				[
					3.12017822265625,
					-2.23583984375
				],
				[
					2.7457275390625,
					-2.23583984375
				],
				[
					0.36016845703125,
					-2.78497314453125
				],
				[
					-0.388671875,
					-2.78497314453125
				],
				[
					-2.40338134765625,
					-3.3341064453125
				],
				[
					-2.40338134765625,
					-3.3341064453125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-2.40338134765625,
				-3.3341064453125
			]
		},
		{
			"id": "Qesjw9YYH7igKm8B88Igk",
			"type": "freedraw",
			"x": 110.85321044921875,
			"y": 241.44772338867188,
			"width": 31.28363037109375,
			"height": 20.650146484375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1847509951,
			"version": 79,
			"versionNonce": 1200000223,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.370849609375,
					-0.3743896484375
				],
				[
					1.112548828125,
					-0.3743896484375
				],
				[
					1.854248046875,
					-0.3743896484375
				],
				[
					2.22509765625,
					-0.3743896484375
				],
				[
					2.966796875,
					-0.3743896484375
				],
				[
					5.34527587890625,
					-0.3743896484375
				],
				[
					7.7237548828125,
					-0.3743896484375
				],
				[
					9.2071533203125,
					-0.3743896484375
				],
				[
					9.5780029296875,
					-0.3743896484375
				],
				[
					10.3197021484375,
					-0.3743896484375
				],
				[
					11.4322509765625,
					-0.0035400390625
				],
				[
					12.17401123046875,
					-0.0035400390625
				],
				[
					12.91571044921875,
					0.3673095703125
				],
				[
					13.28656005859375,
					0.7381591796875
				],
				[
					15.66497802734375,
					1.4798583984375
				],
				[
					16.40673828125,
					1.8507080078125
				],
				[
					16.777587890625,
					1.8507080078125
				],
				[
					17.1484375,
					2.2215576171875
				],
				[
					17.89013671875,
					2.96331787109375
				],
				[
					18.6318359375,
					3.70501708984375
				],
				[
					19.002685546875,
					4.07586669921875
				],
				[
					18.6282958984375,
					4.81756591796875
				],
				[
					18.25384521484375,
					5.18841552734375
				],
				[
					15.8682861328125,
					6.1048583984375
				],
				[
					15.49383544921875,
					6.4757080078125
				],
				[
					13.1082763671875,
					7.56689453125
				],
				[
					12.73382568359375,
					7.937744140625
				],
				[
					11.23974609375,
					8.30859375
				],
				[
					10.49090576171875,
					8.679443359375
				],
				[
					9.74560546875,
					9.421142578125
				],
				[
					9.3712158203125,
					9.7919921875
				],
				[
					8.62591552734375,
					9.7919921875
				],
				[
					7.5062255859375,
					10.162841796875
				],
				[
					6.76092529296875,
					10.162841796875
				],
				[
					7.13177490234375,
					10.162841796875
				],
				[
					9.139404296875,
					10.162841796875
				],
				[
					11.51788330078125,
					10.162841796875
				],
				[
					12.63043212890625,
					10.162841796875
				],
				[
					13.00128173828125,
					10.162841796875
				],
				[
					13.37213134765625,
					10.162841796875
				],
				[
					13.74298095703125,
					10.53369140625
				],
				[
					14.85552978515625,
					10.53369140625
				],
				[
					15.22637939453125,
					10.904541015625
				],
				[
					16.3389892578125,
					12.01708984375
				],
				[
					17.4515380859375,
					13.50054931640625
				],
				[
					17.8223876953125,
					13.87139892578125
				],
				[
					17.8223876953125,
					14.61309814453125
				],
				[
					17.8223876953125,
					14.98394775390625
				],
				[
					17.8223876953125,
					15.35479736328125
				],
				[
					17.8223876953125,
					15.72564697265625
				],
				[
					17.8223876953125,
					16.46734619140625
				],
				[
					17.44793701171875,
					16.83819580078125
				],
				[
					16.6990966796875,
					17.20904541015625
				],
				[
					15.95025634765625,
					17.57989501953125
				],
				[
					12.48419189453125,
					18.44287109375
				],
				[
					12.10980224609375,
					18.44287109375
				],
				[
					11.364501953125,
					18.44287109375
				],
				[
					10.61566162109375,
					18.44287109375
				],
				[
					8.2301025390625,
					19.9049072265625
				],
				[
					6.21539306640625,
					19.9049072265625
				],
				[
					4.72479248046875,
					19.9049072265625
				],
				[
					4.35040283203125,
					20.2757568359375
				],
				[
					3.9759521484375,
					20.2757568359375
				],
				[
					2.85626220703125,
					20.2757568359375
				],
				[
					2.11102294921875,
					20.2757568359375
				],
				[
					0.9913330078125,
					20.2757568359375
				],
				[
					-1.394287109375,
					20.2757568359375
				],
				[
					-2.51397705078125,
					19.15606689453125
				],
				[
					-6.75384521484375,
					16.00738525390625
				],
				[
					-8.7685546875,
					14.91265869140625
				],
				[
					-10.40887451171875,
					14.36346435546875
				],
				[
					-11.15771484375,
					13.6146240234375
				],
				[
					-12.28094482421875,
					12.49139404296875
				],
				[
					-12.28094482421875,
					12.49139404296875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-12.28094482421875,
				12.49139404296875
			]
		},
		{
			"id": "ngXBN1LZ4I-S5ewVmShZh",
			"type": "freedraw",
			"x": 35.36663818359375,
			"y": 239.95004272460938,
			"width": 23.58489990234375,
			"height": 17.95428466796875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 121406783,
			"version": 62,
			"versionNonce": 1134326929,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.370849609375,
					-0.3743896484375
				],
				[
					0.74169921875,
					-0.3743896484375
				],
				[
					1.4833984375,
					-0.3743896484375
				],
				[
					1.854248046875,
					-0.3743896484375
				],
				[
					2.595947265625,
					-0.3743896484375
				],
				[
					6.24029541015625,
					-0.3743896484375
				],
				[
					6.9820556640625,
					-0.3743896484375
				],
				[
					8.9896240234375,
					-0.3743896484375
				],
				[
					9.7313232421875,
					-0.0035400390625
				],
				[
					10.4730224609375,
					0.3673095703125
				],
				[
					11.58563232421875,
					0.7381591796875
				],
				[
					11.95648193359375,
					1.1090087890625
				],
				[
					12.32733154296875,
					1.1090087890625
				],
				[
					13.06903076171875,
					1.4798583984375
				],
				[
					13.81072998046875,
					2.2215576171875
				],
				[
					14.55242919921875,
					3.33416748046875
				],
				[
					14.92327880859375,
					4.07586669921875
				],
				[
					14.92327880859375,
					4.44671630859375
				],
				[
					14.92327880859375,
					5.18841552734375
				],
				[
					14.92327880859375,
					5.55926513671875
				],
				[
					14.1744384765625,
					6.67181396484375
				],
				[
					13.800048828125,
					7.04266357421875
				],
				[
					12.6767578125,
					7.78436279296875
				],
				[
					11.18621826171875,
					8.1552734375
				],
				[
					10.81182861328125,
					8.1552734375
				],
				[
					10.4373779296875,
					8.1552734375
				],
				[
					10.8082275390625,
					8.1552734375
				],
				[
					11.1790771484375,
					8.1552734375
				],
				[
					13.18670654296875,
					8.1552734375
				],
				[
					14.29925537109375,
					8.526123046875
				],
				[
					15.04095458984375,
					8.526123046875
				],
				[
					15.41180419921875,
					8.89697265625
				],
				[
					15.78265380859375,
					9.267822265625
				],
				[
					15.78265380859375,
					9.638671875
				],
				[
					16.15350341796875,
					11.492919921875
				],
				[
					16.895263671875,
					12.60546875
				],
				[
					17.26611328125,
					13.34722900390625
				],
				[
					17.26611328125,
					13.71807861328125
				],
				[
					16.89166259765625,
					14.45977783203125
				],
				[
					16.51727294921875,
					14.83062744140625
				],
				[
					16.142822265625,
					15.20147705078125
				],
				[
					15.02313232421875,
					15.57232666015625
				],
				[
					12.6375732421875,
					16.117919921875
				],
				[
					10.2484130859375,
					17.20904541015625
				],
				[
					8.233642578125,
					17.20904541015625
				],
				[
					7.48480224609375,
					17.20904541015625
				],
				[
					5.4700927734375,
					17.20904541015625
				],
				[
					3.97955322265625,
					17.57989501953125
				],
				[
					3.6051025390625,
					17.57989501953125
				],
				[
					3.230712890625,
					17.57989501953125
				],
				[
					1.2159423828125,
					17.57989501953125
				],
				[
					-1.16961669921875,
					17.03076171875
				],
				[
					-3.55523681640625,
					16.107177734375
				],
				[
					-4.3040771484375,
					15.7327880859375
				],
				[
					-5.9443359375,
					15.18365478515625
				],
				[
					-6.31878662109375,
					14.8092041015625
				],
				[
					-6.31878662109375,
					14.8092041015625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-6.31878662109375,
				14.8092041015625
			]
		},
		{
			"id": "Hc5i3hkLsNp4Pq1YS4peh",
			"type": "freedraw",
			"x": 38.5545654296875,
			"y": 267.9709167480469,
			"width": 13.43988037109375,
			"height": 13.06903076171875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2107527327,
			"version": 33,
			"versionNonce": 704616703,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.74169921875,
					-0.3743896484375
				],
				[
					2.37841796875,
					-0.923583984375
				],
				[
					4.75689697265625,
					-2.018310546875
				],
				[
					7.1353759765625,
					-2.56744384765625
				],
				[
					7.5062255859375,
					-2.56744384765625
				],
				[
					7.8770751953125,
					-2.56744384765625
				],
				[
					8.6187744140625,
					-2.56744384765625
				],
				[
					8.9896240234375,
					-2.56744384765625
				],
				[
					9.3604736328125,
					-2.56744384765625
				],
				[
					10.4730224609375,
					-2.56744384765625
				],
				[
					11.2147216796875,
					-2.56744384765625
				],
				[
					11.5855712890625,
					-2.56744384765625
				],
				[
					12.32733154296875,
					-2.56744384765625
				],
				[
					13.06903076171875,
					-2.56744384765625
				],
				[
					13.43988037109375,
					-2.56744384765625
				],
				[
					13.0654296875,
					-2.56744384765625
				],
				[
					11.94573974609375,
					-1.82574462890625
				],
				[
					11.3966064453125,
					0.181884765625
				],
				[
					11.3966064453125,
					2.189453125
				],
				[
					10.64776611328125,
					3.30206298828125
				],
				[
					10.64776611328125,
					4.78546142578125
				],
				[
					10.2733154296875,
					5.52716064453125
				],
				[
					10.2733154296875,
					6.26885986328125
				],
				[
					9.89892578125,
					7.01055908203125
				],
				[
					9.89892578125,
					9.3890380859375
				],
				[
					9.52447509765625,
					10.1307373046875
				],
				[
					9.52447509765625,
					10.5015869140625
				],
				[
					9.52447509765625,
					10.5015869140625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				9.52447509765625,
				10.5015869140625
			]
		},
		{
			"id": "6xqVStzdrfwA3XhjoEN4T",
			"type": "freedraw",
			"x": 41.86724853515625,
			"y": 280.2982482910156,
			"width": 23.11419677734375,
			"height": 2.218017578125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1444175967,
			"version": 18,
			"versionNonce": 706853489,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.74169921875,
					0
				],
				[
					2.22515869140625,
					0
				],
				[
					3.70855712890625,
					0
				],
				[
					4.82110595703125,
					-0.3743896484375
				],
				[
					5.56280517578125,
					-0.3743896484375
				],
				[
					7.5704345703125,
					-0.3743896484375
				],
				[
					9.94891357421875,
					-1.6688232421875
				],
				[
					15.04815673828125,
					-2.218017578125
				],
				[
					19.2523193359375,
					-2.218017578125
				],
				[
					20.3648681640625,
					-2.218017578125
				],
				[
					22.37249755859375,
					-2.218017578125
				],
				[
					23.11419677734375,
					-2.218017578125
				],
				[
					23.11419677734375,
					-2.218017578125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				23.11419677734375,
				-2.218017578125
			]
		},
		{
			"id": "cYAuyE6n",
			"type": "text",
			"x": 212.17828369140625,
			"y": 258.2217712402344,
			"width": 48.179962158203125,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 882393695,
			"version": 41,
			"versionNonce": 1304568095,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"text": "14195",
			"rawText": "14195",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 17,
			"containerId": null,
			"originalText": "14195",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 89,
			"versionNonce": 2143503441,
			"isDeleted": false,
			"id": "TUGVxIL0",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 209.1597137451172,
			"y": 237.15151977539062,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 48.619964599609375,
			"height": 25,
			"seed": 1913875359,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1715425714542,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "14196",
			"rawText": "14196",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "14196",
			"lineHeight": 1.25,
			"baseline": 17
		},
		{
			"id": "ZJQMxAy6aZ5bHJMm-B2qr",
			"type": "freedraw",
			"x": 214.19659423828125,
			"y": 292.5221862792969,
			"width": 2.5924072265625,
			"height": 21.01385498046875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 542675505,
			"version": 10,
			"versionNonce": 1776490079,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425733527,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					6.92498779296875
				],
				[
					0,
					11.129150390625
				],
				[
					-0.549072265625,
					12.76593017578125
				],
				[
					-1.0982666015625,
					14.40264892578125
				],
				[
					-1.47265625,
					17.5228271484375
				],
				[
					-2.218017578125,
					19.9012451171875
				],
				[
					-2.5924072265625,
					21.01385498046875
				],
				[
					-2.5924072265625,
					21.01385498046875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-2.5924072265625,
				21.01385498046875
			]
		},
		{
			"id": "A5Yp8YKPPz1w6qBHPuaLX",
			"type": "freedraw",
			"x": 234.05865478515625,
			"y": 296.9688415527344,
			"width": 22.2655029296875,
			"height": 10.3197021484375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2137962737,
			"version": 17,
			"versionNonce": 286954623,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425734616,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					0
				],
				[
					-1.11962890625,
					0
				],
				[
					-1.494140625,
					0
				],
				[
					-2.242919921875,
					0.74169921875
				],
				[
					-4.10791015625,
					2.22509765625
				],
				[
					-4.10791015625,
					2.96685791015625
				],
				[
					-4.4822998046875,
					3.33770751953125
				],
				[
					-5.231201171875,
					4.45025634765625
				],
				[
					-5.97998046875,
					5.19195556640625
				],
				[
					-0.5098876953125,
					6.1083984375
				],
				[
					4.7818603515625,
					7.16387939453125
				],
				[
					12.081298828125,
					8.21942138671875
				],
				[
					15.9146728515625,
					10.3197021484375
				],
				[
					16.2855224609375,
					10.3197021484375
				],
				[
					16.2855224609375,
					10.3197021484375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				16.2855224609375,
				10.3197021484375
			]
		},
		{
			"id": "qiYTBYZfW_pyTgsIUfDqh",
			"type": "freedraw",
			"x": 247.55206298828125,
			"y": 298.5520935058594,
			"width": 9.8489990234375,
			"height": 23.3780517578125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1136931025,
			"version": 11,
			"versionNonce": 138559967,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425735287,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					-0.3743896484375
				],
				[
					-0.7489013671875,
					0.7381591796875
				],
				[
					-2.3927001953125,
					4.01165771484375
				],
				[
					-6.5006103515625,
					14.59527587890625
				],
				[
					-9.1002197265625,
					21.52020263671875
				],
				[
					-9.8489990234375,
					22.6328125
				],
				[
					-9.8489990234375,
					23.003662109375
				],
				[
					-9.1072998046875,
					22.25836181640625
				],
				[
					-9.1072998046875,
					22.25836181640625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-9.1072998046875,
				22.25836181640625
			]
		},
		{
			"id": "ZXtOtjlkyMpMkhCggMee6",
			"type": "freedraw",
			"x": 267.75640869140625,
			"y": 296.3626403808594,
			"width": 2.9417724609375,
			"height": 18.2930908203125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1651275121,
			"version": 10,
			"versionNonce": 2136295199,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425735991,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.3743896484375,
					2.00762939453125
				],
				[
					-0.9234619140625,
					7.1068115234375
				],
				[
					-1.47265625,
					12.2060546875
				],
				[
					-2.5673828125,
					14.58453369140625
				],
				[
					-2.9417724609375,
					16.06793212890625
				],
				[
					-2.9417724609375,
					17.5513916015625
				],
				[
					-2.9417724609375,
					18.2930908203125
				],
				[
					-2.9417724609375,
					18.2930908203125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-2.9417724609375,
				18.2930908203125
			]
		},
		{
			"id": "95a7jxlZHmw0KzmWTLMIl",
			"type": "freedraw",
			"x": 295.65252685546875,
			"y": 295.0503845214844,
			"width": 13.2330322265625,
			"height": 19.255859375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1981516849,
			"version": 36,
			"versionNonce": 835453855,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425737232,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.7489013671875,
					-0.3743896484375
				],
				[
					-1.123291015625,
					-0.74884033203125
				],
				[
					-1.4976806640625,
					-0.74884033203125
				],
				[
					-2.24658203125,
					-1.12322998046875
				],
				[
					-2.6209716796875,
					-1.12322998046875
				],
				[
					-4.8568115234375,
					0.73101806640625
				],
				[
					-7.456298828125,
					3.32696533203125
				],
				[
					-8.2052001953125,
					4.0687255859375
				],
				[
					-9.328369140625,
					5.5521240234375
				],
				[
					-9.328369140625,
					7.5596923828125
				],
				[
					-9.328369140625,
					9.04315185546875
				],
				[
					-9.328369140625,
					9.78485107421875
				],
				[
					-8.586669921875,
					10.52655029296875
				],
				[
					-8.2158203125,
					10.89739990234375
				],
				[
					-7.844970703125,
					11.26824951171875
				],
				[
					-7.47412109375,
					11.63909912109375
				],
				[
					-5.83740234375,
					11.63909912109375
				],
				[
					-0.1748046875,
					10.58001708984375
				],
				[
					0.5670166015625,
					9.8311767578125
				],
				[
					1.3087158203125,
					9.0859375
				],
				[
					2.0504150390625,
					8.33709716796875
				],
				[
					2.7921142578125,
					7.962646484375
				],
				[
					3.5338134765625,
					7.5882568359375
				],
				[
					3.9046630859375,
					6.84295654296875
				],
				[
					3.9046630859375,
					6.0941162109375
				],
				[
					3.9046630859375,
					5.348876953125
				],
				[
					3.9046630859375,
					4.60003662109375
				],
				[
					3.9046630859375,
					4.2255859375
				],
				[
					3.9046630859375,
					5.338134765625
				],
				[
					3.9046630859375,
					6.45074462890625
				],
				[
					3.9046630859375,
					10.6549072265625
				],
				[
					3.9046630859375,
					17.39093017578125
				],
				[
					3.9046630859375,
					18.13262939453125
				],
				[
					3.9046630859375,
					18.13262939453125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				3.9046630859375,
				18.13262939453125
			]
		},
		{
			"id": "EVicLfu1mFbVQmt3oy2Mz",
			"type": "freedraw",
			"x": 310.86468505859375,
			"y": 288.6424865722656,
			"width": 28.1705322265625,
			"height": 22.22625732421875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 430401457,
			"version": 34,
			"versionNonce": 1865329631,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425742400,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					3.83331298828125
				],
				[
					0,
					6.2117919921875
				],
				[
					0,
					8.2193603515625
				],
				[
					0,
					8.5902099609375
				],
				[
					0,
					8.9610595703125
				],
				[
					0,
					9.33197021484375
				],
				[
					1.4833984375,
					9.70281982421875
				],
				[
					10.2376708984375,
					9.70281982421875
				],
				[
					18.991943359375,
					9.70281982421875
				],
				[
					23.1961669921875,
					10.44451904296875
				],
				[
					25.2037353515625,
					10.9901123046875
				],
				[
					25.9454345703125,
					11.3609619140625
				],
				[
					27.4288330078125,
					12.4735107421875
				],
				[
					27.7996826171875,
					12.8443603515625
				],
				[
					28.1705322265625,
					13.2152099609375
				],
				[
					28.1705322265625,
					14.6986083984375
				],
				[
					28.1705322265625,
					16.70623779296875
				],
				[
					28.1705322265625,
					18.71380615234375
				],
				[
					28.1705322265625,
					19.08465576171875
				],
				[
					28.1705322265625,
					19.45556640625
				],
				[
					27.4217529296875,
					20.197265625
				],
				[
					26.302001953125,
					20.93896484375
				],
				[
					25.9276123046875,
					21.309814453125
				],
				[
					24.4371337890625,
					21.6806640625
				],
				[
					19.3306884765625,
					21.6806640625
				],
				[
					15.3048095703125,
					22.22625732421875
				],
				[
					14.5560302734375,
					22.22625732421875
				],
				[
					13.436279296875,
					22.22625732421875
				],
				[
					11.9456787109375,
					22.22625732421875
				],
				[
					9.5601806640625,
					22.22625732421875
				],
				[
					8.4404296875,
					22.22625732421875
				],
				[
					8.4404296875,
					22.22625732421875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				8.4404296875,
				22.22625732421875
			]
		},
		{
			"id": "A9wNYjk5P39x5p-ZGt9ts",
			"type": "freedraw",
			"x": 312.40155029296875,
			"y": 291.4274597167969,
			"width": 29.7860107421875,
			"height": 6.243896484375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 670886769,
			"version": 10,
			"versionNonce": 1772433695,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425743159,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1.112548828125,
					-0.37445068359375
				],
				[
					3.1201171875,
					-0.74884033203125
				],
				[
					5.4986572265625,
					-1.8436279296875
				],
				[
					9.7027587890625,
					-1.8436279296875
				],
				[
					22.11572265625,
					-4.090087890625
				],
				[
					29.0443115234375,
					-6.243896484375
				],
				[
					29.7860107421875,
					-6.243896484375
				],
				[
					29.7860107421875,
					-6.243896484375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				29.7860107421875,
				-6.243896484375
			]
		},
		{
			"id": "1Da-cB9FYRTr2hjt_0RUz",
			"type": "freedraw",
			"x": 203.85552978515625,
			"y": 283.7286682128906,
			"width": 60.670166015625,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1806729777,
			"version": 15,
			"versionNonce": 1771260671,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425744383,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1.63671875,
					0
				],
				[
					12.2203369140625,
					0
				],
				[
					26.4625244140625,
					0
				],
				[
					38.8753662109375,
					0
				],
				[
					45.803955078125,
					0
				],
				[
					50.9031982421875,
					0
				],
				[
					52.0157470703125,
					0
				],
				[
					52.7574462890625,
					0
				],
				[
					54.6116943359375,
					0
				],
				[
					55.724365234375,
					0
				],
				[
					59.5576171875,
					0
				],
				[
					60.670166015625,
					0
				],
				[
					60.670166015625,
					0
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				60.670166015625,
				0
			]
		},
		{
			"id": "b4nmCQOgrfgzAkGx480yk",
			"type": "freedraw",
			"x": 216.14715576171875,
			"y": 324.0411682128906,
			"width": 131.5675048828125,
			"height": 12.38079833984375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 884863569,
			"version": 30,
			"versionNonce": 1973482175,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425745727,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					3.2735595703125,
					-0.54913330078125
				],
				[
					13.8570556640625,
					-2.66729736328125
				],
				[
					26.27001953125,
					-3.854736328125
				],
				[
					36.8536376953125,
					-4.913818359375
				],
				[
					50.899658203125,
					-4.913818359375
				],
				[
					63.3126220703125,
					-4.913818359375
				],
				[
					75.7254638671875,
					-4.913818359375
				],
				[
					88.138427734375,
					-8.21942138671875
				],
				[
					94.171875,
					-9.27850341796875
				],
				[
					96.5504150390625,
					-9.27850341796875
				],
				[
					99.8238525390625,
					-9.82763671875
				],
				[
					101.8314208984375,
					-9.82763671875
				],
				[
					103.3148193359375,
					-10.2020263671875
				],
				[
					105.322509765625,
					-10.2020263671875
				],
				[
					106.43505859375,
					-10.94732666015625
				],
				[
					108.8134765625,
					-10.94732666015625
				],
				[
					110.667724609375,
					-11.32171630859375
				],
				[
					112.151123046875,
					-11.32171630859375
				],
				[
					113.2637939453125,
					-11.32171630859375
				],
				[
					113.6346435546875,
					-11.32171630859375
				],
				[
					117.4678955078125,
					-11.32171630859375
				],
				[
					121.672119140625,
					-11.32171630859375
				],
				[
					128.6007080078125,
					-12.38079833984375
				],
				[
					128.9715576171875,
					-12.38079833984375
				],
				[
					129.7132568359375,
					-12.38079833984375
				],
				[
					131.1966552734375,
					-12.38079833984375
				],
				[
					131.5675048828125,
					-12.38079833984375
				],
				[
					131.5675048828125,
					-12.38079833984375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				131.5675048828125,
				-12.38079833984375
			]
		},
		{
			"id": "rekyqhduAyA4WbPHkmPwQ",
			"type": "freedraw",
			"x": -48.9420166015625,
			"y": 430.1160583496094,
			"width": 44.78778076171875,
			"height": 25.735107421875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1406082705,
			"version": 28,
			"versionNonce": 477089343,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425749569,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					3.1737060546875,
					-4.985107421875
				],
				[
					4.8104248046875,
					-6.07989501953125
				],
				[
					13.375732421875,
					-9.29632568359375
				],
				[
					14.859130859375,
					-9.67071533203125
				],
				[
					15.9716796875,
					-10.4195556640625
				],
				[
					16.71343994140625,
					-10.4195556640625
				],
				[
					17.82598876953125,
					-10.4195556640625
				],
				[
					19.30938720703125,
					-10.4195556640625
				],
				[
					23.14276123046875,
					-9.556640625
				],
				[
					23.51361083984375,
					-7.70233154296875
				],
				[
					23.51361083984375,
					-6.58978271484375
				],
				[
					22.04443359375,
					-4.58221435546875
				],
				[
					21.29559326171875,
					-3.8404541015625
				],
				[
					18.3216552734375,
					-0.5028076171875
				],
				[
					14.0496826171875,
					5.36669921875
				],
				[
					12.58050537109375,
					7.7451171875
				],
				[
					10.74053955078125,
					10.12359619140625
				],
				[
					9.25,
					11.60699462890625
				],
				[
					7.75946044921875,
					13.09039306640625
				],
				[
					7.0106201171875,
					14.2030029296875
				],
				[
					6.26177978515625,
					15.3155517578125
				],
				[
					9.38189697265625,
					15.3155517578125
				],
				[
					19.96551513671875,
					15.3155517578125
				],
				[
					39.68853759765625,
					15.3155517578125
				],
				[
					44.78778076171875,
					14.76641845703125
				],
				[
					44.78778076171875,
					14.76641845703125
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				44.78778076171875,
				14.76641845703125
			]
		},
		{
			"id": "TAdoMqoETBAfSDnbe0Rz8",
			"type": "freedraw",
			"x": 115.14654541015625,
			"y": 423.3372497558594,
			"width": 25.37493896484375,
			"height": 21.80194091796875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1099311889,
			"version": 34,
			"versionNonce": 1422249599,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425751102,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.370849609375,
					-0.3743896484375
				],
				[
					1.112548828125,
					-0.74884033203125
				],
				[
					2.22515869140625,
					-0.74884033203125
				],
				[
					4.07940673828125,
					-1.4976806640625
				],
				[
					4.45025634765625,
					-1.4976806640625
				],
				[
					5.56280517578125,
					-1.4976806640625
				],
				[
					7.9412841796875,
					-1.4976806640625
				],
				[
					13.04052734375,
					-0.63470458984375
				],
				[
					21.7947998046875,
					2.34283447265625
				],
				[
					23.2781982421875,
					2.71368408203125
				],
				[
					24.0198974609375,
					3.08453369140625
				],
				[
					24.0198974609375,
					3.45538330078125
				],
				[
					24.0198974609375,
					4.56793212890625
				],
				[
					24.0198974609375,
					5.30963134765625
				],
				[
					24.0198974609375,
					6.4222412109375
				],
				[
					24.0198974609375,
					7.5347900390625
				],
				[
					22.90020751953125,
					8.6473388671875
				],
				[
					21.06024169921875,
					10.65496826171875
				],
				[
					19.5697021484375,
					11.02581787109375
				],
				[
					18.82086181640625,
					11.76751708984375
				],
				[
					17.701171875,
					12.13836669921875
				],
				[
					12.59478759765625,
					14.958984375
				],
				[
					10.20916748046875,
					16.7918701171875
				],
				[
					9.0894775390625,
					18.2752685546875
				],
				[
					9.4603271484375,
					19.3878173828125
				],
				[
					11.83880615234375,
					19.93341064453125
				],
				[
					19.1346435546875,
					19.93341064453125
				],
				[
					19.8763427734375,
					19.93341064453125
				],
				[
					22.25482177734375,
					20.30426025390625
				],
				[
					24.63323974609375,
					20.30426025390625
				],
				[
					25.37493896484375,
					20.30426025390625
				],
				[
					25.37493896484375,
					20.30426025390625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				25.37493896484375,
				20.30426025390625
			]
		},
		{
			"id": "5SRShahy-4wFal75n-tqL",
			"type": "freedraw",
			"x": 409.30853271484375,
			"y": 422.9378967285156,
			"width": 47.38720703125,
			"height": 25.4320068359375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 241489617,
			"version": 33,
			"versionNonce": 1631611551,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425753054,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.370849609375,
					0
				],
				[
					0.74169921875,
					0
				],
				[
					1.4833984375,
					0
				],
				[
					5.3167724609375,
					-2.107421875
				],
				[
					6.8001708984375,
					-2.48187255859375
				],
				[
					8.8077392578125,
					-2.48187255859375
				],
				[
					9.5494384765625,
					-2.48187255859375
				],
				[
					10.2911376953125,
					-2.48187255859375
				],
				[
					11.0328369140625,
					-1.36932373046875
				],
				[
					11.0328369140625,
					3.72991943359375
				],
				[
					12.08837890625,
					10.658447265625
				],
				[
					12.9512939453125,
					17.58343505859375
				],
				[
					12.9512939453125,
					17.95428466796875
				],
				[
					12.9512939453125,
					19.06689453125
				],
				[
					11.8280029296875,
					20.179443359375
				],
				[
					11.0792236328125,
					20.55029296875
				],
				[
					9.43896484375,
					21.09588623046875
				],
				[
					9.064453125,
					21.46673583984375
				],
				[
					8.6900634765625,
					21.83758544921875
				],
				[
					8.315673828125,
					22.20843505859375
				],
				[
					7.5704345703125,
					22.57928466796875
				],
				[
					7.1959228515625,
					22.95013427734375
				],
				[
					26.72998046875,
					22.81817626953125
				],
				[
					35.4842529296875,
					21.95166015625
				],
				[
					36.2259521484375,
					21.95166015625
				],
				[
					37.3385009765625,
					21.95166015625
				],
				[
					37.7093505859375,
					21.95166015625
				],
				[
					44.637939453125,
					21.95166015625
				],
				[
					47.016357421875,
					21.95166015625
				],
				[
					47.38720703125,
					21.95166015625
				],
				[
					47.38720703125,
					21.95166015625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				47.38720703125,
				21.95166015625
			]
		},
		{
			"id": "5cNuey0rq9OSiwfVJVIKu",
			"type": "freedraw",
			"x": 105.7147216796875,
			"y": 296.0987854003906,
			"width": 9.65289306640625,
			"height": 50.01531982421875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 456642737,
			"version": 9,
			"versionNonce": 881708991,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425754528,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.74884033203125,
					0
				],
				[
					-2.18231201171875,
					5.66265869140625
				],
				[
					-3.46246337890625,
					14.6094970703125
				],
				[
					-8.786376953125,
					34.33245849609375
				],
				[
					-9.65289306640625,
					44.91607666015625
				],
				[
					-9.65289306640625,
					50.01531982421875
				],
				[
					-9.65289306640625,
					50.01531982421875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-9.65289306640625,
				50.01531982421875
			]
		},
		{
			"id": "NcPh1ufgRX8Se2Sn9gnAj",
			"type": "freedraw",
			"x": 118.60906982421875,
			"y": 310.9471740722656,
			"width": 11.400146484375,
			"height": 23.40301513671875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1317286289,
			"version": 29,
			"versionNonce": 1465215839,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425755560,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-1.11968994140625
				],
				[
					-1.7330322265625,
					5.61627197265625
				],
				[
					-2.7921142578125,
					14.37060546875
				],
				[
					-2.7921142578125,
					18.57476806640625
				],
				[
					-2.0504150390625,
					20.0582275390625
				],
				[
					-0.9378662109375,
					21.1707763671875
				],
				[
					0.1746826171875,
					21.9124755859375
				],
				[
					0.9163818359375,
					22.2833251953125
				],
				[
					1.65814208984375,
					22.2833251953125
				],
				[
					4.03656005859375,
					21.908935546875
				],
				[
					5.52001953125,
					20.7891845703125
				],
				[
					6.632568359375,
					18.92425537109375
				],
				[
					7.374267578125,
					17.8045654296875
				],
				[
					7.7451171875,
					17.43011474609375
				],
				[
					8.6080322265625,
					13.58966064453125
				],
				[
					8.6080322265625,
					9.74920654296875
				],
				[
					8.6080322265625,
					5.90869140625
				],
				[
					8.6080322265625,
					3.89398193359375
				],
				[
					7.86279296875,
					1.50836181640625
				],
				[
					7.48834228515625,
					1.13397216796875
				],
				[
					6.3651123046875,
					-0.36016845703125
				],
				[
					5.99066162109375,
					-0.36016845703125
				],
				[
					4.867431640625,
					-0.73455810546875
				],
				[
					4.49298095703125,
					-0.73455810546875
				],
				[
					4.11859130859375,
					-0.73455810546875
				],
				[
					3.744140625,
					-0.73455810546875
				],
				[
					3.744140625,
					-0.73455810546875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				3.744140625,
				-0.73455810546875
			]
		},
		{
			"id": "4GBYYurAqt_-MQjNPWj30",
			"type": "freedraw",
			"x": 41.4892578125,
			"y": 295.8741149902344,
			"width": 41.31451416015625,
			"height": 32.08953857421875,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1204868081,
			"version": 42,
			"versionNonce": 423449759,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425757472,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.370849609375,
					0
				],
				[
					2.37847900390625,
					-1.0947265625
				],
				[
					3.12017822265625,
					-1.0947265625
				],
				[
					4.23272705078125,
					-1.0947265625
				],
				[
					4.60357666015625,
					-1.0947265625
				],
				[
					5.7161865234375,
					-0.35302734375
				],
				[
					6.4578857421875,
					1.13037109375
				],
				[
					7.54901123046875,
					3.13800048828125
				],
				[
					7.54901123046875,
					4.99224853515625
				],
				[
					7.54901123046875,
					7.3707275390625
				],
				[
					7.54901123046875,
					8.8541259765625
				],
				[
					7.54901123046875,
					9.5958251953125
				],
				[
					7.54901123046875,
					10.7083740234375
				],
				[
					6.8001708984375,
					11.45013427734375
				],
				[
					5.68048095703125,
					11.82098388671875
				],
				[
					3.294921875,
					12.36651611328125
				],
				[
					2.17523193359375,
					12.36651611328125
				],
				[
					0.534912109375,
					12.36651611328125
				],
				[
					0.16046142578125,
					12.36651611328125
				],
				[
					0.5313720703125,
					12.36651611328125
				],
				[
					0.9022216796875,
					12.36651611328125
				],
				[
					3.2806396484375,
					12.36651611328125
				],
				[
					8.3798828125,
					15.18719482421875
				],
				[
					10.75836181640625,
					16.4744873046875
				],
				[
					11.50006103515625,
					16.8453369140625
				],
				[
					11.87091064453125,
					17.2161865234375
				],
				[
					12.24176025390625,
					17.5870361328125
				],
				[
					12.24176025390625,
					20.70721435546875
				],
				[
					12.24176025390625,
					24.911376953125
				],
				[
					12.24176025390625,
					26.91900634765625
				],
				[
					12.24176025390625,
					27.66070556640625
				],
				[
					11.86737060546875,
					28.03155517578125
				],
				[
					10.7476806640625,
					28.03155517578125
				],
				[
					3.8155517578125,
					28.03155517578125
				],
				[
					-3.1165771484375,
					29.760986328125
				],
				[
					-17.36236572265625,
					29.760986328125
				],
				[
					-27.95306396484375,
					30.62396240234375
				],
				[
					-28.6983642578125,
					30.62396240234375
				],
				[
					-29.07275390625,
					30.99481201171875
				],
				[
					-29.07275390625,
					30.99481201171875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-29.07275390625,
				30.99481201171875
			]
		},
		{
			"id": "dt16B0ZPxzVGoOjWGbSON",
			"type": "freedraw",
			"x": -182.328125,
			"y": 309.3139953613281,
			"width": 97.91253662109375,
			"height": 6.88580322265625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 336578225,
			"version": 28,
			"versionNonce": 672440351,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425761525,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2.00762939453125,
					0.370849609375
				],
				[
					2.74932861328125,
					1.112548828125
				],
				[
					6.9534912109375,
					1.4833984375
				],
				[
					19.36285400390625,
					5.7696533203125
				],
				[
					31.7757568359375,
					5.7696533203125
				],
				[
					40.53363037109375,
					5.7696533203125
				],
				[
					42.54119873046875,
					4.30047607421875
				],
				[
					44.00323486328125,
					2.2857666015625
				],
				[
					45.6400146484375,
					2.2857666015625
				],
				[
					48.0184326171875,
					2.2857666015625
				],
				[
					50.39691162109375,
					2.2857666015625
				],
				[
					51.13861083984375,
					2.2857666015625
				],
				[
					55.15380859375,
					2.2857666015625
				],
				[
					57.53228759765625,
					1.736572265625
				],
				[
					64.4608154296875,
					-0.38153076171875
				],
				[
					65.9442138671875,
					-0.38153076171875
				],
				[
					66.3150634765625,
					-0.38153076171875
				],
				[
					71.260986328125,
					-0.38153076171875
				],
				[
					74.7235107421875,
					-0.38153076171875
				],
				[
					85.30706787109375,
					0.6739501953125
				],
				[
					87.685546875,
					0.6739501953125
				],
				[
					88.798095703125,
					0.6739501953125
				],
				[
					92.07159423828125,
					0.6739501953125
				],
				[
					97.17083740234375,
					-0.74169921875
				],
				[
					97.91253662109375,
					-1.11614990234375
				],
				[
					97.91253662109375,
					-1.11614990234375
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				97.91253662109375,
				-1.11614990234375
			]
		},
		{
			"id": "2l16VvxTMOaAWjnwFhU54",
			"type": "freedraw",
			"x": -215.4517822265625,
			"y": 295.1680603027344,
			"width": 16.460205078125,
			"height": 20.10455322265625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1399546673,
			"version": 29,
			"versionNonce": 1382473663,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425762920,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1.112548828125,
					0
				],
				[
					2.22509765625,
					0
				],
				[
					2.96685791015625,
					0.370849609375
				],
				[
					3.70855712890625,
					1.112548828125
				],
				[
					4.7996826171875,
					2.74932861328125
				],
				[
					5.71612548828125,
					5.12774658203125
				],
				[
					5.71612548828125,
					6.2403564453125
				],
				[
					5.71612548828125,
					6.9820556640625
				],
				[
					4.96728515625,
					7.3529052734375
				],
				[
					4.2220458984375,
					7.7237548828125
				],
				[
					1.83642578125,
					8.0946044921875
				],
				[
					2.207275390625,
					8.0946044921875
				],
				[
					9.1358642578125,
					10.7547607421875
				],
				[
					14.235107421875,
					12.16326904296875
				],
				[
					15.34765625,
					13.27587890625
				],
				[
					15.718505859375,
					13.646728515625
				],
				[
					16.08935546875,
					14.388427734375
				],
				[
					16.460205078125,
					15.5009765625
				],
				[
					16.460205078125,
					16.613525390625
				],
				[
					16.0858154296875,
					17.72607421875
				],
				[
					14.96612548828125,
					18.096923828125
				],
				[
					11.68548583984375,
					19.73370361328125
				],
				[
					9.67071533203125,
					19.73370361328125
				],
				[
					8.551025390625,
					20.10455322265625
				],
				[
					7.8057861328125,
					20.10455322265625
				],
				[
					7.43133544921875,
					20.10455322265625
				],
				[
					7.43133544921875,
					20.10455322265625
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				7.43133544921875,
				20.10455322265625
			]
		},
		{
			"id": "kr7GjRP4LTu3RgqNuUaiC",
			"type": "freedraw",
			"x": 345.51812744140625,
			"y": 295.9311828613281,
			"width": 580.0759887695312,
			"height": 26.829833984375,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1023811985,
			"version": 114,
			"versionNonce": 1258007039,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715425801792,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-10.5908203125,
					0.8629150390625
				],
				[
					-23.0108642578125,
					3.102294921875
				],
				[
					-35.430908203125,
					5.2169189453125
				],
				[
					-49.6766357421875,
					6.49346923828125
				],
				[
					-65.751708984375,
					7.6773681640625
				],
				[
					-94.6212158203125,
					7.6773681640625
				],
				[
					-128.97509765625,
					7.6773681640625
				],
				[
					-165.15478515625,
					7.6773681640625
				],
				[
					-197.68292236328125,
					7.6773681640625
				],
				[
					-226.552490234375,
					9.11798095703125
				],
				[
					-264.3724365234375,
					14.7449951171875
				],
				[
					-285.931884765625,
					14.7449951171875
				],
				[
					-309.31707763671875,
					14.7449951171875
				],
				[
					-338.1866455078125,
					16.18560791015625
				],
				[
					-368.88543701171875,
					19.0704345703125
				],
				[
					-392.2706298828125,
					20.46826171875
				],
				[
					-412.00079345703125,
					25.64593505859375
				],
				[
					-428.07586669921875,
					26.829833984375
				],
				[
					-442.3216552734375,
					26.829833984375
				],
				[
					-449.25732421875,
					26.829833984375
				],
				[
					-458.01873779296875,
					26.829833984375
				],
				[
					-468.6094970703125,
					26.829833984375
				],
				[
					-489.97991943359375,
					26.829833984375
				],
				[
					-502.39996337890625,
					26.829833984375
				],
				[
					-507.50634765625,
					26.829833984375
				],
				[
					-512.6127319335938,
					26.28070068359375
				],
				[
					-514.998291015625,
					25.90625
				],
				[
					-515.7435913085938,
					25.5318603515625
				],
				[
					-516.1179809570312,
					25.15740966796875
				],
				[
					-516.492431640625,
					24.0377197265625
				],
				[
					-518.1362915039062,
					20.757080078125
				],
				[
					-519.0028076171875,
					16.5457763671875
				],
				[
					-519.0028076171875,
					15.79693603515625
				],
				[
					-510.05596923828125,
					12.8621826171875
				],
				[
					-477.534912109375,
					6.94281005859375
				],
				[
					-430.39373779296875,
					2.22509765625
				],
				[
					-375.9388427734375,
					2.22509765625
				],
				[
					-317.828857421875,
					2.22509765625
				],
				[
					-263.3740234375,
					2.22509765625
				],
				[
					-208.92266845703125,
					2.22509765625
				],
				[
					-145.517333984375,
					2.22509765625
				],
				[
					-125.601806640625,
					2.22509765625
				],
				[
					-91.2550048828125,
					2.22509765625
				],
				[
					-44.306396484375,
					2.22509765625
				],
				[
					-20.9283447265625,
					2.22509765625
				],
				[
					-1.205322265625,
					2.22509765625
				],
				[
					7.5489501953125,
					2.22509765625
				],
				[
					9.0323486328125,
					2.22509765625
				],
				[
					9.4031982421875,
					2.22509765625
				],
				[
					10.1448974609375,
					2.22509765625
				],
				[
					11.62841796875,
					2.22509765625
				],
				[
					11.999267578125,
					2.22509765625
				],
				[
					-5.0101318359375,
					3.701416015625
				],
				[
					-43.022705078125,
					9.763427734375
				],
				[
					-92.0003662109375,
					17.66546630859375
				],
				[
					-153.7724609375,
					25.84564208984375
				],
				[
					-219.1995849609375,
					25.84564208984375
				],
				[
					-293.76617431640625,
					25.84564208984375
				],
				[
					-370.16204833984375,
					25.84564208984375
				],
				[
					-444.732177734375,
					17.5941162109375
				],
				[
					-491.88409423828125,
					17.5941162109375
				],
				[
					-517.0986328125,
					17.5941162109375
				],
				[
					-517.8438720703125,
					17.5941162109375
				],
				[
					-500.8416748046875,
					17.5941162109375
				],
				[
					-442.73175048828125,
					17.5941162109375
				],
				[
					-349.41204833984375,
					17.5941162109375
				],
				[
					-242.677490234375,
					15.59722900390625
				],
				[
					-138.5174560546875,
					15.59722900390625
				],
				[
					-63.9580078125,
					15.59722900390625
				],
				[
					-25.956298828125,
					17.037841796875
				],
				[
					-11.3468017578125,
					19.61602783203125
				],
				[
					-11.72119140625,
					19.61602783203125
				],
				[
					-31.4512939453125,
					19.61602783203125
				],
				[
					-82.25830078125,
					19.61602783203125
				],
				[
					-165.7003173828125,
					19.61602783203125
				],
				[
					-254.5555419921875,
					19.61602783203125
				],
				[
					-370.24053955078125,
					19.61602783203125
				],
				[
					-469.93597412109375,
					19.61602783203125
				],
				[
					-533.5338745117188,
					19.61602783203125
				],
				[
					-566.06201171875,
					15.572265625
				],
				[
					-568.0767211914062,
					15.02313232421875
				],
				[
					-562.7849731445312,
					15.02313232421875
				],
				[
					-521.12451171875,
					15.02313232421875
				],
				[
					-429.5628662109375,
					15.02313232421875
				],
				[
					-309.413330078125,
					15.02313232421875
				],
				[
					-193.7354736328125,
					15.02313232421875
				],
				[
					-109.287841796875,
					13.3115234375
				],
				[
					-51.1778564453125,
					13.3115234375
				],
				[
					-22.3154296875,
					13.3115234375
				],
				[
					-21.57373046875,
					13.682373046875
				],
				[
					-25.039794921875,
					14.5452880859375
				],
				[
					-61.2230224609375,
					11.54638671875
				],
				[
					-124.8209228515625,
					11.54638671875
				],
				[
					-218.218994140625,
					11.54638671875
				],
				[
					-334.6385498046875,
					11.54638671875
				],
				[
					-365.5263671875,
					11.54638671875
				],
				[
					-423.6434326171875,
					11.54638671875
				],
				[
					-465.31103515625,
					5.3524169921875
				],
				[
					-490.5255126953125,
					2.3927001953125
				],
				[
					-494.365966796875,
					1.15179443359375
				],
				[
					-493.9951171875,
					1.15179443359375
				],
				[
					-465.13275146484375,
					1.15179443359375
				],
				[
					-412.50714111328125,
					1.15179443359375
				],
				[
					-341.60272216796875,
					1.15179443359375
				],
				[
					-263.38470458984375,
					1.15179443359375
				],
				[
					-172.6396484375,
					1.15179443359375
				],
				[
					-70.8509521484375,
					1.15179443359375
				],
				[
					-27.36474609375,
					1.15179443359375
				],
				[
					-0.328125,
					2.49609375
				],
				[
					5.7054443359375,
					3.5516357421875
				],
				[
					6.0762939453125,
					3.5516357421875
				],
				[
					6.0762939453125,
					3.5516357421875
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				6.0762939453125,
				3.5516357421875
			]
		},
		{
			"id": "kQGNkGMM",
			"type": "text",
			"x": -154.0718994140625,
			"y": 477.3500061035156,
			"width": 723.719970703125,
			"height": 625,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1099227985,
			"version": 1222,
			"versionNonce": 1750968319,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1715427853248,
			"link": null,
			"locked": false,
			"text": "發票一定是一種payway\norderItem也是只有三種，可以拆掉\n但是ride也可能30元裡面有10是credit 3 and 20 是 credit1\n\n組起來會是 \n\nrent: [{payway1 20}., {payway3, 10}\ninsurace:[payway1 10]\n\n\n=> 因此，會需要另一個 table 記錄這個組合，這樣會比較好撈\n但是目前不存在這個表，因此是 in mem 的方法去計算出來\n\n正確作法是，在ceres那邊需要透過 credit 去 dedut 和搞出不同的\norder item下的payway的 source of truth 那邊，就需要去\n順便把這個結構存起來\n\n\n好奇...我需要這個結構做什麼？ -> 結果需要的 XD\n我覺得....\n還是不要一直想要幫前端把東西組好，這會讓系統過度複雜\n-> 想到 Jack說的，該給的給他們就好，後端應該提供的比較面相資料面的結構。\n\n\n",
			"rawText": "發票一定是一種payway\norderItem也是只有三種，可以拆掉\n但是ride也可能30元裡面有10是credit 3 and 20 是 credit1\n\n組起來會是 \n\nrent: [{payway1 20}., {payway3, 10}\ninsurace:[payway1 10]\n\n\n=> 因此，會需要另一個 table 記錄這個組合，這樣會比較好撈\n但是目前不存在這個表，因此是 in mem 的方法去計算出來\n\n正確作法是，在ceres那邊需要透過 credit 去 dedut 和搞出不同的\norder item下的payway的 source of truth 那邊，就需要去\n順便把這個結構存起來\n\n\n好奇...我需要這個結構做什麼？ -> 結果需要的 XD\n我覺得....\n還是不要一直想要幫前端把東西組好，這會讓系統過度複雜\n-> 想到 Jack說的，該給的給他們就好，後端應該提供的比較面相資料面的結構。\n\n\n",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 617,
			"containerId": null,
			"originalText": "發票一定是一種payway\norderItem也是只有三種，可以拆掉\n但是ride也可能30元裡面有10是credit 3 and 20 是 credit1\n\n組起來會是 \n\nrent: [{payway1 20}., {payway3, 10}\ninsurace:[payway1 10]\n\n\n=> 因此，會需要另一個 table 記錄這個組合，這樣會比較好撈\n但是目前不存在這個表，因此是 in mem 的方法去計算出來\n\n正確作法是，在ceres那邊需要透過 credit 去 dedut 和搞出不同的\norder item下的payway的 source of truth 那邊，就需要去\n順便把這個結構存起來\n\n\n好奇...我需要這個結構做什麼？ -> 結果需要的 XD\n我覺得....\n還是不要一直想要幫前端把東西組好，這會讓系統過度複雜\n-> 想到 Jack說的，該給的給他們就好，後端應該提供的比較面相資料面的結構。\n\n\n",
			"lineHeight": 1.25
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidth": 1,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 501.9434509277344,
		"scrollY": 158.9750518798828,
		"zoom": {
			"value": 1
		},
		"currentItemRoundness": "round",
		"gridSize": null,
		"gridColor": {
			"Bold": "#C9C9C9FF",
			"Regular": "#EDEDEDFF"
		},
		"currentStrokeOptions": null,
		"previousGridSize": null,
		"frameRendering": {
			"enabled": true,
			"clip": true,
			"name": true,
			"outline": true
		}
	},
	"files": {}
}
```
%%