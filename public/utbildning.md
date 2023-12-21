# Utbildningslogg

## Moment och avklarade delmål

- [ ] Bicep [Microsoft Bicep intro](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/learn-bicep#get-started)
- [ ] Git Branching [Learngitbranching](https://learngitbranching.js.org/)
- [ ] Percipio Interactive Course Basic Git Workflow **behöver inloggning?**
- [ ] Github Azure/bicep [Microsofts github sida om bicep för uppdaterad info ](https://github.com/Azure/bicep) 
- [ ] Percipio Channel Microsoft Certified: Azure DevOps Engineer Expert - **Behöver inloggning?** 
- [ ] Läroplan för att certifiera sig inom Terraform [Studyguide - Terraform Associate certification | Terraform | HashiCorp Developer]( https://developer.hashicorp.com/terraform/tutorials/certification-003/associate-study-003)
- [ ] OpenTofu–OpenSource-fork av Terraform [OpenTofu Introduction](https://opentofu.org/docs/intro/)



### Bicep
Microsoft Learn modulerna resulterar i att deploya en main.bicep fil med en modul som ligger i folder .modules som inkluderar `var` och `params`

Min VSC är kopplat till Azure Subscription och på så sätt kan vi skapa resurser m.h.a. mallar (templates) enligt nedan. 

Med `params` och `vars` möjliggör man att undvika använda fasta strängar eller andra variabler som fasta värden. 
Istället använder vi unika strängar som gör att mallarna kan återanvändas då varje resurs i Azure kräver ett unikt namn. 

**main.bicep**
```bicep
param location string = 'westus3'
param storageAccountName string = 'toylaunch${uniqueString(resourceGroup().id)}'
param appServiceAppName string = 'toylaunch${uniqueString(resourceGroup().id)}'

@allowed([
  'nonprod'
  'prod'
])
param environmentType string

var storageAccountSkuName = (environmentType == 'prod') ? 'Standard_GRS' : 'Standard_LRS'

resource storageAccount 'Microsoft.Storage/storageAccounts@2022-09-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: storageAccountSkuName
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

module appService 'modules/appService.bicep' = {
  name: 'appService'
  params: {
    location: location
    appServiceAppName: appServiceAppName
    environmentType: environmentType
  }
}

output appServiceAppHostName string = appService.outputs.appServiceAppHostName
```
**appService.bicep**
```bicep
param location string
param appServiceAppName string

@allowed([
  'nonprod'
  'prod'
])
param environmentType string

var appServicePlanName = 'toy-product-launch-plan'
var appServicePlanSkuName = (environmentType == 'prod') ? 'P2v3' : 'F1'

resource appServicePlan 'Microsoft.Web/serverFarms@2022-03-01' = {
  name: appServicePlanName
  location: location
  sku: {
    name: appServicePlanSkuName
  }
}

resource appServiceApp 'Microsoft.Web/sites@2022-03-01' = {
  name: appServiceAppName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    httpsOnly: true
  }
}

output appServiceAppHostName string = appServiceApp.properties.defaultHostName
```

## Verktyg att lära sig och nuvarande förutsättningar
- [ ] VSCode :green_square::green_square::white_large_square::white_large_square::white_large_square: 
- [ ] Git CMD/GUI :green_square::green_square::white_large_square::white_large_square::white_large_square: 
- [ ] Github :green_square::green_square::white_large_square::white_large_square::white_large_square: 
- [ ] Azure DevOps :white_large_square::white_large_square::white_large_square::white_large_square::white_large_square:
- [ ] Code deployment :green_square::green_square::green_square::white_large_square::white_large_square: 
- [ ] PowerShell :green_square::green_square::green_square::white_large_square::white_large_square: 
- [ ] Az CLI :green_square::green_square::green_square::white_large_square::white_large_square: 
- [ ] Github Actions (CI/CD Pipelines) :green_square::white_large_square::white_large_square::white_large_square::white_large_square:
- [ ] Azure Devops Pipelines :white_large_square::white_large_square::white_large_square::white_large_square::white_large_square:



### VsCode
[Länk för nedladdning](https://code.visualstudio.com/sha/download?build=stable&os=win32-x64-user)


### Git CMD/Gui
#### GIT CHEAT SHEET
[Länk för nedladdning](https://git-scm.com/download/win)


### Github
[Länk för nedladdning](https://windows.github.com)
![Github workflow](./assets/Gitflow.JPG)


### Azure DevOps

### Code Deployment

### PowerShell

### Az CLI
Installeras via [Länken](https://learn.microsoft.com/sv-se/cli/azure/install-azure-cli-windows?tabs=azure-cli#install-or-update)
Du interagerar med Azure genom att först koppla upp dig till ditt konto `Connect-AzAccount`

` Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` gör att man får köra skript lokalt.

`Import-Module Az.Accounts` hämtar modulen för att koppla upp sig till sitt microsoft konto.

`Connect-AzAccount` sätter igång authentisering för att koppla ihop CLI med microsoft kontot.

Här säkerställer jag att är inne på rätt sub för att skjuta in min kod samt att man anger det som en variabel i miljön. 
```sh
$context = Get-AzSubscription -SubscriptionId {subid} 
>> Set-AzContext $context
```

`Set-AzDefault -ResourceGroupName {namn på resursgrupp}` anger den resursgrupp man ska deploya sin kod i. 


### Github Actions 

### Azure DevOps Pipelines
