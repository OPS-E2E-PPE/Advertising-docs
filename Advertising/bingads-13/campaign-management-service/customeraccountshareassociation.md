---
title: CustomerAccountShareAssociation Data Object - Campaign Management
ms.service: bing-ads-campaign-management-service
ms.topic: article
author: eric-urban
ms.author: eur
description: Contains the association count for the corresponding usage type.
---
# CustomerAccountShareAssociation Data Object - Campaign Management
Contains the association count for the corresponding usage type. 

> [!NOTE]
> Shared UET tags and audiences are only available for pilot customers where [GetCustomerPilotFeatures](../customer-management-service/getcustomerpilotfeatures.md) returns feature identifier 506.
> 
> Shared UET tags and audiences also require customer hierarchy feature enablement. Customer to customer [hierarchy](../guides/account-hierarchy-permissions.md#account-hierarchy) is only available for pilot customers where [GetCustomerPilotFeatures](../customer-management-service/getcustomerpilotfeatures.md) returns feature identifier 449.  

> [!TIP]
> For an overview of sharing audiences and UET tags in a customer hierarchy, see the [Share Audiences and UET Tags](../guides/universal-event-tracking.md#hierarchy-share) technical guide. 

## Syntax
```xml
<xs:complexType name="CustomerAccountShareAssociation" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:sequence>
    <xs:element minOccurs="0" name="AssociationCount" nillable="true" type="xs:long" />
    <xs:element minOccurs="0" name="UsageType" nillable="true" type="xs:string" />
  </xs:sequence>
</xs:complexType>
```

## <a name="elements"></a>Elements

|Element|Description|Data Type|
|-----------|---------------|-------------|
|<a name="associationcount"></a>AssociationCount|The association count for the corresponding usage type.<br/><br/>**Add:** Read-only<br/>**Update:** Read-only|**long**|
|<a name="usagetype"></a>UsageType|Indicates how the customer account share is used e.g., via audience association.<br/><br/>The possible values include RemarketingListAssociateToAdGroup, RemarketingListAssociateToCampaign, and UETTagAssociateToRemarketingList.<br/><br/>RemarketingListAssociateToAdGroup - The remarketing list is associated with or excluded by one or more ad groups.<br/><br/>RemarketingListAssociateToCampaign - The remarketing list is associated with or excluded by one or more campaigns.<br/><br/>UETTagAssociateToRemarketingList - The UET tag is used by one or more remarketing lists.<br/><br/>**Add:** Read-only<br/>**Update:** Read-only|**string**|

## Requirements
Service: [CampaignManagementService.svc v13](https://campaign.api.bingads.microsoft.com/Api/Advertiser/CampaignManagement/v13/CampaignManagementService.svc)  
Namespace: https\://bingads.microsoft.com/CampaignManagement/v13  

## Used By
[CustomerAccountShare](customeraccountshare.md)  
