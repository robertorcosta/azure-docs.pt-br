---
title: Usando APIs rest da bancada de blockchain do Azure
description: Cenários de como usar a API de visualização de banco de trabalho blockchain do Azure
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672743"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Usando a API de visualização de banco de trabalho blockchain do Azure REST

A Azure Blockchain Workbench Preview REST API fornece aos desenvolvedores e trabalhadores da informação uma maneira de construir integrações ricas em aplicativos blockchain. Este artigo destaca vários cenários de como usar a API Workbench REST. Por exemplo, suponha que você queira criar um cliente blockchain personalizado que permite que os usuários inscritos visualizem e interajam com seus aplicativos blockchain atribuídos. O cliente pode usar a API blockchain Workbench para visualizar instâncias contratuais e tomar ações em contratos inteligentes.

## <a name="blockchain-workbench-api-endpoint"></a>Ponto final da API da bancada de trabalho blockchain

As APIs do Blockchain Workbench são acessadas através de um ponto final para sua implantação. Para obter a URL de ponto final da API para sua implantação:

1. Faça login no [portal Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **grupos de recursos**.
1. Escolha o nome do grupo de recursos que o banco de trabalho blockchain implantado.
1. Selecione a coluna **TIPO** para classificar a lista alfabeticamente por tipo.
1. Há dois recursos com o tipo **Serviço de Aplicativo**. Selecione o recurso do tipo Serviço de **Aplicativo** *com* o sufixo "-api".
1. Na visão **geral**do serviço do aplicativo, copie o valor da **URL,** que representa a URL de ponto final da API para o seu Banco de Trabalho blockchain implantado.

    ![URL de ponto final do serviço de aplicativo](media/use-api/app-service-api.png)

## <a name="authentication"></a>Autenticação

As solicitações à API Blockchain Workbench REST são protegidas com o Azure Active Directory (Azure AD).

Para fazer uma solicitação autenticada às APIs REST, o código do cliente requer autenticação com credenciais válidas antes de poder chamar a API. A autenticação é coordenada entre os vários atores pelo Azure AD, e fornece ao seu cliente um [token de acesso](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) como prova da autenticação. O token é então enviado no cabeçalho de autorização HTTP de solicitações de API REST. Para saber mais sobre a autenticação do Azure AD, consulte [O Azure Active Directory para desenvolvedores](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

Consulte [amostras de API REST](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) para exemplos de como autenticar.

## <a name="using-postman"></a>Usando Postman

Se você quiser testar ou experimentar com APIs do Workbench, você pode usar [o Carteiro](https://www.postman.com) para fazer chamadas de API para sua implantação. [Baixe uma amostra da coleção de carteiros de solicitações de API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) do Workbench do GitHub. Consulte o arquivo README para obter detalhes sobre autenticação e usar o exemplo de solicitações de API.

## <a name="create-an-application"></a>Criar um aplicativo

Você usa duas chamadas de API para criar um aplicativo blockchain Workbench. Esse método só pode ser executado por usuários que são administradores do Workbench.

Use a [API POST de aplicativos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) para carregar o arquivo JSON do aplicativo e obter um ID de aplicativo.

### <a name="applications-post-request"></a>Solicitação post de aplicativos

Use o **parâmetro appFile** para enviar o arquivo de configuração como parte do corpo de solicitação.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Resposta POST de aplicativos

O ID do aplicativo criado é devolvido na resposta. Você precisa do ID do aplicativo para associar o arquivo de configuração com o arquivo de código quando você chamar a próxima API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Solicitação post de código de contrato

Use o [código de contrato de aplicativos POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) passando o ID do aplicativo para carregar o arquivo de código solidity do aplicativo. A carga pode ser um único arquivo Solidity ou um arquivo fechado contendo arquivos solidity.

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {applicationId} | Valor de devolução das aplicações POST API. |
| {ledgerId} | Índice do livro razão. O valor é geralmente 1. Você também pode verificar a [tabela Ledger](data-sql-management-studio.md) para obter o valor. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Resposta POST do código de contrato

Se for bem-sucedida, a resposta inclui o ID de código de contrato criado da [tabela ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Atribuir funções aos usuários

Use as [atribuições de função aplicativos POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) passando o ID do aplicativo, o ID do usuário e o ID da função do aplicativo para criar um mapeamento usuário-para-função no aplicativo blockchain especificado. Esse método só pode ser executado por usuários que são administradores do Workbench.

### <a name="role-assignments-post-request"></a>Atribuições de função SOLICITAÇÃO POST

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {applicationId} | Valor de devolução da API POST de aplicativos. |
| {userId} | Valor da ID do usuário na [tabela Usuário](data-sql-management-studio.md). |
| {aplicativoRoleId} | Valor de ID da função de aplicativo associado ao ID do aplicativo da [tabela ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Atribuições de função RESPOSTA POST

Se for bem-sucedida, a resposta inclui o ID de atribuição de função criado na [tabela RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Listar aplicativos

Use os [aplicativos OBTER API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) para recuperar todos os aplicativos blockchain Workbench para o usuário. Neste exemplo, o usuário inscrito tem acesso a dois aplicativos:

- [Transferência de ativos](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Transporte refrigerado](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Solicitação de aplicativos GET

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Aplicativos GET resposta

A resposta lista todos os aplicativos blockchain aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm cada aplicativo blockchain. Os administradores não-Workbench recebem todos os aplicativos blockchain para os quais eles têm pelo menos uma função de aplicativo associada ou uma função de instância de contrato inteligente associada.

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

Use [aplicativos Fluxos de trabalho OBTENHA API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) para listar todos os fluxos de trabalho de um aplicativo blockchain especificado ao qual um usuário tem acesso no Blockchain Workbench. Cada aplicativo blockchain tem um ou mais fluxos de trabalho, e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para um aplicativo cliente blockchain que possui apenas um fluxo de trabalho, recomendamos ignorar o fluxo de experiência do usuário que permite aos usuários selecionar o fluxo de trabalho apropriado.

### <a name="application-workflows-request"></a>Solicitação de fluxos de trabalho de aplicativos

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Resposta dos fluxos de trabalho do aplicativo

Os administradores do Blockchain Workbench obtêm todos os fluxos de trabalho blockchain. Os administradores que não são do Workbench obtêm todos os fluxos de trabalho para os quais têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

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

## <a name="create-a-contract-instance"></a>Criar uma instância de contrato

Use [contratos V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) para criar uma nova instância de contrato inteligente para um fluxo de trabalho. Os usuários só podem criar uma nova instância de contrato inteligente se o usuário estiver associado a uma função de aplicativo, que pode iniciar uma instância de contrato inteligente para o fluxo de trabalho.

> [!NOTE]
> Neste exemplo, a versão 2 da API é usada. As APIs do contrato da versão 2 fornecem mais granularidade para os campos provisionamentoStatus associados.

### <a name="contracts-post-request"></a>Solicitação de CONTRATOS CORREIO

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {fluxo de trabalhoId} | O valor do ID do fluxo de trabalho é o ConstructorID do contrato a partir da [tabela Fluxo de trabalho](data-sql-management-studio.md). |
| {contractCodeId} | Valor de id do código de contrato da [tabela ContractCode](data-sql-management-studio.md). Correlacionar o ID de aplicativo e o ID de livro para a instância do contrato que você deseja criar. |
| {conexãoId} | Valor de ID de conexão da [tabela Conexão](data-sql-management-studio.md). |

Para o órgão de solicitação, defina valores usando as seguintes informações:

| Parâmetro | Valor |
|-----------|-------|
| fluxo de trabalhoFunctionID | ID da [tabela WorkflowFunction](data-sql-management-studio.md). |
| fluxo de trabalhoActionParameters | Name value pares de parâmetros passados para o construtor. Para cada parâmetro, use o valor workflowFunctionParameterID da tabela [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contratos PÓS resposta

Se for bem-sucedido, a API de atribuições de função retorna o ContractActionID da [tabela ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligentes para um fluxo de trabalho

Use [contratos OBTENHA API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) para mostrar todas as instâncias de contrato inteligentes para um fluxo de trabalho. Ou você pode permitir que os usuários mergulhem em qualquer uma das instâncias do contrato inteligente mostradas.

### <a name="contracts-request"></a>Solicitação de contratos

Neste exemplo, considere que um usuário gostaria de interagir com uma das instâncias de contrato inteligente para agir.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Resposta de contratos

A resposta lista todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores do Workbench obtêm todas as instâncias do contrato inteligente. Os administradores que não são do Workbench obtêm cada instância de contrato inteligente para a qual eles têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

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

Use [a API de ação contratual para](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) mostrar as ações disponíveis do usuário dado o estado do contrato. 

### <a name="contract-action-request"></a>Solicitação de ação contratual

Neste exemplo, o usuário está olhando todas as ações disponíveis para um novo contrato inteligente que criou.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Resposta de ação contratual

Resposta lista todas as ações que um usuário pode executar levando em consideração o estado atual da instância do contrato inteligente especificado.

* Modificar: permite que o usuário modifique a descrição e o preço de um ativo.
* Encerrar: permite que o usuário finalize o contrato do ativo.

Os usuários obterão todas as ações aplicáveis se o usuário tiver uma função de aplicativo associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

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

Use [a API Post de Ação contratual](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) para tomar medidas para a instância de contrato inteligente especificada.

### <a name="contract-action-post-request"></a>Pedido de ação contratual POST

Neste caso, considere o cenário em que um usuário gostaria de modificar a descrição e o preço de um ativo.

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

Os usuários podem executar a ação somente com base no estado atual da instância de contrato inteligente especificada e na função de aplicativo associada do usuário ou na função de instância de contrato inteligente.

### <a name="contract-action-post-response"></a>Resposta post ação de contrato

Se a publicação for bem-sucedida, uma resposta HTTP 200 OK é retornada sem corpo de resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações de referência sobre APIs de blockchain workbench, consulte a [referência da API do Azure Blockchain Workbench REST](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).
