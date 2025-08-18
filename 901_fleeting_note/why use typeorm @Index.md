# typeorm @Index
`@Index('idx_daily_type_settings_tenantid_location_localdate', ['localDate', 'location', 'tenantId'], {})
加上這個有差嗎? 好處是?

- you could know idx by looking at code
	- but not really true, you shall check "that" DB for real, since we don't always use code to gen DB
- you can get index info from typeorm meta data
	- can you can programmaticlly use those data
- you can use typeorm code to gen sql and includes the idx
	- if you write sql into db with idx, and you use db to gen typeorm, you will got idx as well