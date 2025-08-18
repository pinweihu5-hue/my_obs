[[tool optimize]]


- chose this in vscode
![[IMG-vscode_tsc_watch_auto_run-20241003104934080.png| 300]]


改一下內容變成...
```
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "typescript",
			"tsconfig": "tsconfig.json",
			
		    // 其他都是內建幫你gen好，加上這一段讓你把開專案就可以自動跑	
			"runOptions": {
				"runOn": "folderOpen"
			},
		
			"option": "watch",
			"problemMatcher": [
				"$tsc-watch"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"label": "tsc: watch - tsconfig.json"
		}
	]
}
```



允許可以跑
![[IMG-vscode_tsc_watch_auto_run-20241003104948940.png| 600]]


done


ref: https://enji.systems/2022/10/30/typescript-auto-compile-vs-code.html