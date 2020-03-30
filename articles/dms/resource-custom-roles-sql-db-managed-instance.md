---
title: 'Funções personalizadas: SQL Server on-line para migrações de instância sql gerenciadas'
titleSuffix: Azure Database Migration Service
description: Aprenda a usar as funções personalizadas do SQL Server para o Azure SQL Database gerenciadas migrações on-line de instâncias.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254935"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Funções personalizadas para migrações on-line de instância gerenciada do SQL Server para SQL Database

O Azure Database Migration Service usa um ID do APP para interagir com os Serviços Do Azure. O App ID requer a função contribuinte no nível de Assinatura (que muitos departamentos de segurança corporativa não permitirão) ou a criação de funções personalizadas que concedam as permissões específicas que o Serviço de Migrações do banco de dados Azure exige. Uma vez que há um limite de 2.000 funções personalizadas no Azure Active Directory, você pode querer combinar todas as permissões exigidas especificamente pelo ID do APP em uma ou duas funções personalizadas e, em seguida, conceder ao App ID a função personalizada em objetos específicos ou grupos de recursos (vs. no nível de inscrição). Se o número de funções personalizadas não for uma preocupação, você pode dividir as funções personalizadas por tipo de recurso, para criar três funções personalizadas no total, conforme descrito abaixo.

A seção AtribuintesScopes da seqüência de definição de função json string permite controlar onde as permissões aparecem na Interface de **Atribuição de Atribuição** de Função no portal. Você provavelmente vai querer definir a função no grupo de recursos ou até mesmo o nível de recursos para evitar bagunçar a ui com funções extras. Observe que isso não executa a atribuição real do papel.

## <a name="minimum-number-of-roles"></a>Número mínimo de funções

Atualmente recomendamos a criação de um mínimo de duas funções personalizadas para o App ID, uma no nível de recurso e outra no nível de assinatura.

> [!NOTE]
> O último requisito de função personalizada pode eventualmente ser removido, uma vez que o novo código de instância gerenciado do SQL Database é implantado no Azure.

**Função personalizada para o ID do APP**. Essa função é necessária para a migração do Azure Database Migration Service no nível *de recurso* ou grupo *de recursos* (para obter mais informações sobre o App ID, consulte o artigo Use o portal para criar um aplicativo e principal de serviço [do Azure AD que possa acessar recursos).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Função personalizada para o APP ID - assinatura**. Essa função é necessária para a migração do Azure Database Migration Service no nível *de assinatura.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

O json acima deve ser armazenado em três arquivos de texto, e você pode usar os cmdlets AzureRM, AZ PowerShell ou Azure CLI para criar as funções usando **o New-AzureRmRoleDefinition (AzureRM)** ou **o New-AzRoleDefinition (AZ)**.

Para obter mais informações, consulte o artigo [Funções personalizadas para os recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Depois de criar essas funções personalizadas, você deve adicionar atribuições de funções aos usuários e iDs do APP aos recursos ou grupos de recursos apropriados:

* A função "DMS Role - App ID" deve ser concedida ao ID do APP que será usado para as migrações, e também na conta de armazenamento, na instância do Serviço de Migração do Banco de Dados Do Azure e nos níveis de recurso gerenciados do SQL Database.
* A função "DMS Role - App ID - Sub" deve ser concedida ao APP ID no nível de assinatura (a concessão no grupo de recursos ou recursos falhará). Esse requisito é temporário até que uma atualização de código seja implantada.

## <a name="expanded-number-of-roles"></a>Número expandido de funções

Se o número de funções personalizadas em seu Diretório Ativo do Azure não for uma preocupação, recomendamos que você crie um total de três funções. Você ainda precisará da função "DMS Role - App ID – Sub", mas a função "DMS Role - App ID" acima é dividida por tipo de recurso em duas funções diferentes.

**Função personalizada para o App ID para instância gerenciada do Banco de Dados SQL**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Função personalizada para o App ID for Storage**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Atribuição de função

Para atribuir uma função aos usuários/ID do APP, abra o portal do Azure e execute as seguintes etapas:

1. Navegue até o grupo de recursos ou recursos (exceto para a função que precisa ser concedida na assinatura), vá para o **Access Control**e, em seguida, role para encontrar as funções personalizadas que você acabou de criar.

2. Selecione a função apropriada, selecione o ID do APP e salve as alterações.

  Seus IDs do APLICATIVO agora aparecem listados na guia **Atribuições de Função.**

## <a name="next-steps"></a>Próximas etapas

* Revise a orientação de migração para o seu cenário no [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/)da Microsoft .
