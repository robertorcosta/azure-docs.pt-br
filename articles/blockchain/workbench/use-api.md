---
title: Using Azure Blockchain Workbench REST APIs
description: Scenarios for how to use the Azure Blockchain Workbench Preview REST API
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 188bbb9a9f6d289a7950ff74596352dff36e79f2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324199"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Using the Azure Blockchain Workbench Preview REST API

Azure Blockchain Workbench Preview REST API provides developers and information workers a way to build rich integrations to blockchain applications. Este documento orienta você através de vários métodos de chave da API REST do Workbench. Por exemplo, suponha que um desenvolvedor queira criar um cliente blockchain customizado. Esse cliente blockchain permite que usuários conectados visualizem e interajam com seus aplicativos blockchain atribuídos. O cliente permite aos usuários exibir instâncias de contrato e executar ações em contratos inteligentes. O cliente usa a API REST do Workbench no contexto do usuário conectado para executar as seguintes ações:

* Listar aplicativos
* Listar fluxos de trabalho para um aplicativo
* Listar instâncias de contrato inteligentes para um fluxo de trabalho
* Listar ações disponíveis para um contrato
* Executar uma ação para um contrato

Os aplicativos de blockchain de exemplo usados nos cenários podem ser [baixados do GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API endpoint

Blockchain Workbench APIs are accessed through an endpoint for your deployment. To get the API endpoint URL for your deployment:

1. Entre no [portal do Azure](https://portal.azure.com).
1. In the left-hand navigation pane, select **Resource groups**.
1. Choose the resource group name your deployed Blockchain Workbench.
1. Selecione a coluna **TIPO** para classificar a lista alfabeticamente por tipo.
1. Há dois recursos com o tipo **Serviço de Aplicativo**. Select the resource of type **App Service** *with* the "-api" suffix.
1. In the App Service **Overview**, copy the **URL** value, which represents the API endpoint URL to your deployed Blockchain Workbench.

    ![App service API endpoint URL](media/use-api/app-service-api.png)

## <a name="list-applications"></a>Listar aplicativos

Depois que um usuário tiver se conectado ao cliente blockchain, a primeira tarefa será recuperar todos os aplicativos do Blockchain Workbench para o usuário. Nesse cenário, o usuário tem acesso a dois aplicativos:

1. [Asset transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Transporte refrigerado](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Use a [API GET de aplicativos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

A resposta lista todos os aplicativos blockchain aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm cada aplicativo blockchain. Os administradores que não são do Workbench obtêm todos os blockchains para os quais possuem pelo menos uma função de aplicativo associada ou uma função de instância de contrato inteligente associada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Listar fluxos de trabalho para um aplicativo

Quando um usuário seleciona o aplicativo blockchain aplicável (como **Transferência de ativo**), o cliente blockchain recupera todos os fluxos de trabalho do aplicativo blockchain específico. Os usuários podem, em seguida, selecionar o fluxo de trabalho aplicável antes da exibição de todas as instâncias de contrato inteligente para o fluxo de trabalho. Cada aplicativo blockchain tem um ou mais fluxos de trabalho, e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para um aplicativo cliente blockchain que possui apenas um fluxo de trabalho, recomendamos ignorar o fluxo de experiência do usuário que permite aos usuários selecionar o fluxo de trabalho apropriado. Nesse caso, o **Asset Transferv** tem apenas um fluxo de trabalho, também chamado **Asset Transfer**.

Use a [API GET de Fluxos de Trabalho de Aplicativos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Resposta lista todos os fluxos de trabalho do aplicativo blockchain especificado aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm todos os fluxos de trabalho blockchain. Os administradores que não são do Workbench obtêm todos os fluxos de trabalho para os quais têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligentes para um fluxo de trabalho

Depois que um usuário selecionar o fluxo de trabalho aplicável, neste caso, **Asset Transfer**, o cliente blockchain recuperará todas as instâncias de contrato inteligente para o fluxo de trabalho especificado. Você pode usar essas informações para mostrar todas as instâncias do contrato inteligente para o fluxo de trabalho. Ou você pode permitir que os usuários mergulhem em qualquer uma das instâncias do contrato inteligente mostradas. Neste exemplo, considere que um usuário gostaria de interagir com uma das instâncias de contrato inteligente para agir.

Use a [API GET de Contratos](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Resposta lista todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores do Workbench obtêm todas as instâncias do contrato inteligente. Os administradores que não são do Workbench obtêm cada instância de contrato inteligente para a qual eles têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Listar ações disponíveis para um contrato

Quando um usuário decide mergulhar em um contrato, o cliente blockchain pode mostrar as ações disponíveis do usuário, considerando o estado do contrato. Neste exemplo, o usuário está procurando todas as ações disponíveis para um novo contrato inteligente criado:

* Modificar: permite que o usuário modifique a descrição e o preço de um ativo.
* Encerrar: permite que o usuário finalize o contrato do ativo.

Use a [API GET de Ação de Contrato](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Resposta lista todas as ações que um usuário pode executar levando em consideração o estado atual da instância do contrato inteligente especificado. Os usuários obterão todas as ações aplicáveis se o usuário tiver uma função de aplicativo associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Executar uma ação para um contrato

Um usuário pode decidir executar uma ação para a instância de contrato inteligente especificada. Nesse caso, considere o cenário em que um usuário gostaria de modificar a descrição e o preço de um ativo para a seguinte ação:

* Descrição: "Meu atualizado carro"
* Preço: 54321

Use a [API POST de Ação de Contrato](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Os usuários podem executar a ação somente com base no estado atual da instância de contrato inteligente especificada e na função de aplicativo associada do usuário ou na função de instância de contrato inteligente. Se a publicação for bem-sucedida, uma resposta HTTP 200 OK é retornada sem corpo de resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência de API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)