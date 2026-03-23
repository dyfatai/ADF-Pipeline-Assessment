Purpose

This document explains how to deploy the assessment environment (Storage, Data Factory, datasets, dataflow and pipeline), how to upload the sample data, and how to remove the assessment artifacts when finished. This document is simply to show what the full pipeline sould look like after deployment. Upon full deployment, the pipeline and dataflow acticvities should be fully deteleted before candidate test.

Files in `arm_template/`
- `CombinedFullDeployment.json` - creates Storage (ADLS Gen2), containers `rawsource` & `cleansed`, Data Factory, linked service, datasets, dataflow and pipeline.
- `CombinedFullDeployment.parameters.json` - edit placeholders before deploy.

Prerequisites
- Azure CLI (az) or Portal access; permission to create resources in the target subscription/resource group.
- Local copy of `HR Employee Survey Responses.xlsx` (upload step is manual unless you ask for automation).

Deploy (Portal UI)
1. Portal -> Resource groups -> Create new resource group (or use an empty one).
2. In the resource group blade click `Deploy a custom template` -> `Build your own template in the editor`.
3. Paste the contents of `CombinedFullDeployment.json` and Save.
4. Provide parameter values (or upload `CombinedFullDeployment.parameters.json`) - set a unique `factoryName` and a globally unique `storageAccountName`.
5. Deploy and wait for completion.

Deploy (CLI)
1. Login and create an RG:

```powershell
az login
az group create --name MyNewResourceGroup --location eastus
```
2. Deploy using the provided files:

```powershell
$tmpl = 'C:\Users\Beyond One\Documents\ADF Pipeline\arm_template\CombinedFullDeployment.json'
$params = 'C:\Users\Beyond One\Documents\ADF Pipeline\arm_template\CombinedFullDeployment.parameters.json'
az deployment group create --resource-group MyNewResourceGroup --template-file $tmpl --parameters @$params
```

Post-deploy (upload sample file)
- Upload the Excel to the `rawsource` container (Storage Explorer in Portal or CLI):

```powershell
az storage blob upload --account-name <storageAccountName> --container-name rawsource --name 'HR Employee Survey Responses.xlsx' --file 'C:\path\to\HR Employee Survey Responses.xlsx'
```

Verify in ADF
- Open the Data Factory -> Author & Monitor. Confirm the linked service, datasets, dataflow `df_raw_to_cleansed` and pipeline `PL_raw_to_cleansed` are present.

Remove assessment resources
- In ADF: delete the dataflow (`df_raw_to_cleansed`) and the pipeline (`PL_raw_to_cleansed`) from the factory when finished. (You can also delete the storage account separately if desired.)

Notes
- `storageAccountName` must be globally unique.
- The template uses the created storage account key to configure the linked service; for production use Managed Identity + RBAC instead.

If deployment fails, collect the deployment error details (Resource group -> Deployments -> select deployment -> click the failed operation) and share them for troubleshooting.
