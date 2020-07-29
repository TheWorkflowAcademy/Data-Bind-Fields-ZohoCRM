# Data-Bind-Fields-ZohoCRM
A Deluge script that data binds two of the same fields in two separate modules - both fields will always reflect any updates that are made to each of the corresponding fields.

## Core Idea
This is useful when you have a similar field in two separate modules. For example, you have a field called "Company Overview" in the **Accounts** module and also in **Deals** for ease of reference (or whatever other reason it may be). Because they are both similar fields, you would want the information to be accessible and constantly up to date in both modules. This script enables you to edit the field in either module and have the changes reflected in both modules.

## Configuration
For this function to work both ways, you will need to set up two custom functions and workflow rules (one for each module).
* **Worfklow Rule 1**
  * Triggers the custom function below when an **Account** is created/ edited.
  * Custom Function 1: When the "Company Overview" field in an **Account** is updated, the similar field in all related **Deals** gets the updated with the same value.
* **Workflow Rule 2**
  * Triggers the custom function below when a **Deal** is created/ edited.
  * Custom Function 2: When the "Company Overview" field on a **Deal** is updated, the similar field in the **Account** gets updated, along with all other **Deals** related to the same **Account**.

## Tutorial

### Custom Function 1
```javascript
account = zoho.crm.getRecordById("Accounts",accountid);
//Get Company Overview Value
company_overview = account.get("Company_Overview");
//Get the related Deal IDs
deal = zoho.crm.getRelatedRecords("Deals","Accounts",accountid);
dealids = List();
for each  d in deal
{
	dealids.add(d.get("id"));
}
//Update Company Overview Value in the similar field in all related Deals
for each  id in dealids
{
	update = zoho.crm.updateRecord("Deals",id,{"Company_Overview":company_overview});
	info update;
}
```
*Replace "Company_Overview" with the relevant field name.*

### Custom Function 2
```javascript
deal = zoho.crm.getRecordById("Deals",dealid);
//Get Company Overview Value
company_overview = deal.get("Company_Overview");
//Get the Account ID
accountid = deal.get("Account_Name").get("id");
//Update Company Overview value in the similar field in the Account
update = zoho.crm.updateRecord("Accounts",accountid,{"Company_Overview":company_overview});
//info update;
//Get other related Deal IDs of the Account
deals = zoho.crm.getRelatedRecords("Deals","Accounts",accountid);
dealids = List();
for each  d in deals
{
	dealids.add(d.get("id"));
}
//Update the Company Overview value in all other Deals related to the Account
for each  id in dealids
{
	if(id != dealid)
	{
		update2 = zoho.crm.updateRecord("Deals",id,{"Company_Overview":company_overview});
		info update2;
	}
}
```
*Replace "Company_Overview" with the relevant field name.*
