---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
hermes ^prIPTE8N

u_${userID}r${rentId}
 ^Mb1P1xY1

box-service ^MAY8Svtr

"who": "u_${userID}r${rentId}", // req. ^LJnpGf7B

"until": "2024-02-08T10:08:53.000Z", // utc + 20 min ^PUoLkoZV

{ ^vqzGPORA

"as": "clientid",            // req.  ^qkShubZk

"reason": "return scooter",     // req. ^T1npS3GS

} ^FOWJaw7J

emqx ^gWDxXiqo

proto

message BanClientByIdRequest {
  string clientId = 1;
  string reason = 2;
  int32 untilInSec = 3
}

message BanClientByIdResponse {
  string result = 1;
}
 ^LKxPKBWU

grpc method: BanClientById ^HfG6C37Y

POST https://emqx-qat.wemoscooter.com/api/v5/banned ^V16lKoMm

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.0.17",
	"elements": [
		{
			"type": "rectangle",
			"version": 109,
			"versionNonce": 1622655389,
			"isDeleted": false,
			"id": "9xDPma0XIuawcnp2II2N8",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -408.6328125,
			"y": -253.17381286621094,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 101.770751953125,
			"height": 83.04037475585938,
			"seed": 259226643,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "prIPTE8N"
				},
				{
					"id": "S7hyRQbmSw8Cy1BxwXAN0",
					"type": "arrow"
				}
			],
			"updated": 1706767248904,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 90,
			"versionNonce": 2027188915,
			"isDeleted": false,
			"id": "prIPTE8N",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -391.0921630859375,
			"y": -223.15362548828125,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 66.689453125,
			"height": 23,
			"seed": 267393203,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248904,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "hermes",
			"rawText": "hermes",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "9xDPma0XIuawcnp2II2N8",
			"originalText": "hermes",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "arrow",
			"version": 481,
			"versionNonce": 1905406429,
			"isDeleted": false,
			"id": "S7hyRQbmSw8Cy1BxwXAN0",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -288.203125,
			"y": -202.09767267343784,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 455.471053470608,
			"height": 0.3287417589971824,
			"seed": 1553130333,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"boundElements": [],
			"updated": 1706768586778,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "9xDPma0XIuawcnp2II2N8",
				"gap": 18.658935546875,
				"focus": 0.23115641229546813
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					455.471053470608,
					-0.3287417589971824
				]
			]
		},
		{
			"type": "text",
			"version": 244,
			"versionNonce": 632923219,
			"isDeleted": false,
			"id": "Mb1P1xY1",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -408.26495361328125,
			"y": -384.9120864868164,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 187.919921875,
			"height": 46,
			"seed": 254143389,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "u_${userID}r${rentId}\n",
			"rawText": "u_${userID}r${rentId}\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "u_${userID}r${rentId}\n",
			"lineHeight": 1.15,
			"baseline": 41
		},
		{
			"type": "rectangle",
			"version": 167,
			"versionNonce": 1734327901,
			"isDeleted": false,
			"id": "-d7D7-XBPTKWtBRqfbMQQ",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 191.03912353515625,
			"y": -241.98046875,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 131.829345703125,
			"height": 83.04037475585938,
			"seed": 1728729981,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "MAY8Svtr"
				}
			],
			"updated": 1706767248905,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 183,
			"versionNonce": 239575539,
			"isDeleted": false,
			"id": "MAY8Svtr",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 205.82586669921875,
			"y": -211.9602813720703,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 102.255859375,
			"height": 23,
			"seed": 684514269,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "box-service",
			"rawText": "box-service",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "-d7D7-XBPTKWtBRqfbMQQ",
			"originalText": "box-service",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 268,
			"versionNonce": 351398589,
			"isDeleted": false,
			"id": "vqzGPORA",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.035888671875,
			"y": -47.18809509277344,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 6.6796875,
			"height": 23,
			"seed": 719264445,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "{",
			"rawText": "{",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "{",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 292,
			"versionNonce": 1535437715,
			"isDeleted": false,
			"id": "LJnpGf7B",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 291.694091796875,
			"y": -13.849563598632812,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 326.3671875,
			"height": 23,
			"seed": 1245887379,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "\"who\": \"u_${userID}r${rentId}\", // req.",
			"rawText": "\"who\": \"u_${userID}r${rentId}\", // req.",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "\"who\": \"u_${userID}r${rentId}\", // req.",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 274,
			"versionNonce": 1428931741,
			"isDeleted": false,
			"id": "qkShubZk",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.37451171875,
			"y": 18.470108032226562,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 251.81640625,
			"height": 23,
			"seed": 2097593117,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706773146070,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "\"as\": \"clientid\",            // req. ",
			"rawText": "\"as\": \"clientid\",            // req. ",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "\"as\": \"clientid\",            // req. ",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 268,
			"versionNonce": 1369964851,
			"isDeleted": false,
			"id": "T1npS3GS",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.035888671875,
			"y": 51.81190490722656,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 308.525390625,
			"height": 23,
			"seed": 1210831155,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "\"reason\": \"return scooter\",     // req.",
			"rawText": "\"reason\": \"return scooter\",     // req.",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "\"reason\": \"return scooter\",     // req.",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 289,
			"versionNonce": 2109349757,
			"isDeleted": false,
			"id": "PUoLkoZV",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.035888671875,
			"y": 84.81190490722656,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 457.001953125,
			"height": 23,
			"seed": 2115719037,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "\"until\": \"2024-02-08T10:08:53.000Z\", // utc + 20 min",
			"rawText": "\"until\": \"2024-02-08T10:08:53.000Z\", // utc + 20 min",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "\"until\": \"2024-02-08T10:08:53.000Z\", // utc + 20 min",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 268,
			"versionNonce": 1779917523,
			"isDeleted": false,
			"id": "FOWJaw7J",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.035888671875,
			"y": 150.81190490722656,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 6.6796875,
			"height": 23,
			"seed": 744380381,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "}",
			"rawText": "}",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "}",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "arrow",
			"version": 187,
			"versionNonce": 663350845,
			"isDeleted": false,
			"id": "wZZQ1bEY47-bpokiK00-U",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 243.93878173828125,
			"y": -137.5227813720703,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 1.99542236328125,
			"height": 353.2128601074219,
			"seed": 1328177715,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"boundElements": [],
			"updated": 1706768586778,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": {
				"elementId": "ObY96EpYre-VpVjNUuCfK",
				"gap": 20.270217895507812,
				"focus": -0.02018635148391448
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					1.99542236328125,
					353.2128601074219
				]
			]
		},
		{
			"type": "rectangle",
			"version": 161,
			"versionNonce": 761892979,
			"isDeleted": false,
			"id": "ObY96EpYre-VpVjNUuCfK",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 181.70391845703125,
			"y": 235.96029663085938,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 131.829345703125,
			"height": 83.04037475585938,
			"seed": 1550778205,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "gWDxXiqo"
				},
				{
					"id": "wZZQ1bEY47-bpokiK00-U",
					"type": "arrow"
				}
			],
			"updated": 1706767248905,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 178,
			"versionNonce": 1678517309,
			"isDeleted": false,
			"id": "gWDxXiqo",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 223.16546630859375,
			"y": 265.98048400878906,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 48.90625,
			"height": 23,
			"seed": 141626301,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "emqx",
			"rawText": "emqx",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "ObY96EpYre-VpVjNUuCfK",
			"originalText": "emqx",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 263,
			"versionNonce": 260642077,
			"isDeleted": false,
			"id": "LKxPKBWU",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -203.03717041015625,
			"y": -172.70835876464844,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 315.7421875,
			"height": 276,
			"seed": 834930131,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706768187490,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "proto\n\nmessage BanClientByIdRequest {\n  string clientId = 1;\n  string reason = 2;\n  int32 untilInSec = 3\n}\n\nmessage BanClientByIdResponse {\n  string result = 1;\n}\n",
			"rawText": "proto\n\nmessage BanClientByIdRequest {\n  string clientId = 1;\n  string reason = 2;\n  int32 untilInSec = 3\n}\n\nmessage BanClientByIdResponse {\n  string result = 1;\n}\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "proto\n\nmessage BanClientByIdRequest {\n  string clientId = 1;\n  string reason = 2;\n  int32 untilInSec = 3\n}\n\nmessage BanClientByIdResponse {\n  string result = 1;\n}\n",
			"lineHeight": 1.15,
			"baseline": 270
		},
		{
			"type": "text",
			"version": 253,
			"versionNonce": 733214973,
			"isDeleted": false,
			"id": "HfG6C37Y",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -173.18365478515625,
			"y": -255.67381286621094,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 249.0234375,
			"height": 23,
			"seed": 1106957363,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "grpc method: BanClientById",
			"rawText": "grpc method: BanClientById",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "grpc method: BanClientById",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 118,
			"versionNonce": 1143329117,
			"isDeleted": false,
			"id": "V16lKoMm",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 292.23974609375,
			"y": -78.99089050292969,
			"strokeColor": "#f08c00",
			"backgroundColor": "transparent",
			"width": 501.3671875,
			"height": 23,
			"seed": 907450301,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": "https://emqx-qat.wemoscooter.com/api/v5/banned",
			"locked": false,
			"fontSize": 20,
			"fontFamily": 2,
			"text": "POST https://emqx-qat.wemoscooter.com/api/v5/banned",
			"rawText": "POST https://emqx-qat.wemoscooter.com/api/v5/banned",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "POST https://emqx-qat.wemoscooter.com/api/v5/banned",
			"lineHeight": 1.15,
			"baseline": 18
		},
		{
			"type": "freedraw",
			"version": 83,
			"versionNonce": 897424829,
			"isDeleted": false,
			"id": "JYvYWeTRmYExXux7M3PCb",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -11.1751708984375,
			"y": -233.80532836914062,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 100.8333740234375,
			"height": 17.161468505859375,
			"seed": 1592702237,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767248905,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1.015625,
					0.3385467529296875
				],
				[
					3.1868896484375,
					0.3385467529296875
				],
				[
					4.541015625,
					0.3385467529296875
				],
				[
					4.87957763671875,
					0.3385467529296875
				],
				[
					5.2181396484375,
					0.3385467529296875
				],
				[
					6.2337646484375,
					0.3385467529296875
				],
				[
					6.91082763671875,
					0.3385467529296875
				],
				[
					7.92645263671875,
					0.3385467529296875
				],
				[
					8.2650146484375,
					0.3385467529296875
				],
				[
					8.603515625,
					0.3385467529296875
				],
				[
					8.94207763671875,
					0.3385467529296875
				],
				[
					10.2962646484375,
					0.3385467529296875
				],
				[
					11.7904052734375,
					0.3385467529296875
				],
				[
					13.96160888671875,
					0.3385467529296875
				],
				[
					14.97723388671875,
					0.3385467529296875
				],
				[
					15.654296875,
					0.3385467529296875
				],
				[
					15.99285888671875,
					0.3385467529296875
				],
				[
					16.3314208984375,
					0.3385467529296875
				],
				[
					16.669921875,
					0.3385467529296875
				],
				[
					18.8411865234375,
					0.3385467529296875
				],
				[
					23.49609375,
					1.1262969970703125
				],
				[
					24.1732177734375,
					1.1262969970703125
				],
				[
					26.005859375,
					1.1262969970703125
				],
				[
					27.36004638671875,
					1.1262969970703125
				],
				[
					27.6986083984375,
					1.1262969970703125
				],
				[
					29.53125,
					1.1262969970703125
				],
				[
					33.369140625,
					1.1262969970703125
				],
				[
					34.0462646484375,
					1.1262969970703125
				],
				[
					35.87890625,
					1.1262969970703125
				],
				[
					37.71160888671875,
					1.1262969970703125
				],
				[
					38.0501708984375,
					1.1262969970703125
				],
				[
					38.72723388671875,
					1.1262969970703125
				],
				[
					40.0814208984375,
					1.1262969970703125
				],
				[
					40.75848388671875,
					1.1262969970703125
				],
				[
					47.0833740234375,
					2.1223907470703125
				],
				[
					56.74481201171875,
					3.0859375
				],
				[
					58.23895263671875,
					3.0859375
				],
				[
					62.07684326171875,
					3.0859375
				],
				[
					63.09246826171875,
					3.0859375
				],
				[
					63.4310302734375,
					3.0859375
				],
				[
					64.78515625,
					3.0859375
				],
				[
					65.80078125,
					3.4244842529296875
				],
				[
					66.4779052734375,
					3.4244842529296875
				],
				[
					66.81640625,
					3.7630157470703125
				],
				[
					67.4935302734375,
					4.1015625
				],
				[
					68.5091552734375,
					4.4401092529296875
				],
				[
					68.84765625,
					4.4401092529296875
				],
				[
					71.0189208984375,
					4.4401092529296875
				],
				[
					72.5130615234375,
					4.4401092529296875
				],
				[
					73.5286865234375,
					4.4401092529296875
				],
				[
					75.0228271484375,
					4.4401092529296875
				],
				[
					75.69989013671875,
					4.0983123779296875
				],
				[
					76.0384521484375,
					3.7565155029296875
				],
				[
					77.0540771484375,
					2.734375
				],
				[
					79.22528076171875,
					1.552734375
				],
				[
					79.90234375,
					1.2109375
				],
				[
					81.396484375,
					0.2115936279296875
				],
				[
					81.73504638671875,
					0.2115936279296875
				],
				[
					82.412109375,
					0.2115936279296875
				],
				[
					83.0892333984375,
					0.2115936279296875
				],
				[
					83.427734375,
					0.2115936279296875
				],
				[
					85.5989990234375,
					0.2115936279296875
				],
				[
					86.6146240234375,
					0.2115936279296875
				],
				[
					87.29168701171875,
					0.2115936279296875
				],
				[
					88.30731201171875,
					0.2115936279296875
				],
				[
					88.984375,
					-0.1302032470703125
				],
				[
					89.6614990234375,
					-0.4720001220703125
				],
				[
					90.6771240234375,
					-1.1555938720703125
				],
				[
					93.7239990234375,
					-4.208984375
				],
				[
					94.7396240234375,
					-4.892578125
				],
				[
					95.41668701171875,
					-5.9147186279296875
				],
				[
					95.41668701171875,
					-6.2565155029296875
				],
				[
					95.7552490234375,
					-6.5983123779296875
				],
				[
					96.43231201171875,
					-6.9401092529296875
				],
				[
					96.7708740234375,
					-7.9622344970703125
				],
				[
					97.44793701171875,
					-8.984375
				],
				[
					99.8177490234375,
					-11.357421875
				],
				[
					100.49481201171875,
					-12.379562377929688
				],
				[
					100.8333740234375,
					-12.721359252929688
				],
				[
					100.8333740234375,
					-12.721359252929688
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 69,
			"versionNonce": 2015690653,
			"isDeleted": false,
			"id": "62_Qp8-uty0ft-QHxwHbL",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 32.783203125,
			"y": -259.2838439941406,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 309.12762451171875,
			"height": 90.9765625,
			"seed": 947740211,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767250676,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.341796875,
					0
				],
				[
					-2.51953125,
					0
				],
				[
					-5.68359375,
					0
				],
				[
					-10.3450927734375,
					0
				],
				[
					-16.67645263671875,
					-1.4680938720703125
				],
				[
					-21.51043701171875,
					-2.4348907470703125
				],
				[
					-38.35614013671875,
					-8.336593627929688
				],
				[
					-56.3671875,
					-14.147140502929688
				],
				[
					-69.5443115234375,
					-18.102218627929688
				],
				[
					-94.2318115234375,
					-25.963546752929688
				],
				[
					-122.255859375,
					-28.6328125
				],
				[
					-132.09637451171875,
					-31.09375
				],
				[
					-155.11395263671875,
					-33.642578125
				],
				[
					-168.12176513671875,
					-34.7265625
				],
				[
					-172.95574951171875,
					-36.66015625
				],
				[
					-182.62371826171875,
					-37.626953125
				],
				[
					-192.29168701171875,
					-38.59375
				],
				[
					-198.79559326171875,
					-39.677734375
				],
				[
					-208.46356201171875,
					-41.845703125
				],
				[
					-214.79168701171875,
					-42.63671875
				],
				[
					-217.95574951171875,
					-43.427734375
				],
				[
					-223.4700927734375,
					-45.703125
				],
				[
					-225.6478271484375,
					-46.044921875
				],
				[
					-228.8118896484375,
					-46.8359375
				],
				[
					-234.326171875,
					-48.14453125
				],
				[
					-240.654296875,
					-50.517578125
				],
				[
					-243.818359375,
					-51.30859375
				],
				[
					-254.189453125,
					-56.376953125
				],
				[
					-259.5540771484375,
					-60.27668762207031
				],
				[
					-263.4212646484375,
					-63.17707824707031
				],
				[
					-271.4193115234375,
					-64.93489074707031
				],
				[
					-276.78387451171875,
					-68.33659362792969
				],
				[
					-279.94793701171875,
					-69.12760925292969
				],
				[
					-283.818359375,
					-72.001953125
				],
				[
					-290.7357177734375,
					-77.33723449707031
				],
				[
					-294.6029052734375,
					-81.20442199707031
				],
				[
					-300.1400146484375,
					-85.15950012207031
				],
				[
					-303.193359375,
					-88.212890625
				],
				[
					-303.87371826171875,
					-88.5546875
				],
				[
					-304.5540771484375,
					-88.896484375
				],
				[
					-304.8958740234375,
					-89.23828125
				],
				[
					-305.2376708984375,
					-89.580078125
				],
				[
					-305.5794677734375,
					-89.921875
				],
				[
					-305.9212646484375,
					-89.921875
				],
				[
					-306.943359375,
					-88.56770324707031
				],
				[
					-308.44403076171875,
					-85.57942199707031
				],
				[
					-308.78582763671875,
					-84.90234375
				],
				[
					-308.78582763671875,
					-84.56379699707031
				],
				[
					-309.12762451171875,
					-83.54817199707031
				],
				[
					-309.12762451171875,
					-83.20964050292969
				],
				[
					-308.7890625,
					-83.88996887207031
				],
				[
					-307.79296875,
					-85.38736724853516
				],
				[
					-307.4544677734375,
					-85.72916412353516
				],
				[
					-306.9564208984375,
					-87.568359375
				],
				[
					-306.279296875,
					-88.24869537353516
				],
				[
					-305.9407958984375,
					-89.27082824707031
				],
				[
					-305.9407958984375,
					-89.61262512207031
				],
				[
					-305.9407958984375,
					-90.634765625
				],
				[
					-305.60223388671875,
					-90.9765625
				],
				[
					-304.9251708984375,
					-90.9765625
				],
				[
					-304.248046875,
					-90.63801574707031
				],
				[
					-300.41015625,
					-89.51171875
				],
				[
					-292.41864013671875,
					-86.00260162353516
				],
				[
					-292.080078125,
					-86.00260162353516
				],
				[
					-288.9193115234375,
					-85.21484375
				],
				[
					-287.4251708984375,
					-85.21484375
				],
				[
					-287.4251708984375,
					-85.21484375
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 54519997,
			"isDeleted": false,
			"id": "tveFLx__6Bt05oltwMkrN",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -86.4486083984375,
			"y": -46.00584411621094,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 927139411,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767926980,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 260695517,
			"isDeleted": false,
			"id": "6K_y4nBWhEVZ4VLsWlFbd",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -86.4486083984375,
			"y": -46.00584411621094,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 1146397491,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767927121,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 854292221,
			"isDeleted": false,
			"id": "0oHZvNIQweAu1SQnpZfWT",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -49.140625,
			"y": -114.93162536621094,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 352119827,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1706767937891,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"id": "nchdmD2x",
			"type": "text",
			"x": 20.73577880859375,
			"y": -241.64709854125977,
			"width": 5.556640625,
			"height": 23,
			"angle": 0,
			"strokeColor": "#1971c2",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1712112861,
			"version": 2,
			"versionNonce": 736189715,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1706772193568,
			"link": null,
			"locked": false,
			"text": "",
			"rawText": "",
			"fontSize": 20,
			"fontFamily": 2,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "",
			"lineHeight": 1.15
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1971c2",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidth": 0.5,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 2,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 459.77203369140625,
		"scrollY": 433.43095779418945,
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