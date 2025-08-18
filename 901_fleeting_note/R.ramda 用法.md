

- 也可以把 ramda 看成是一個很 handy 的 抽象
	- like you just use queue, stack or pqor just use graph algo(如果你夠熟，你就會用
- 函數style, 習慣好其實更好讀懂


不需要用 new map 去挑東西加入 -> 用 groupBy
```ts


const timeLineIdToRuleMap = new Map<string, PricingRule[]>()
for (const rule of rules) {
  //  如果沒有，就加入
  if (!timeLineIdToRuleMap.has(rule.pricingTimelineId)) {
	timeLineIdToRuleMap.set(rule.pricingTimelineId, [])
  }
  // 如果有，就 push
  timeLineIdToRuleMap.get(rule.pricingTimelineId)?.push(rule)
}

const rules = timeLineIdToRuleMap.get(timelineId) || []



// 用 ramda:
const rulesGroupByPricingTimelineId = R.groupBy(R.prop('pricingTimelineId'), rules)
const rules2 = rulesGroupByPricingTimelineId[timelineId] || []

// 只是下面要用 rulesGroupByPricingTimelineId.pricingTimelineId 去拿, 上面要用 map.get 去拿


```



mapping data 只需要拿其中一些東西 -> map + pick
```ts

]
const data = rules.map((rule: PricingRule) => ({
	id: rule.id,
	algorithm: rule.algorithm,
	role: rule.role,
	configs: rule.configs
}))



// 用 ramda:
const data3 = R.map(R.pick(['id', 'algorithm', 'role', 'configs']), rules2)


```



多層次排序
```ts

// Sort the pricingTimeLines array by beginAt and id
timeLines.sort((a, b) => {

	// 如果 beginA 一樣，後建立的優先 (由大到小）
	if (b.beginAt.getTime() === a.beginAt.getTime()) {
		return b.id.localeCompare(a.id)
	}
	
	// 晚生效的優先：beginAt（由大到小）
	return b.beginAt.getTime() - a.beginAt.getTime()

})


// 用 ramda:
const sortedTimeLines = 
	  R.sortWith([
		  R.descend(R.prop('beginAt')), 
		  R.descend(R.prop('id')
	   )], timeLines)





```