---
title: Restringir o acesso do usuário a operações de dados apenas com o Azure Cosmos DB
description: Saiba como restringir o acesso às operações de dados apenas com o Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980368"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Restringir o acesso do usuário apenas às operações de dados

No Azure Cosmos DB, existem duas maneiras de autenticar suas interações com o serviço de banco de dados:
- usando sua identidade do Azure Active Directory ao interagir com o portal Azure,
- usando [chaves](secure-access-to-data.md#master-keys) DB do Azure Cosmos ou [tokens de recursos ao](secure-access-to-data.md#resource-tokens) emitir chamadas de APIs e SDKs.

Cada método de autenticação dá acesso a diferentes conjuntos de operações, com alguma sobreposição: ![Divisão de operações por tipo de autenticação](./media/how-to-restrict-user-data/operations.png)

Em alguns cenários, você pode querer restringir alguns usuários da sua organização para realizar operações de dados (ou seja, apenas solicitações e consultas CRUD). Este é normalmente o caso para desenvolvedores que não precisam criar ou excluir recursos, ou alterar o throughput provisionado dos contêineres em que estão trabalhando.

Você pode restringir o acesso aplicando as seguintes etapas:
1. Criando uma função de diretório ativo do Azure personalizado para os usuários que você deseja restringir o acesso. A função de Diretório Ativo personalizado deve ter nível de acesso fino às operações usando [as ações granulares](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)do Azure Cosmos DB .
1. Não permitindo a execução de operações sem dados com chaves. Você pode conseguir isso restringindo essas operações apenas às chamadas do Azure Resource Manager.

As próximas seções deste artigo mostram como executar essas etapas.

> [!NOTE]
> Para executar os comandos nas próximas seções, você precisa instalar o Módulo 3.0.0 powershell do Azure ou posterior, bem como a [Função de Proprietário do Azure](../role-based-access-control/built-in-roles.md#owner) na assinatura que você está tentando modificar.

Nos scripts do PowerShell nas próximas seções, substitua os seguintes espaços reservados por valores específicos para o seu ambiente:
- `$MySubscriptionId`- O ID de assinatura que contém a conta do Azure Cosmos onde você deseja limitar as permissões. Por exemplo: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- O grupo de recursos contendo a conta do Azure Cosmos. Por exemplo: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- O nome da sua conta no Azure Cosmos. Por exemplo: `mycosmosdbsaccount`.
- `$MyUserName`- Ousername@domainlogin ( ) do usuário para quem você deseja limitar o acesso. Por exemplo: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Selecionar sua assinatura do Azure

Os comandos Do Azure PowerShell exigem que você faça login e selecione a assinatura para executar os comandos:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Crie a função de diretório ativo do Azure personalizado

O script a seguir cria uma atribuição de função azure Active Directory com acesso "Somente chave" para contas do Azure Cosmos. O papel é baseado em [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md) e [ações granulares para o Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Essas funções e ações `Microsoft.DocumentDB` fazem parte do namespace do Azure Active Directory.

1. Primeiro, crie um documento `AzureCosmosKeyOnlyAccess.json` JSON nomeado com o seguinte conteúdo:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Execute os seguintes comandos para criar a atribuição Função e atribua-a ao usuário:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Proibir a execução de operações sem dados

Os seguintes comandos removem a capacidade de usar chaves para:
- criar, modificar ou excluir recursos
- atualizar as configurações do contêiner (incluindo políticas de indexação, throughput etc.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o controle de acesso baseado em papéis da Cosmos DB](role-based-access-control.md)
- Obtenha uma visão geral do [acesso seguro aos dados no Cosmos DB](secure-access-to-data.md)
