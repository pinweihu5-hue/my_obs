---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
0, 1 ,2  , 3,  4
 ^VkfAXA5M

0
1
2
3
4
 ^wxueE4v4

0  1  2  3  4
5  6  7  8  9
10 11  12  13  14
15 16  17  18 19
20 21 22 23 24 ^eW3DhjH5

N=4
 ^I0Y4tmH1

method(row, col): int
if r = 0  c is ans
if r = 1  c+5 is ans
if r = 2 c+10 is ans
if r = 3 c+15 is ans
if r = 4 c+20 is ans

5, 10, 15, 20 is row*(N)

return col + row * (N)

 ^Hz3IGHGO

1dToRowCol(int num) -> (r, c)
 ^3J1PtJep

(num-1)/N, reminder is 2, divider is 
7/5=1...2 ->(1, 2)


another ex:
24/5=4..4
23/5=4..3
10/5=2..0

another ex:
17/5=3....2 -> (3,2)
 ^wATslCaU

how to find up/down/left/right neighbor?


to find top:
the first row don't have top
and the rest can find top: num - N

to find bottom:
the bottom row don't have bottom
and the rest can find bottom:
num + N

to find left:
first col don't have left, the rest
can find: l num -1

to find right:
the last col don't have right, the rst
can find: num +1 ^MSYPRgLE

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.1.7",
	"elements": [
		{
			"type": "text",
			"version": 31,
			"versionNonce": 234198155,
			"index": "a8",
			"isDeleted": false,
			"id": "VkfAXA5M",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 44.570220947265625,
			"y": -256.6165771484375,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 150.39996337890625,
			"height": 50,
			"seed": 329016837,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723687445213,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "0, 1 ,2  , 3,  4\n",
			"rawText": "0, 1 ,2  , 3,  4\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "0, 1 ,2  , 3,  4\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 81,
			"versionNonce": 824014603,
			"index": "a9",
			"isDeleted": false,
			"id": "wxueE4v4",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 5.434417724609375,
			"y": -219.0248260498047,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 14.239990234375,
			"height": 150,
			"seed": 1372124389,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723687451413,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "0\n1\n2\n3\n4\n",
			"rawText": "0\n1\n2\n3\n4\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "0\n1\n2\n3\n4\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 231,
			"versionNonce": 301349637,
			"index": "aB",
			"isDeleted": false,
			"id": "eW3DhjH5",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 48.895660400390625,
			"y": -213.1660919189453,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 171.0399169921875,
			"height": 125,
			"seed": 244352645,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723687429853,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "0  1  2  3  4\n5  6  7  8  9\n10 11  12  13  14\n15 16  17  18 19\n20 21 22 23 24",
			"rawText": "0  1  2  3  4\n5  6  7  8  9\n10 11  12  13  14\n15 16  17  18 19\n20 21 22 23 24",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "0  1  2  3  4\n5  6  7  8  9\n10 11  12  13  14\n15 16  17  18 19\n20 21 22 23 24",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 43,
			"versionNonce": 2068892619,
			"index": "aC",
			"isDeleted": false,
			"id": "I0Y4tmH1",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -142.05484008789062,
			"y": -191.0218048095703,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 38.01997375488281,
			"height": 50,
			"seed": 1457049061,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723687460518,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "N=4\n",
			"rawText": "N=4\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "N=4\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 301,
			"versionNonce": 1370527499,
			"index": "aD",
			"isDeleted": false,
			"id": "Hz3IGHGO",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -140.10073852539062,
			"y": 29.204727172851562,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 228.099853515625,
			"height": 300,
			"seed": 272979237,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723728586645,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "method(row, col): int\nif r = 0  c is ans\nif r = 1  c+5 is ans\nif r = 2 c+10 is ans\nif r = 3 c+15 is ans\nif r = 4 c+20 is ans\n\n5, 10, 15, 20 is row*(N)\n\nreturn col + row * (N)\n\n",
			"rawText": "method(row, col): int\nif r = 0  c is ans\nif r = 1  c+5 is ans\nif r = 2 c+10 is ans\nif r = 3 c+15 is ans\nif r = 4 c+20 is ans\n\n5, 10, 15, 20 is row*(N)\n\nreturn col + row * (N)\n\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "method(row, col): int\nif r = 0  c is ans\nif r = 1  c+5 is ans\nif r = 2 c+10 is ans\nif r = 3 c+15 is ans\nif r = 4 c+20 is ans\n\n5, 10, 15, 20 is row*(N)\n\nreturn col + row * (N)\n\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 71,
			"versionNonce": 1589940517,
			"index": "aG",
			"isDeleted": false,
			"id": "3J1PtJep",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -460.2366943359375,
			"y": -112.62190246582031,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 280.19976806640625,
			"height": 50,
			"seed": 1480558443,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723688895152,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "1dToRowCol(int num) -> (r, c)\n",
			"rawText": "1dToRowCol(int num) -> (r, c)\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "1dToRowCol(int num) -> (r, c)\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 280,
			"versionNonce": 1860155109,
			"index": "aH",
			"isDeleted": false,
			"id": "wATslCaU",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -438.93756103515625,
			"y": -47.63688659667969,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 338.29974365234375,
			"height": 300,
			"seed": 1636372581,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723689057374,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "(num-1)/N, reminder is 2, divider is \n7/5=1...2 ->(1, 2)\n\n\nanother ex:\n24/5=4..4\n23/5=4..3\n10/5=2..0\n\nanother ex:\n17/5=3....2 -> (3,2)\n",
			"rawText": "(num-1)/N, reminder is 2, divider is \n7/5=1...2 ->(1, 2)\n\n\nanother ex:\n24/5=4..4\n23/5=4..3\n10/5=2..0\n\nanother ex:\n17/5=3....2 -> (3,2)\n",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "(num-1)/N, reminder is 2, divider is \n7/5=1...2 ->(1, 2)\n\n\nanother ex:\n24/5=4..4\n23/5=4..3\n10/5=2..0\n\nanother ex:\n17/5=3....2 -> (3,2)\n",
			"lineHeight": 1.25
		},
		{
			"type": "text",
			"version": 516,
			"versionNonce": 1551173835,
			"index": "aI",
			"isDeleted": false,
			"id": "MSYPRgLE",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 328.9404602050781,
			"y": -154.67127990722656,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 390.65966796875,
			"height": 475,
			"seed": 539608299,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1723689266075,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "how to find up/down/left/right neighbor?\n\n\nto find top:\nthe first row don't have top\nand the rest can find top: num - N\n\nto find bottom:\nthe bottom row don't have bottom\nand the rest can find bottom:\nnum + N\n\nto find left:\nfirst col don't have left, the rest\ncan find: l num -1\n\nto find right:\nthe last col don't have right, the rst\ncan find: num +1",
			"rawText": "how to find up/down/left/right neighbor?\n\n\nto find top:\nthe first row don't have top\nand the rest can find top: num - N\n\nto find bottom:\nthe bottom row don't have bottom\nand the rest can find bottom:\nnum + N\n\nto find left:\nfirst col don't have left, the rest\ncan find: l num -1\n\nto find right:\nthe last col don't have right, the rst\ncan find: num +1",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "how to find up/down/left/right neighbor?\n\n\nto find top:\nthe first row don't have top\nand the rest can find top: num - N\n\nto find bottom:\nthe bottom row don't have bottom\nand the rest can find bottom:\nnum + N\n\nto find left:\nfirst col don't have left, the rest\ncan find: l num -1\n\nto find right:\nthe last col don't have right, the rst\ncan find: num +1",
			"lineHeight": 1.25
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 494.2839885038487,
		"scrollY": 484.12624179615693,
		"zoom": {
			"value": 0.8500000000000001
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
		},
		"objectsSnapModeEnabled": false
	},
	"files": {}
}
```
%%