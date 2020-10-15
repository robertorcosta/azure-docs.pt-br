---
title: 'Tutorial: Usar uma identidade gerenciada para acessar o Azure Cosmos DB – Windows – Azure AD'
description: Um tutorial que orienta você pelo processo de usar uma identidade gerenciada atribuída pelo sistema em uma VM do Windows para acessar o Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b571b2b8e0d334a02631e3f443ec54398117ee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532662"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Windows para acessar o Cosmos DB. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta do BD Cosmos
> * Conceder a uma identidade gerenciada atribuída pelo sistema da VM do Windows acesso às chaves de acesso da conta do Cosmos DB
> * Obter um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM do Windows para chamar o Azure Resource Manager
> * Obter chaves de acesso do Azure Resource Manager para realizar chamadas do Cosmos DB

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)


## <a name="enable"></a>Habilitar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso


### <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos 

Se você ainda não tiver uma conta do Cosmos DB, crie uma. Você poderá ignorar esta etapa e usar uma conta de Cosmos DB existente. 

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **Bancos de dados** e, em seguida, **Azure Cosmos DB** e um novo painel "Nova conta" será exibido.
3. Insira uma **ID** para a conta do Azure Cosmos DB, a qual será usada posteriormente.  
4. A **API** deve ser definida como “SQL”. A abordagem descrita neste tutorial pode ser usada com outros tipos de API disponíveis, mas as etapas neste tutorial são para a API do SQL.
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.  Selecione um **Local** no qual o Cosmos DB está disponível.
6. Clique em **Criar**.

### <a name="create-a-collection"></a>Criar uma coleção 

Em seguida, adicione uma coleção de dados na conta do Cosmos DB que você possa consultar em etapas posteriores.

1. Navegue até sua conta do Cosmos DB recém-criado.
2. Na guia **Visão geral**, clique no botão **+/Adicionar coleção** e um painel "Adicionar coleção" surgirá.
3. Dê à coleção uma ID de banco de dados, ID de coleção, selecione uma capacidade de armazenamento, insira uma chave de partição, digite um valor de taxa de transferência e clique em **OK**.  Para este tutorial, é suficiente usar "Teste" como ID do banco de dados e a ID da coleção, selecionar uma capacidade de armazenamento fixa e a menor taxa de transferência (400 RU/s).  


### <a name="grant-access-to-the-cosmos-db-account-access-keys"></a>Permitir acesso às chaves de acesso da conta do Cosmos DB

Esta seção mostra como conceder à identidade gerenciada atribuída pelo sistema da VM do Windows acesso às chaves de acesso da conta do Cosmos DB. O Cosmos DB não dá suporte nativo a autenticação do Microsoft Azure AD. No entanto, você pode usar uma identidade gerenciada atribuída pelo sistema para recuperar uma chave de acesso do Cosmos DB do Resource Manager e usar essa chave para acessar o Cosmos DB. Nesta etapa, você concede à identidade gerenciada atribuída pelo sistema da VM do Windows acesso às chaves para a conta do Cosmos DB.

Para conceder o acesso de identidade gerenciada atribuída pelo sistema da VM do Windows à conta do Cosmos DB no Azure Resource Manager usando o PowerShell, atualize os valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` para o seu ambiente. O Cosmos DB oferece suporte a dois níveis de granularidade ao usar chaves de acesso: acesso de leitura/gravação à conta e acesso somente leitura à conta.  Atribua a função `DocumentDB Account Contributor` se você quiser obter chaves de leitura/gravação para a conta ou atribua a função `Cosmos DB Account Reader Role` se você quiser obter chaves somente leitura para a conta.  Para este tutorial, atribua o `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Lembre-se de que caso não seja possível executar uma operação, pode ser que você não tenha as permissões corretas. Caso queira obter o acesso para gravação de chaves, será necessário usar uma função RBAC, como o Colaborador da Conta do DocumentDB ou criar uma função personalizada. Para obter mais informações, examine o [Controle de acesso baseado em função no Azure Cosmos DB](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Acessar dados

Esta seção mostra como chamar o Azure Resource Manager usando um token de acesso para a identidade gerenciada atribuída pelo sistema da VM do Windows. Para o restante do tutorial, trabalharemos na VM criada anteriormente. 

Você precisa instalar a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) em sua VM do Windows.



### <a name="get-an-access-token"></a>Obter um token de acesso

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a Máquina Virtual do Windows e, na página **Visão geral**, clique em **Conectar** na parte superior. 
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows. 
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o PowerShell na sessão remota.
4. Usando Invoke-WebRequest do Powershell, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > O valor do parâmetro "resource" deve ser uma correspondência exata para o que é esperado pelo Azure AD. Ao usar a ID de recurso do Azure Resource Manager, você deve incluir a barra à direita no URI.
    
   Em seguida, extraia o elemento “Conteúdo”, que está armazenado como uma cadeia de caracteres formatada em JSON (JavaScript Object Notation) no objeto $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Em seguida, extraia o token de acesso da resposta.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Obter chaves de acesso 

Esta seção mostra como obter chaves de acesso do Azure Resource Manager para realizar chamadas do Cosmos DB. Agora, usaremos PowerShell para fazer uma chamada ao Gerenciador de Recursos usando o token de acesso recuperado na seção anterior para recuperar a chave de acesso da conta do Cosmos DB. Assim que tivermos a chave de acesso, poderemos fazer consultas no Cosmos DB. Substitua os parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo token de acesso que você recuperou anteriormente.  Se você quiser recuperar chaves de leitura/gravação, use o tipo de operação de chave `listKeys`.  Se você quiser recuperar chaves somente leitura, use o tipo de operação de chave `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
A resposta fornece a lista de chaves.  Por exemplo, se você receber as chaves somente leitura:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Agora que você tem a chave de acesso para a conta do Cosmos DB, pode passá-la para um SDK do Cosmos DB e fazer chamadas para acessar a conta.  Como exemplo rápido, você pode passar a chave de acesso para a CLI do Azure.  Você pode obter o `<COSMOS DB CONNECTION URL>` na guia **Visão geral** da folha da conta do Cosmos DB no portal do Azure.  Substitua o `<ACCESS KEY>` pelo valor obtido acima:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Esse comando de CLI retorna detalhes sobre a coleção:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Desabilitar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade atribuída pelo sistema da VM do Windows para acessar o Cosmos DB.  Para saber mais sobre o Cosmos DB, confira:

> [!div class="nextstepaction"]
>[Visão geral do Azure Cosmos DB](../../cosmos-db/introduction.md)