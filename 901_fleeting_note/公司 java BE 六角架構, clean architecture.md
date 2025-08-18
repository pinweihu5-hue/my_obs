- app
	- config
		- MongoConfig
		- MinioConfig
	- domain
		- entity (放置核心業務物件的實體類別，通常對應業務模型，包含屬性與行為)
			- PartEntity
		- service (放置純粹的業務邏輯類別，負責處理 domain 規則、流程，不涉及基礎設施或 UI)
		- vo ([[Value Object in Java]])
	- exception
		- exception1
		- exception2
	- util
		- util1
	- 
	- adaptor
		- in.web
			- PartController
		- out
			- repo
				- PartRepo
			- SmServicePortImpl
			- minioPortImpl
			- PartRepoImpl
	- application
		- model
			- dto
				- PartRequestDTO
				- PartResponseDTO
			- po
				- partPO
		- port
			- in
				- PartService
			- out
				- SmServicePort
				- minioPort
		- PartServiceImpl







