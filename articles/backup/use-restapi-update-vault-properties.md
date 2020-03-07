---
title: Atualizar a configuração do cofre dos serviços de recuperação com a API REST
description: Neste artigo, saiba como atualizar a configuração do cofre usando a API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395558"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Atualizar as configurações do cofre dos serviços de recuperação do Azure usando a API REST

Este artigo descreve como atualizar configurações relacionadas a backup no cofre dos serviços de recuperação do Azure usando a API REST.

## <a name="soft-delete-state"></a>Estado de exclusão reversível

Excluir backups de um item protegido é uma operação significativa que deve ser monitorada. Para proteger contra exclusões acidentais, o cofre dos serviços de recuperação do Azure tem uma funcionalidade de exclusão reversível. Essa funcionalidade permite que os clientes restaurem backups excluídos, se necessário, dentro de um período de tempo após a exclusão.

Mas há cenários em que esse recurso não é necessário. Um cofre dos serviços de recuperação do Azure não poderá ser excluído se houver itens de backup dentro dele, até mesmo os excluídos de maneira reversível. Isso pode representar um problema se o cofre precisar ser excluído imediatamente. Por exemplo: as operações de implantação geralmente limpam os recursos criados no mesmo fluxo de trabalho. Uma implantação pode criar um cofre, configurar backups para um item, fazer uma restauração de teste e, em seguida, continuar a excluir os itens de backup e o cofre. Se a exclusão do cofre falhar, a implantação inteira poderá falhar. Desabilitar a exclusão reversível é a única maneira de garantir a exclusão imediata.

Portanto, o cliente precisa escolher cuidadosamente se deseja ou não desabilitar a exclusão reversível para um cofre específico, dependendo do cenário. Para obter mais informações, consulte o [artigo exclusão reversível](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Buscar o estado de exclusão reversível usando a API REST

Por padrão, o estado de exclusão reversível será habilitado para qualquer cofre de serviços de recuperação criado recentemente. Para buscar/atualizar o estado de exclusão reversível para um cofre, use o [documento da API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) relacionada à configuração do cofre de backup

Para buscar o estado atual de exclusão reversível para um cofre, use a seguinte operação *Get*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O URI GET tem `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são fornecidos no URI, não é necessário ter o corpo da solicitação separado.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Respostas

A resposta bem-sucedida para a operação ' GET ' é mostrada abaixo:

|{1&gt;Nome&lt;1}  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemplo de resposta

Depois que a solicitação ' GET ' for enviada, uma resposta 200 (bem-sucedida) será retornada.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Atualizar o estado de exclusão reversível usando a API REST

Para atualizar o estado de exclusão reversível do cofre dos serviços de recuperação usando a API REST, use a seguinte operação de *patch*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O URI do PATCH tem `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Se substituirmos o URI pelos valores acima, o URI terá a seguinte aparência.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo da solicitação

As seguintes definições comuns são usadas para criar um corpo de solicitação

Para obter mais detalhes, consulte [a documentação da API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|{1&gt;Nome&lt;1}  |Obrigatório  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag Opcional       |
|local     |  {1&gt;true&lt;1}       |String         |   Localização do recurso      |
|propriedades     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|marcas     |         | Object        |     Marcações de recursos    |

#### <a name="example-request-body"></a>Exemplo do corpo de solicitação

O exemplo a seguir é usado para atualizar o estado de exclusão reversível para ' disabled '.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Respostas

A resposta bem-sucedida para a operação ' PATCH ' é mostrada abaixo:

|{1&gt;Nome&lt;1}  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemplo de resposta

Depois que a solicitação ' PATCH ' for enviada, uma resposta 200 (bem-sucedida) será retornada.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[Crie uma política de backup para fazer backup de uma VM do Azure neste Vault](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs REST do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
