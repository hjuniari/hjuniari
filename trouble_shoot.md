### .INBOUND
> #### PO
><details>
 test
></details>

> #### Barcoded
> #### Update ASN use resend event kafka
> #### Can't create LPN
> #### Delete SKU
> #### Double GR / LPN / Excess receive
><details>
>
>- [Ticket Reference Link](https://swiftsolutions.proofhub.com/bapplite/#app/todos/project-4652120818/list-143025339924/task-214639046461)<br>
>- [Slack Reference Link](https://swiftsolutionsid.slack.com/archives/C010W1EJ8D7/p1638592194007800)

></details>
> #### 

### .INVENTORY
> #### Update Expiry Date
> <details>
>     
> **Requirment :**<br>
>         <span style='background :#F7DC6F'> 
>     - entity_id wms-inventory.inventory provide by ops<br>
>     - expire date,double ensure to ops before execute<br>
>     - when set expiry date use UTC time,example when expiry time requets extend to "2022-09-01" and existing expiry_date on database "2021-09-01 17:00:00"  should set update to "2022-08-31 17:00:00" will display on wms "1 September 2022"
>         </span>
> ``` sql
>     select * from `wms-inventory`.inventory i 
>     where entity_id ='32a44a50-c7f2-49be-8f1a-bd6a7f42c22d'
> ```
>     
> </details>

> #### Inventory Movement
> <details>
>
> [swagger]("http://wms-inv-wms-inventory.production.svc.cluster.local/assets/lib/swagger-ui/index.html?/url=http://wms-inv-wms-inventory.production.svc.cluster.local/api-docs.json#!/inventory/bulkUpdate") <br>
> **payload :**
> ``` json
{
	"referenceType": "force_update",
	"referenceText": "Move to dmg/qr rack 11172021",
	"allowPartialUpdate": false,
	"force": true,
	"items": [{
		"id": "string",
		"move": {
			"containerId": "string",
			"containerCode": "string"
		}
	}]
}
> ```
> </details>


### .ORDER
> #### Sync Metadata
> <details>
> 
> </details>

> #### Update Tag

> #### Rubah 3PL AWB
> <details>
> <summary>Example case Click!!</summary>
>
> ![example_case](../source/chang_3pl.png)
> </details>
> <details>
> Check Provider Request Change:
>
> ``` sql
>    select * from `wms-awb`.provider where title like '%wahana%'; 
>  ```
> - Simpan provider entity_id
> - Simpan provider code 
> - Simpan provider title.
>
> Check Existing AWB:
>
> ``` sql
>    select * from `wms-awb`.awb a where awb_number ='WHN00123456'; 
>  ```
> - Ganti provider_entity_id dengan entity_id provider yang di request
>
> Check Existing order_fulfillment_provider :
>
> ``` sql
>    select * from `wms-fulfill-order`.order_fulfillment_provider ofp 
>    where order_fulfillment_entity_id ='8b757899-0de6-330f-9d20-d6dc0c3193e8';
>  ```
> - Ganti position_reference_id dengan provider_entity_id yang di minta
> - Ganti position_reference_text dengan provider code  yang di minta
> - Ganti position_label dengan provider title  yang di minta.
>
> Check Existing order_fulfillment_status_history :
>
> ``` sql
> select * from `wms-fulfill-order`.order_fulfillment_status_history ofsh 
> where order_fulfillment_entity_id ='8b757899-0de6-330f-9d20-d6dc0c3193e8'
> and status = 'in_transit';
>  ```
> - Ganti position_reference_id dengan provider_entity_id yang di minta
> - Ganti position_reference_text dengan provider code  yang di minta
> - Ganti position_label dengan provider title  yang di minta.
>
> Check Existing order_fulfillment :
>
> ``` sql
> select * from `wms-fulfill-order`.order_fulfillment_status_history ofsh 
> where order_fulfillment_entity_id ='8b757899-0de6-330f-9d20-d6dc0c3193e8'
> and status = 'in_transit';
>  ```
> - Ganti position_reference_id dengan provider_entity_id yang di minta
> - Ganti position_reference_text dengan provider code  yang di minta
> - Ganti position_label dengan provider title  yang di minta.
>
> </details>

> #### Rubah Status dari Cancelled ke Routing
><details>
>
>- update inventory status ke **preparing_for_dispatch** tapi nempel ke SO itu (kalo nggak bisa lgsg ya ke available dulu) via swagger
>> POST **/v1/inventory/update**
>> ``` json
>> {
>> "referenceType": "force_update",
>> "referenceText": "1021048483",
>>   "referenceId" : "f0a6519f-f108-3f2b-ad70-6d90db13405d",
>> "allowPartialUpdate": false,
>> "force": true,
>> "items": [{
>>     "id": "43fc4a39-3157-4ee3-9e74-924d9ad0f080",
>>     "status": "preparing_for_dispatch"
>> }]
>> }
>> ```
>- **Soft delete OFSH** status cancelled via database update field deleted with current date
>- Update OF status ke **routing** via database
>- update OFI status **preparing_for_dispatch** via database
></details>

> #### Banyak Can't Predefine AWB (Lag)
><details>
>
>- Cek WMS /SO Status search by SO Number,biasanya di info so provider dan awb isinya -
>- Cek lag di Offset Kafka Consumer **ss.wms-tokopedia.order.process-order-awb** /offset kemungkinan ada banyak lag<br>
>- _ssi g rd -u production wms-int -s wms-tokopedia-order_
>- Kalo masih ada yang belum solved bisa di process dengan cara manual upload awb :
>>- Via Postman Api Get Singel Order kemudian search di result Logistic ambil logistic_name dan logistic_service,warehouse_name
>>- Via Postman Api Get Order Job kemudian search di result booking_code(awb number)
>>- Buat csv file dengan header **(SO Number,3PL Code,AWB Number)** dan value di bawah nya sesuai header,untuk provider di sesuaikan dengan warehouse_name nya,file bisa berisi multiple SO selama tenant nya masih sama
>>- Buka WMS /4PL/Upload AWB sesuai tenant SO nya,setelah upload sukses pastikan so sudah ada awb number dan provider nya
></details>

### .OUTBOUND
> #### Picklist
> #### _Can't Assign Picker_
> #### Update SO Ready To Pack Without OFI
> #### 

### .BAU
> #### Get User UUID <br>
> <details>
>
> 
> </details>




> #### Inject AWB 
> <details>
>
> **Requirement**
> - AWB Stock file (dari tim lapangan)
> - Provider tujuan AWB
> **Tutorial**
> Download dan buka file dari tim lapangan yang berisi kumpulan AWB yang di request
> Cek AWB yang saat itu masih tersedia di DB WMS-AWB // AWB_Availability_Summary
> query
> ``` sql
select p.entity_id,p.code,p.title,p.provider_type,p.status,count(a.awb_number) available
from wms-awb.provider p left join `wms-awb`.awb a 
on p.entity_id = a.provider_entity_id 
where a.status = 'available' 
group by p.entity_id,p.code,p.title,p.provider_type,p.status
order by count(a.awb_number);
> ```
> </details>

> #### ADD TENANT
> #### ADD EMAIL DO
> #### ADD NEW LOCATION
> #### ADD PACKAGING
> #### OPEN NEW 3PL
> #### CEK SKU
> #### CEK STATUS PRODUCT




```python

```
