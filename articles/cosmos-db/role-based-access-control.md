---
title: Controle de acesso baseado em função no Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece proteção de banco de dados com a Integração de DiretórioAtivo (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445096"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controle de acesso baseado em função no Azure Cosmos DB

O Azure Cosmos DB fornece rbac (control de acesso baseado em função) integrado para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Active Directory pode atribuir essas funções RBAC a usuários, grupos, diretores de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações nos recursos do Azure Cosmos DB. As atribuições das tarefas de função são escopo apenas para acesso a plano de controle, o que inclui acesso a contas, bancos de dados, contêineres e ofertas (throughput).

## <a name="built-in-roles"></a>Funções internas

A seguir estão as funções incorporadas suportadas pelo Azure Cosmos DB:

|**Função interna**  |**Descrição**  |
|---------|---------|
|[Colaborador de Conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas do Azure Cosmos DB.|
|[Leitor de contas Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler dados de contas do Azure Cosmos DB.|
|[Operador de backup da Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Pode enviar solicitação de restauração para um banco de dados do Azure Cosmos ou um contêiner.|
|[Operador cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Pode provisionar contas, bancos de dados e contêineres do Azure Cosmos, mas não pode acessar as chaves necessárias para acessar os dados.|

> [!IMPORTANT]
> O suporte ao RBAC no Azure Cosmos DB aplica-se apenas às operações de controle de aviões. As operações de plano de dados são seguras usando chaves mestres ou tokens de recursos. Para saber mais, consulte [Acesso seguro aos dados no Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestão de identidade e acesso (IAM)

O painel **de controle de acesso (IAM)** no portal Azure é usado para configurar o controle de acesso baseado em função nos recursos do Azure Cosmos. As funções são aplicadas a usuários, grupos, diretores de serviços e identidades gerenciadas no Active Directory. Você pode usar funções incorporadas ou funções personalizadas para indivíduos e grupos. A captura de tela a seguir mostra a integração do Active Directory (RBAC) usando o controle de acesso (IAM) no portal Azure:

![Controle de acesso (IAM) no portal do Azure – demonstrando a segurança de banco de dados](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Funções personalizadas

Além das funções incorporadas, os usuários também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar essas funções aos diretores de serviço em todas as assinaturas dentro de seu inquilino do Active Directory. As funções personalizadas fornecem aos usuários uma maneira de criar definições de função RBAC com um conjunto personalizado de operações de provedor escriumbantes de recursos. Para saber quais operações estão disponíveis para a construção de funções personalizadas para o Azure Cosmos DB ver, [operações do provedor de recursos Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Prevenção de alterações do Cosmos SDK

O provedor de recursos Cosmos pode ser bloqueado para evitar quaisquer alterações nos recursos, incluindo conta cosmos, bancos de dados, contêineres e throughput de qualquer cliente que se conecte via chaves de conta (ou seja, aplicativos conectados via Cosmos SDK). Quando definido, as alterações em qualquer recurso devem ser de um usuário com a função e credenciais de RBAC adequadas. Esse recurso é `disableKeyBasedMetadataWriteAccess` definido com o valor da propriedade no provedor de recursos Cosmos. Um exemplo de um modelo do Azure Resource Manager com essa configuração de propriedade está abaixo.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- [O que é o RBAC (Role-Based Access Control, controle de acesso baseado em função) para recursos do Azure](../role-based-access-control/overview.md)
- [Funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md)
- [Operações do provedor de recursos Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
