---
title: Histórico de implantações
description: Descreve como exibir as operações de implantação do Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149575"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Exibir histórico de implantação com Azure Resource Manager

Azure Resource Manager permite que você exiba seu histórico de implantação e examine operações específicas em implantações anteriores. Você pode ver os recursos que foram implantados e obter informações sobre quaisquer erros.

Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Para obter detalhes sobre uma implantação do histórico de implantação.

1. Selecione o grupo de recursos que você deseja examinar.

1. Selecione o link em **implantações**.

   ![Selecionar histórico de implantação](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Selecione uma das implantações do histórico de implantação.

   ![Selecionar a implantação](./media/resource-manager-deployment-operations/select-details.png)

1. Um resumo da implantação é exibido, incluindo uma lista dos recursos que foram implantados.

    ![Resumo da implantação](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Para exibir o modelo usado para a implantação, selecione **modelo**. Você pode baixar o modelo para reutilizá-lo.

    ![Mostrar modelo](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Se sua implantação falhou, você verá uma mensagem de erro. Selecione a mensagem de erro para obter mais detalhes.

    ![Exibir falha na implantação](./media/resource-manager-deployment-operations/show-error.png)

1. A mensagem de erro detalhada é exibida.

    ![Exibir detalhes do erro](./media/resource-manager-deployment-operations/show-details.png)

1. A ID de correlação é usada para rastrear eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para solucionar problemas de implantação.

    ![Obter ID de correlação](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Para saber mais sobre a etapa que falhou, selecione **detalhes da operação**.

    ![Selecionar operações de implantação](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Você verá os detalhes dessa etapa da implantação.

    ![Mostrar detalhes da operação](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o status geral de uma implantação, use o comando **Get-AzResourceGroupDeployment**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Ou você pode filtrar os resultados para mostrar somente as implantações que falharam.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

A ID de correlação é usada para rastrear eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para solucionar problemas de implantação. Para obter a ID de correlação, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Cada implantação inclui várias operações. Cada operação representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, geralmente você precisa ver os detalhes sobre as operações de implantação. É possível ver o status das operações com **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Que retorna várias operações com cada uma no seguinte formato:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Para obter mais detalhes sobre as operações com falha, recupere as propriedades das operações com o estado **Falha** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Retorna todas as operações com falha com cada uma no seguinte formato:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Observe serviceRequestId e trackingId para a operação. serviceRequestId pode ser útil ao trabalhar com o suporte técnico para solucionar um problema de implantação. Você usará o TrackingID na próxima etapa para se concentrar em uma operação específica.

Para obter a mensagem de status de uma determinada operação com falha, use o seguinte comando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Que retorna:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Cada operação de implantação no Azure inclui conteúdo da solicitação e resposta. Durante a implantação, você pode usar o parâmetro **DeploymentDebugLogLevel** para especificar que a solicitação e/ou a resposta são registradas.

Você obtém informações do log e salva-as localmente usando os seguintes comandos do PowerShell:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>CLI do Azure

Para obter o status geral de uma implantação, use o comando **Azure Group Deployment show** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
A ID de correlação é usada para rastrear eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para solucionar problemas de implantação.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Para ver as operações de uma implantação, use:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Para obter informações sobre uma implantação, use a operação [obter informações sobre uma implantação de modelo](https://docs.microsoft.com/rest/api/resources/deployments) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Na resposta, observe em particular os elementos **provisioningState**, **correlationId** e **error**. **correlationId** é usado para acompanhar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico na solução de um problema de implantação.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Para obter informações sobre implantações, use [listar todas as operações de implantação de modelo](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
A resposta inclui informações de solicitação e/ou resposta com base no que foi especificado na propriedade **debugSetting** durante a implantação.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como usar os logs de atividades para monitorar outros tipos de ação, confira [View activity logs to manage Azure resources](resource-group-audit.md) (Exibir logs de atividades para gerenciar recursos do Azure).
* Para validar sua implantação antes de executá-la, consulte [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

