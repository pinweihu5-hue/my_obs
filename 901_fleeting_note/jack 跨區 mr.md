[[_wemo_tickets]]

---


MR:

hermes: [https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5178](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5178) (including tasks [[BE] 跨區 post zone api + regional operator (hermes + atlas)](https://app.asana.com/0/1206177962100119/1207535528739787/f)[[BE] 跨區 patch zone api + regional operator (hermes + atlas)](https://app.asana.com/0/1206177962100119/1207535528739789/f))

proto: [https://athena.wemoscooter.com/wemo/proto/-/merge_requests/111](https://athena.wemoscooter.com/wemo/proto/-/merge_requests/111)

atlas: [https://athena.wemoscooter.com/wemo/atlas/-/merge_requests/90](https://athena.wemoscooter.com/wemo/atlas/-/merge_requests/90)




```ts

table.addAuthEndpoint(3, { method: 'get', endpoint: '/:id', controller: controller.getById })



export const getById: WeMoRouter = async (req, res, next) => {
  try {
    if (req.me.type !== authType.Manager) throw new Error(customErrorsV2.FORBIDDEN.type)

    const { params } = req as GetByIdReqVO
    const { id } = params
    const result = await selectById(Number(id))
    res.send({
      result: 'success',
      data: result
    })
  } catch (error) {
    next(error)
  }
}


export async function selectById(id: number) {
  return await atlas.zoneService.getById(id)
}




  getById(id: number) {
    return new Promise((resolve, reject) => {
      this.client.getById({ id }, (err: grpcError, res: CreateZoneProto) => {
        if (err) {
          reject(new Error(err.details))
        } else {
          resolve(zhtwToZh_tw(formatToEntity(res)))
        }
      })
    })
  }


// v24
registerAuthEndpoint(table, {
  version: 24,
  method: 'get',
  endpoint: '/:id',
  controller: { manager: controller.getZone }
})


export const getZone: ControllerManager<typeof GetZoneResponseSchema> = {
  responseSchema: GetZoneResponseSchema,
  middleware: async ({ req }) => {
    const id = Number(req.params.id)
    const data = await service.getZone(id)

    return { data }
  }
}


export async function getZone(id: number): Promise<Zone> {
  const zone = (await atlas.zoneService.getById(id)) as GetZoneResponse

  const regionalOperatorZone = await db.regionalOperatorZone.getByZoneId(id)
  if (!R.isNil(regionalOperatorZone)) {
    zone.regionalOperatorId = regionalOperatorZone.regionalOperatorId
  }

  return zone
}



  getById(id: number) {
    return new Promise((resolve, reject) => {
      this.client.getById({ id }, (err: grpcError, res: CreateZoneProto) => {
        if (err) {
          reject(new Error(err.details))
        } else {
          resolve(zhtwToZh_tw(formatToEntity(res)))
        }
      })
    })
  }
							

```