# Endpoints of the project

## Structure of data 

components:

  schemas:
    
    HourlyMeteringDataFromSolace:
      type: array
      items:
        properties:
          from:
            type: integer
            description: Timestamp from which the hourly usage data will be provided. Timestamp represents the number of seconds elapsed since 00:00:00 UTC, Thursday, 1 January 1970
          to:
            type: integer
            description: Timestamp to which the hourly usage data will be provided. Timestamp represents the number of seconds elapsed since 00:00:00 UTC, Thursday, 1 January 1970
          orgId:
            type: string
            description: Solace orgId
          consumer:
            $ref: "#/components/schemas/ConsumerFromSolace"
          items:
            $ref: "#/components/schemas/EventBrokerUsageFromSolace"
            
    DailyMeteringDataFromSolace:
      type: array
      items:
        type: object
        properties:
          month:
            type: string
            description: Month for which subscription data will be provided. Month in yyyy-MM format
          orgId:
            type: string
            description: Solace orgId
          consumer:
            $ref: "#/components/schemas/ConsumerFromSolace"
          subscriptions:
            $ref: "#/components/schemas/SubscriptionUsageFromSolace"
        
    ConsumerFromSolace:
        type: object
        properties:
          environment:
            type: string
            description: BTP environment on which the customer is onboarded. Value can be CF/NEO/KUBERNETES
          region:
            type: string
            description: The technical name of the BTP region to which customer is onboarded. Value can be cf-eu10/cf-eu20
          subAccount:
            type: string
            description: Subaccount ID of customer, a GUID field
          cloudFoundry:
            $ref: "#/components/schemas/ConsumerCloudFoundry"
        required:
          - environment
          - region
          - subaccountId
        
    EventBrokerUsageFromSolace:
      type: array
      items:
        type: object
        properties:
          brokerId:
            type: string
            description: Unique ID/Name of the event broker
          itemId:
            type: string
            description: ItemId of the event broker in the Solace invoice which provides scale information like Enterprise 1K (sample value like CM-PUBSUB+SERVICE-ENT-1K-HA-S) 
          regionId:
            type: string
            description: Unique ID/Name of the region where event broker is created. In case of public cloud, this could be Solace data center and in case of private cloud this could be the name of the region provided by the customer
          regionType:
            type: string
            description: Region type of Solace event broker - AWSDedicatedCloud(VPC) or PrivateCloud...
          pricingModelId:
            type: string
            description: PricingModelId of an item in the Solace invoice. This provides information that the metered item is for hourly usage
            enum:
              - subscription-service-usage
          serviceHours:
            type: integer
            description: Service hour usage
          inboundBytes:
            type: integer
            description: Sum of size of all the incoming messages
          inboundMessages:
            type: integer
            description: Total number of incoming messages
          outboundBytes:
            type: integer
            description: Sum of size of all the outgoing messages
          outboundMessages:
            type: integer
            description: Total number of outgoing messages
          sapEventEnablement:
            $ref: "#/components/schemas/SapEventEnablementUsageFromSolace"
            
    SapEventEnablementUsageFromSolace:
      type: object
      properties:
        inboundBytes:
          type: integer
          description: Sum of size of all the incoming SAP Event Enablement (aka asapio) messages
        outboundBytes:
          type: integer
          description: Sum of size of all the outgoing SAP Event Enablement (aka asapio) messages
          
    SubscriptionUsageFromSolace:
      type: array
      items:
        type: object
        properties:
          subscriptionId:
            type: string
            description: Unique ID/Name of the subscription
          itemId:
            type: string
            description: ItemId of the subscription in the Solace invoice which provides scale information like Enterprise 1K (sample value like CM-PUBSUB+SERVICE-ENT-1K-HA-S) 
          regionId:
            type: string
            description: Unique ID/Name of the region where susbcription is created. In case of public cloud, this could be Solace data center and in case of private cloud this could be the name of the region provided by the customer 
          regionType:
            type: string
            description: Region of Solace subscription - AWSDedicatedCloud(VPC) or PrivateCloud...
          pricingModelId:
            type: string
            description: PricingModelId of an item in the Solace invoice. This provides information that the metered item is for monthly susbcription
            enum:
              - monthly-capacity

    ConsumerCloudFoundry:
      type: object
      properties:
        zoneId:
          type: string
          description: Zone ID of customer, a GUID field


    Error:
      type: object
      properties:
        code:
          type: integer
          description: Relevant error code, to identify Connectivity error, Business error or Technical error
        errorMessage:
          type: string
          description: Relevant error message
        errorDetails:
          type: string
          description: Specify here additional technical details about the error
      required:
      - code
      - errorMessage
      - errorDetails
     
    HourlyUsageToMaas:
      type: array
      items:
        type: object
        properties:
          id:
            type: string
          timestamp:
            type: string
          service:
            $ref: "#/components/schemas/ServiceToMaas"
          consumer:
            $ref: "#/components/schemas/ConsumerToMaas"
          measures:
            $ref: "#/components/schemas/MeasuresToMaas"
    
    DailyUsageToMaas:
      type: array
      items:
        type: object
        properties:
          id:
            type: string
          timestamp:
            type: string
          service:
            $ref: "#/components/schemas/ServiceToMaas"
          consumer:
            $ref: "#/components/schemas/ConsumerToMaas"
          measures:
            $ref: "#/components/schemas/MeasuresToMaas"
          customDimesions:
            $ref: "#/components/schemas/CustomDimensionsToMaas"
  
    ServiceToMaas:
        type: object
        properties:
          id:
            type: string
          plan: 
            type: string
 
    ConsumerToMaas: 
        type: object
        properties:
          cloudFoundry:
            $ref: "#/components/schemas/ConsumerCloudFoundry"
          environment:
            type: string
            description: BTP environment on which the customer is onboarded. Value can be CF/NEO/KUBERNETES
          region:
            type: string
            description: The technical name of the BTP region to which customer is onboarded. Value can be cf-eu10/cf-eu20
          subAccount:
            type: string
            description: Subaccount ID of customer, a GUID field
        required:
          - environment
          - region
          - subaccountId
          
    MeasuresToMaas:
      type: array
      items:
        type: object
        properties:
          id:
            type: string
            description: ItemId of the event broker 
          qualifier:
            type: string
          value: 
            type: integer
            description: number of hours of broker usage

    CustomDimensionsToMaas:
        type: object
        properties:
          dimension1:
            type: string
          dimension2:
            type: string


## Solace endpoints

### Hourly

- /solaceHourlyMeteringData/{from}/{to}
    - GET method will be called at this endpoint.
    - This API retrives the hourly metering data from solace stored in the object storage for given mandatory parameters "from timestamp" and "to timestamp" and optional parameters "region" and "subaccount".
    - i/p:
        - from timestamp (mandatory)
        - to timestamp (mandatory)
        - region (optional)
        - subAccount (optional)
    - o/p: 
        - components/schemas/HourlyMeteringDataFromSolace

- /solaceHourlyMeteringData
  - POST method will be called at this endpoint.
  - This API stores the hourly metering data from solace in the object storage.
    - i/p:
       - nil
    - o/p: 
        - Data stored in format: components/schemas/HourlyMeteringDataFromSolace

### Daily

- /solaceDailyMeteringData/{month}
  - GET method will be called at this endpoint.
  - This API retrives the daily metering data from solace stored in the object storage for given mandatory parameters "month" and optional parameters "region" and "subaccount".
    - i/p:
        - from timestamp (mandatory)
        - to timestamp (mandatory)
        - region (optional)
        - subAccount (optional)
    - o/p: 
        - components/schemas/DailyMeteringDataFromSolace

- /solaceDailyMeteringData
  - POST method will be called at this endpoint.
  - This API stores the daily metering data from solace in the object storage.
    - i/p:
       - nil
    - o/p: 
        - Data stored in format: components/schemas/DailyMeteringDataFromSolace


## Maas Endpoints

### Hourly

- /maasHourlyUsage/{from}
  - GET method will be called at this endpoint.
  - This API retrives the hourly usage data from Maas stored in the object storage for given mandatory parameters "from timestamp" and optional parameters "region" and "subaccount".
    - i/p:
        - from timestamp (mandatory)
        - region (optional)
        - subAccount (optional)
    - o/p: 
        - components/schemas/HourlyUsageToMaas

- /maasHourlyUsage
  - POST method will be called at this endpoint.
  - This API stores the hourly metering usage from Maas in the object storage.
    - i/p:
       - nil
    - o/p: 
        - Data stored in format: components/schemas/HourlyUsageToMaas

### Daily

- /maasDailyUsage/{from}
  - GET method will be called at this endpoint.
  - This API retrives the daily usage data from Maas stored in the object storage for given mandatory parameters "from timestamp" and optional parameters "region" and "subaccount".
    - i/p:
        - from timestamp (mandatory)
        - region (optional)
        - subAccount (optional)
    - o/p: 
        - components/schemas/DailyUsageToMaas

- /maasDailyUsage
  - POST method will be called at this endpoint.
  - This API stores the daily metering usage from Maas in the object storage.
    - i/p:
       - nil
    - o/p: 
        - Data stored in format: components/schemas/DailyUsageToMaas