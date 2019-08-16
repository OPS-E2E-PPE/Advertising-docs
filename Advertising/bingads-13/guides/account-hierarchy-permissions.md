---
title: "Account Hierarchy and User Permissions"
ms.service: "bing-ads"
ms.topic: "article"
author: "eric-urban"
ms.author: "eur"
description: Determine user permissions for the account hierarchy. 
---
# Account Hierarchy and User Permissions
Microsoft Advertising users can use the same login credentials to access multiple accounts, potentially with different permissions on each account. 

[User Roles and Permissions](#user-roles-permissions) describes the actions available for each user role, how users are provisioned on an account, how you can act on behalf of an authenticated Microsoft Advertising user, and how you can discover their current access rights with the Bing Ads API.  

For more information about the campaign hierarchy within an account, see [Entity Limits](entity-hierarchy-limits.md), [Campaigns](campaigns.md), and [Ads](ads.md).

## <a name="user-roles-permissions"></a>User Roles and Permissions
Your application might only need to support one Super Admin user on a known account. Even with such a relatively simple permission structure you'll want to understand the actions available for each [user role](#user-roles), how [users are provisioned](#assign-user-roles) on an account, how you can [discover their current access rights](#get-user-roles), and how you can [act on behalf of an authenticated Microsoft Advertising user](#access-developer-token) with the Bing Ads API.  

### <a name="user-roles"></a>User Roles
The user role granted by a customer's Super Admin or the Microsoft Advertising system administrator determines service availability. For example a user with the Advertiser Campaign Manager role can add and update campaigns, but cannot create or update users. Unless otherwise noted in the reference content per service operation, the following table describes at a high level the service restrictions per user role. 

> [!NOTE]
> Only Super Admin and Standard users can be set as the primary contact for an account.

|User Role|Available Services|
|-------------|-------------|
|Advertiser Campaign Manager|This role has permissions to view selected accounts and add, edit, or delete campaigns within the selected accounts. The Advertiser Campaign Manager can view payment methods, but cannot manage any billing and payment tasks.<br/><br/>Read operations for all services are available.<br/><br/>Write operations with the [Customer Management service](../customer-management-service/customer-management-service-operations.md) are generally not available. One exception is that the Advertiser Campaign Manager can update the [AutoTagType](../customer-management-service/advertiseraccount.md#autotagtype) element of an [AdvertiserAccount](../customer-management-service/advertiseraccount.md) using the [UpdateAccount](../customer-management-service/updateaccount.md) operation.|
|Aggregator|Read and write operations for all services are available, except [DeleteCustomer](../customer-management-service/deletecustomer.md).|
|Standard user|This role has permissions to manage campaigns and perform some billing activities on selected accounts. This role cannot add, edit, or delete payment methods; add or delete accounts. Standard users can link and unlink ad accounts, but cannot manage customer to customer level client links.<br/><br/>Standard users can manage some users in the accounts they have access to. A Standard user can invite or delete other Standard users, Advertiser Campaign Managers, and Viewers, and view information about all users in the context of the current customer. However, Standard users cannot invite or delete a Super Admin, nor can they edit a Super Admin's role.<br/><br/>Read operations for all services are available.<br/><br/>Write operations with the [Customer Billing service](../customer-management-service/customer-billing-service-operations.md) and [Customer Management service](../customer-management-service/customer-management-service-operations.md) are generally not available. Exceptions of operations available to a Standard user are [AddInsertionOrder](../customer-billing-service/addinsertionorder.md), [UpdateInsertionOrder](../customer-billing-service/updateinsertionorder.md), and [UpdateAccount](../customer-management-service/updateaccount.md).|
|Super Admin|This role has full permissions for all accounts. A Super Admin can manage everything related to billing and payments, account details, and other users (including other Super Admins). The Super Admin can specify which accounts other users have access to. When signing up as a new customer, the first user is the Super Admin.<br/><br/>Read and write operations for all services are available, except [DeleteCustomer](../customer-management-service/deletecustomer.md) and [SignupCustomer](../customer-management-service/signupcustomer.md). Write operations for all other services are available.|
|Viewer|This role has read-only permissions.<br/><br/>Read operations for all services are available.|

### <a name="assign-user-roles"></a>Assign User Roles
When signing up for a new account in the Microsoft Advertising web application, you'll be granted the Super Admin [user role](#user-roles). A Super Admin can create new users with the Advertiser Campaign Manager, Super Admin, Standard, or Viewer role. The Aggregator role is provisioned for resellers by special request through the System Administrator. For more information, see [Aggregator Hierarchy](#aggregator-hierarchy) and contact your account manager.

Technically new users cannot be created programmatically; however, you can use the [SendUserInvitation](../customer-management-service/senduserinvitation.md) operation to invite people to sign up under an existing Microsoft Advertising account. When you invite someone to an account or set of accounts, you will also set the [user role](#user-roles). Microsoft Advertising generates an email invitation that is sent to the invitee. By clicking on the emailed link and completing the Microsoft Advertising signup workflow, they are accepting the invitation to manage accounts with the user role that you provisioned in the [SendUserInvitation](../customer-management-service/senduserinvitation.md) request. 

> [!NOTE]
> A person can use the same login credentials when signing up for new accounts and accepting invitations to existing accounts. In either case when the same credentials are used to complete the signup workflow, the person is considered to have [Multi-User Credentials](#multi-user-credentials). From the perspective of each Super Admin managing users in their customer scope, the user's role, account access, and contact information are unique. Any permissions the user has in the context of another customer is not taken into account when acting within the scope of the current customer. 

A Super Admin has the option to modify their users' access to different accounts and potentially modify the [user role](#user-roles) e.g., from Viewer to Standard user. To update a user's role, call the [UpdateUserRoles](../customer-management-service/updateuserroles.md) operation. 

### <a name="get-user-roles"></a>Get User Roles
To get a list of the users who can access one or more accounts of a customer, call the [GetUsersInfo](../customer-management-service/getusersinfo.md) operation. The operation returns an array of objects that contains the log in email address and identifier of each user. Then you can get the details of each user in the list, such as their role and account permissions in Microsoft Advertising, call the [GetUser](../customer-management-service/getuser.md) operation. When calling [GetUser](../customer-management-service/getuser.md) if you leave the *UserId* element nil, the response will include details for the current authenticated user as specified by the request header credentials. 

Here is an example [CustomerRoles](../customer-management-service/getuser.md#customerroles) element returned by the [GetUser](../customer-management-service/getuser.md) operation. 

```xml
<CustomerRoles xmlns:e1335="https://bingads.microsoft.com/Customer/v13/Entities" d4p1:nil="false" xmlns:d4p1="http://www.w3.org/2001/XMLSchema-instance">
  <e1335:CustomerRole>
    <e1335:RoleId>ValueHere</e1335:RoleId>
    <e1335:CustomerId>ValueHere</e1335:CustomerId>
    <e1335:AccountIds d4p1:nil="false" xmlns:a1="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a1:long>ValueHere</a1:long>
    </e1335:AccountIds>
    <e1335:LinkedAccountIds d4p1:nil="false" xmlns:a1="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a1:long>ValueHere</a1:long>
    </e1335:LinkedAccountIds>
    <e1335:CustomerLinkPermission d4p1:nil="false">ValueHere</e1335:CustomerLinkPermission>
  </e1335:CustomerRole>
</CustomerRoles>
```

Each [CustomerRole](customerrole.md#roleid) represents the permissions that a person has when accessing the corresponding account or set of accounts.  
- The [RoleId](customerrole.md#roleid) represents the [user role](#user-roles) e.g., 41 represents the Super Admin user role.  
- The [CustomerId](customerrole.md#customerid) is the identifier of the customer where the user has either signed up or has some [account hierarchy](#account-hierarchy) relationship.  
- The [AccountIds](customerrole.md#accountids) contains identifiers of ad accounts that the user can access in the context of the [CustomerId](customerrole.md#customerid).  
- The [LinkedAccountIds](customerrole.md#linkedaccountids) contains identifiers of linked ad accounts that the user can access in the context of the [CustomerId](customerrole.md#customerid).  
- The [CustomerLinkPermission](customerrole.md#customerlinkpermission) qualifies the [account hierarchy](#account-hierarchy) relationship and [user role](#user-roles) in the context of the [CustomerId](customerrole.md#customerid).  

Taken individually, the user has the same role on the [CustomerId](customerrole.md#customerid), [AccountIds](customerrole.md#accountids), and [LinkedAccountIds](customerrole.md#linkedaccountids) for a given [CustomerRole](customerrole.md); however, if a user has multiple customer roles then taken as a whole the effective set of permissions depend on the full set of [CustomerRole](customerrole.md) objects. Several examples are provided below. 

#### <a name="roles-initial-signup"></a>Roles Example for New User
First signed up with Super Admin permissions:

```xml
<CustomerRoles xmlns:a="https://bingads.microsoft.com/Customer/v13/Entities" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<a:CustomerRole>
	   <a:RoleId>41</a:RoleId>
	   <a:CustomerId>123</a:CustomerId>
	   <a:AccountIds xmlns:b="http://schemas.microsoft.com/2003/10/Serialization/Arrays"/>
	   <a:LinkedAccountIds xmlns:b="http://schemas.microsoft.com/2003/10/Serialization/Arrays"/>
	   <a:CustomerLinkPermission i:nil="true"/>
	</a:CustomerRole>
 </CustomerRoles>
```


#### <a name="roles-multi-user"></a>Roles Example for Multi-User Credentials


#### <a name="roles-account-hierarchy"></a>Roles Example for Account Hierarchy


#### <a name="roles-aggregator-hierarchy"></a>Roles Example for Aggregator Hierarchy



### <a name="access-developer-token"></a>Access and Developer Tokens
To programmatically act on behalf of a Microsoft Advertising user, you must obtain their consent. At the end of the consent workflow you can get an access token that represents the user. The access token has the same roles and access to the same accounts as the user has in the Microsoft Advertising web application. In other words, the same accounts and user role permissions available in the Microsoft Advertising web application are available to the user programmatically through the API. For information on getting an access token to act on behalf of a Microsoft Advertising user, see [Authentication with OAuth](authentication-oauth.md). 

You'll also need a developer token that uniquely identifies your application. Obtaining a developer token for API access does not grant additional permissions to any Microsoft Advertising accounts. A developer token enables programmatic access to the accounts already provisioned for a user. For information, see [Get a Developer Token](get-started.md#get-developer-token). 

> [!TIP]
> To get an access token and make your first service call using the Bing Ads API, see the [Quick Start](get-started.md#quick-start) sample. 

The AuthenticationToken and DeveloperToken headers must be set in every request via the Bing Ads API. Here is an example call to the [GetUser](../customer-management-service/getuser.md) operation. 

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:v13="https://bingads.microsoft.com/Customer/v13">
  <soapenv:Header>
      <v13:DeveloperToken>DeveloperTokenGoesHere</v13:DeveloperToken>
      <v13:AuthenticationToken>AccessTokenGoesHere</v13:AuthenticationToken>
  </soapenv:Header>
  <soapenv:Body>
      <v13:GetUserRequest>
        <v13:UserId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
      </v13:GetUserRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

## <a name="multi-user-credentials"></a>Multi-User Credentials
You can use one set of Microsoft Advertising multi-user credentials to access ad accounts across multiple customers, potentially with different user roles and permissions. By adding multi-user access to your existing user name, you effectively extend your reach by being able to access other people's accounts. The nice part: You don't need to remember another set of user names and passwords, and you don't need to log in and out of Microsoft Advertising to view different accounts owned by other people. If you accept the invitation with existing Microsoft Advertising credentials, you will have multi-user credentials. It is also possible to contact support directly and have multiple user names consolidated to a single user name i.e., one login will have multi-user permissions.

Each person with multi-user credentials can be assigned a different user role (Super Admin, Standard user, Advertiser Campaign Manager, or Viewer) per customer that they are invited to. For example, your multi-user credentials grants you access to Customer A and Customer B. However, your Viewer user role for Customer A limits you from making any changes on the accounts that Belong to Customer A. But as a Super Admin for Customer B, you have full control over that customer's accounts.  

It might help to think of "multi-user" credentials to mean "multiple user roles", since from one perspective you only login with one user name to access multipe customers with varying permissions. One person's credentials can act with multiple distinct user roles. 

For more details see the Microsoft Advertising help topic [Managing your user name to access multiple accounts](https://help.ads.microsoft.com/#apex/3/en/56867/0).

### <a name="multi-user-consolidation"></a>Multi-User Consolidation
If you already login with multiple sets of credentials e.g., two email addresses, multi-user credentials can be provisioned manually. Contact support or your account manager to merge existing user names into a single user name. Another option is to have an invitation sent to you from each customer that you want to manage, and then accept each invitation using the login credentials that you want to keep. This option is available either through the Microsoft Advertising web application or the [SendUserInvitation](../customer-management-service/senduserinvitation.md) service operation. Once you accept the invitation with existing Microsoft Advertising credentials, you will have "multi-user" credentials. 

Let's consider the following user roles and permissions before multi-user consolidation. In the Microsoft Advertising web application each user must log in separately and has different permissions during each logged in session. Likewise via the API each user's access token (see [Authentication with OAuth](authentication-oauth.md)) represents permissions limited to the corresponding user and role. 

|User|Role|Permissions|
|-------------|----------------------|----------------|
|one@contoso.com|Viewer|Customer A - All Accounts|
|two@contoso.com|Super Admin|Customer B - All Accounts|
|three@contoso.com|Viewer|Customer C - Account A|
|four@contoso.com|Standard user|Customer B - All Accounts|

First please note that only one email address per customer can be consolidated, so in this example two@contoso.com and four@contoso.com cannot be consolidated together. Now let's see what happens after the top three users are consolidated under one@contoso.com. 
  * No changes for user four@contoso.com whether via the Microsoft Advertising web application, Microsoft Advertising Editor, or API. 
  * The user one@contoso.com can log in via the Microsoft Advertising web application and Microsoft Advertising Editor. The consolidated users i.e., two@contoso.com and three@contoso.com no longer have permissions to sign in via the Microsoft Advertising web application or Microsoft Advertising Editor. While signed in as one@contoso.com, you can switch context to the customer accounts with corresponding user roles that had previously been assigned to two@contoso.com and three@contoso.com. Although you can access multiple customers signed in with one user's credentials (one@contoso.com), you will need to switch from customer to customer to manage the accounts that are linked with unique user roles. Customers and their related accounts remain distinct. For more details see the Microsoft Advertising help topic [Managing your user name to access multiple accounts](https://help.ads.microsoft.com/#apex/3/en/56867/0).
  * After multi-user consolidation the access token for user one@contoso.com will represent permissions to the consolidated list (superset) of accounts. The user role in effect will depend on the customer and account identifiers specified in the service request. Access tokens for two@contoso.com and three@contoso.com will no longer be accepted i.e., error 120 - UserLoginAccessDenied will be returned. 

### <a name="multi-user-contactinfo"></a>Multi-User Contact Info
A person with multi-user credentials is represented by multiple [User](../customer-management-service/user.md) objects and corresponding user identifiers. In effect, the same person's credentials can be associated with separate sets of user contact information i.e., unique contact information per customer. 

The [GetUser](../customer-management-service/getuser.md) response can vary depending on who makes the call, even with the same user identifier. Let's say for example, that prior to consolidation the identifiers for one@contoso.com, two@contoso.com, and three@contoso.com were 123, 456, and 789 respectively. Each user identifier effectively maps a person to a specific customer e.g., identifier 123 maps one@contoso.com to the original customer that the person could access. In this example we refer to 123 as the original user identifier. 

- If the access token for one@contoso.com is used to call [GetUser](../customer-management-service/getuser.md), and the UserId is nil or UserId is set to the original user identifier (e.g., 123), the operation will return [CustomerRole](../customer-management-service/customerrole.md) objects for all customers that the user can access. 
- If the access token for one@contoso.com is used to call [GetUser](../customer-management-service/getuser.md), and the UserId is set to either 456 or 789, the operation will only return one [CustomerRole](../customer-management-service/customerrole.md) object that maps this person to a specific customer. 

The Name, Lcid, JobTitle, and ContactInfo user settings for the same person will be automatically synchronized with any updates that occur after user consolidation. The LastModifiedByUserId and LastModifiedTime are also in sync across each returned [User](../customer-management-service/user.md) object, unless you had an old username merged and have not updated any user settings since the consolidation. 

> [!NOTE]
> The TimeStamp differs from the LastModifiedTime. All TimeStamp values are unique per User and when you call [UpdateUser](../customer-management-service/updateuser.md) you must include the corresponding user's timestamps (including the address timestamp).

For example let's say you haven't updated user information for one@contoso.com since prior to consolidation with two@contoso.com and three@contoso.com. After consolidation and until the user settings are updated post-consolidation, you will continue to observe a distinct LastModifiedByUserId and LastModifiedTime within each returned [User](../customer-management-service/user.md) object.

|User Id|Contact Info Id|Permissions|LastModifiedByUserId|
|-------------|----------------------|----------------|----------------|
|123|234|Customer A - All Accounts|123|
|456|567|Customer B - All Accounts|456|
|789|890|Customer C - Account A|789|

Now let's say that one@contoso.com is acting in the context of Customer B and updates their contact information. The updated contact information as well as the same LastModifiedByUserId and LastModifiedTime are now syncrhonized across all returned [User](../customer-management-service/user.md) objects.

|User Id|Contact Info Id|Permissions|LastModifiedByUserId|
|-------------|----------------------|----------------|----------------|
|123|234|Customer A - All Accounts|456|
|456|567|Customer B - All Accounts|456|
|789|890|Customer C - Account A|456|

## <a name="account-hierarchy"></a>Account Hierarchy
- direct advertiser model via ad accounts under one customer
- agency model via account and customer links
- Aggregator model via signupcustomer

Search advertising businesses typically align with one or more of the following account management models. 
- A direct advertiser builds a Bing Ads API application for its own advertising campaigns and is billed directly by Microsoft for valid ad clicks.  
- A tool provider builds a Bing Ads API application for other companies to manage their advertising campaigns, and is not billed by Microsoft. The advertiser user owns the accounts, is billed directly by Microsoft for valid ad clicks, and may pay a fee to the tool provider.  
- An agency builds a Bing Ads API application for their company to manage the campaigns of their advertising clients. The client of the agency owns the accounts, is billed directly by Microsoft for valid ad clicks, and may pay a fee to the agency.  
- A reseller builds a Bing Ads API application to manage the campaigns of their advertising clients, and is billed directly by Microsoft for valid live clicks. The advertiser does not sign up for Microsoft Advertising credentials, and may pay a fee to the reseller.  

### <a name="agency-hierarchy"></a>Agency Hierarchy
An agency builds a Bing Ads API application for their company to manage the campaigns of their advertising clients. Agencies can manage some or all aspects of an advertiser account. When sending the invitation to manage a client account, the agency can specify whether the client or agency is responsible for billing. For more information about becoming an agency, see the help article [Managing your clients as an agency on Microsoft Advertising](https://help.ads.microsoft.com/#apex/3/en/52083/3) or [Resources for agency partners](https://about.ads.microsoft.com/en-us/resources/agency-hub).

> [!NOTE]
> The client account must be set up for post-pay billing. Prepaid accounts are not supported for management by agencies.

Only an agency Super Admin can add, update, and search for client links. Linking enables any agency Super Admin to access the specified client account. If the client has multiple accounts, then a client link invitation must be sent for each client account. The Super Admin can also determine which individual accounts the Advertiser Campaign Manager and Viewer users can access. In the figure above **User A** has access to account A1, and **User B** has access to accounts A2, B1, and B2. For more information, see [User Roles](#user-roles) technical guide.

#### <a name="clientlink"></a>Link to Client Accounts

To manage client accounts, a Super Admin user of the agency must send an invitation to the client, which must then be accepted by a Super Admin user of the client. To determine whether a link already exists, call the [SearchClientLinks](../customer-management-service/searchclientlinks.md) operation and check the Status element of any returned [ClientLink](../customer-management-service/clientlink.md). For a list of possible status values, see [ClientLinkStatus value set](../customer-management-service/clientlinkstatus.md). To search by individual account, set the predicate field to ClientAccountId and set the predicate value to the account identifier that you want to find. There is no set limit to the amount of client accounts that can be linked to an agency.

If a link exists with status either Active, LinkAccepted, LinkInProgress, LinkPending, UnlinkInProgress, or UnlinkPending, the agency cannot initiate a duplicate client link.

If a client link to the specified account does not yet exist, or if the lifecycle of an existing link had ended with status of Expired, LinkCanceled, LinkDeclined, LinkFailed, or Inactive, then the agency can initiate a new client link invitation by calling the [AddClientLinks](../customer-management-service/addclientlinks.md) operation. The service transitions client link status to LinkPending immediately.

> [!IMPORTANT]
> The agency can specify whether the client or agency is responsible for billing by setting the *IsBillToClient* element in the service request. If not otherwise specified, the agency will be billed.

To update a client link, the *TimeStamp* element is required for validation, so you must first call the [SearchClientLinks](../customer-management-service/searchclientlinks.md) operation to get the existing *ClientLink* object. Then modify the *Status* element of the returned *ClientLink*, and include the updated *ClientLink* object in a subsequent call to the [UpdateClientLinks](../customer-management-service/updateclientlinks.md) operation.

The client can only use the *UpdateClientLinks* operation to update the status as LinkAccepted or LinkDeclined.

> [!NOTE]
> The client can accept or decline through an application built on the Bing Ads API, or through the **Accounts & Billing** tab in the Microsoft Advertising web application. In either case the client credentials must be used to accept or decline. For more information, see the Microsoft Advertising help article [Managing your clients as an agency on Microsoft Advertising](https://help.ads.microsoft.com/#apex/3/en/52083/3).

If the client sets the status to LinkDeclined, the client link lifecycle ends. You cannot update a declined client link, and you must send a new invitation to manage the client account. If the client sets the status to LinkAccepted, the status transitions to LinkInProgress. If the link process succeeds, the service updates the client link status to Active.

If the link process fails, possibly due to a billing transition error, the service updates the client link status to LinkFailed. You cannot update a failed client link, and you must send a new invitation to manage the client account.

If the client or agency does not take action within 30 days, the service sets the status to LinkExpired and the client link lifecycle ends. You cannot update an expired client link, and you must send a new invitation to manage the client account.

![Link to Client](media/client-link-status-flow.png "Link to Client")

If the client link status is LinkPending, the agency may choose to cancel a prior link request. If the client link status is Active, the agency may choose to initiate the unlink process, which would terminate the existing relationship with the client.

To initiate the unlink process, the agency sets the client link status to UnlinkRequested and calls the [UpdateClientLinks](../customer-management-service/updateclientlinks.md) operation. Updating the status with UnlinkRequested effectively sets the status to UnlinkInProgress. The service transitions client link status to UnlinkPending immediately, and then waits for system resources to proceed. The state should transition quickly to UnlinkInProgress.

If the unlink process fails, possibly due to a billing transition error, the client link resumes to Active status. If the unlink process succeeds the status will update to Inactive, and the client link lifecycle ends. You cannot update an inactive client link, and you must send a new invitation to manage the client account.

![Unlink from Client](media/client-unlink-status-flow.png "Unlink from Client")

For code examples that show how to add and update a client link invitation, see [Client Links Code Example](code-example-client-links.md).

### <a name="aggregator-hierarchy"></a>Aggregator Hierarchy
The reseller role is offered to a limited set of partners that offer search-marketing tools and services to a large number of advertisers. The reseller role allows partners to programmatically create new customer accounts. The reseller is billed by invoice for all advertising costs incurred by their clients. The advertiser typically does not sign up for Microsoft Advertising credentials, and may pay a fee to the reseller. 

An Aggregator user is provisioned in the reseller's primary customer shell. As described in [Entity Limits](entity-hierarchy-limits.md) all advertising activity is organized by customer, which can have one or more accounts. Every time [SignupCustomer](../customer-management-service/signupcustomer.md) is called by the Aggregator user, a new ad account is created within a new customer. 

> [!IMPORTANT] 
> The Aggregator user role is required for [SignupCustomer](../customer-management-service/signupcustomer.md), and is provided solely to resellers. If a Super Admin user is added to the reseller customer after your initial credentials are provisioned, by default the user can manage the data of all customers that the reseller manages. The user won't be able to call [SignupCustomer](../customer-management-service/signupcustomer.md), but will otherwise have read and write access to the campaign data.

The [SignupCustomer](../customer-management-service/signupcustomer.md) operation requires both of the [Customer](../customer-management-service/customer.md) and [AdvertiserAccount](../customer-management-service/advertiseraccount.md) objects. The customer object includes the customer's name, the address where the customer is located, the market in which the customer operates, and the industry in which the customer participates. Although it is possible to add multiple customers with the same details, you should use unique customer names so that users can easily distinguish between customers in a user interface. The account object must specify the name of the account; the type of currency to use to settle the account; and the payment method identifier, which must be set to null. The operation generates an invoice account and sets the payment method identifier to the identifier associated with the reseller's invoice. The billing rolls up to the reseller's payment instrument, and you are invoiced for all charges incurred by the customers that you manage.

#### <a name="reseller-signup"></a>How to Get Reseller Credentials
To request reseller credentials, please contact your designated account management team for details about getting the Aggregator role for resellers. If you are not currently a reseller but would like to become one, go to the [Microsoft Advertising Partner Program](https://about.ads.microsoft.com/en-us/partners/welcome) welcome page. 

## See Also
[Customer Management Service Reference](../customer-management-service/customer-management-service-reference.md)  
[Bing Ads API Web Service Addresses](web-service-addresses.md)  

