---
title: Atualizar configuração do cofre dos Serviços de Recuperação com API REST
description: Neste artigo, saiba como atualizar a configuração do cofre usando a API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252357"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Atualize as configurações do Cofre de Serviços de Recuperação do Azure usando a API REST

Este artigo descreve como atualizar configurações relacionadas ao backup no cofre do Azure Recovery Services usando a API REST.

## <a name="soft-delete-state"></a>Estado de exclusão suave

A exclusão de backups de um item protegido é uma operação significativa que deve ser monitorada. Para proteger contra exclusões acidentais, o cofre do Azure Recovery Services possui um recurso de exclusão suave. Esse recurso permite que os clientes restaurem backups excluídos, se necessário, dentro de um período de tempo após a exclusão.

Mas há cenários em que essa capacidade não é necessária. Um cofre do Azure Recovery Services não pode ser excluído se houver itens de backup dentro dele, mesmo os excluídos suavemente. Isso pode representar um problema se o cofre precisar ser imediatamente excluído. Por exemplo: as operações de implantação geralmente limpam os recursos criados no mesmo fluxo de trabalho. Uma implantação pode criar um cofre, configurar backups para um item, fazer uma restauração de teste e, em seguida, proceder à exclusão dos itens de backup e do cofre. Se a exclusão do cofre falhar, toda a implantação pode falhar. Desativar a exclusão suave é a única maneira de garantir a exclusão imediata.

Portanto, o cliente precisa escolher cuidadosamente se deve ou não desativar a exclusão suave de um cofre específico, dependendo do cenário. Para obter mais informações, consulte o [artigo soft-delete](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Buscar estado de exclusão suave usando a API REST

Por padrão, o estado de exclusão suave será habilitado para qualquer cofre de Serviços de Recuperação recém-criado. Para buscar/atualizar o estado de soft-delete para um cofre, use o [documento de API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) relacionado ao cofre de backup

Para buscar o estado atual de soft-delete para um cofre, use a seguinte operação *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O GET `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}` tem, parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são fornecidos no URI, não é necessário ter o corpo da solicitação separado.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Respostas

A resposta bem-sucedida para a operação 'GET' é mostrada abaixo:

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemplo de resposta

Uma vez que a solicitação 'GET' é enviada, uma resposta de 200 (bem-sucedida) é retornada.

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

### <a name="update-soft-delete-state-using-rest-api"></a>Atualizar o estado de exclusão suave usando a API REST

Para atualizar o estado de exclusão suave do cofre de serviços de recuperação usando a API REST, use a seguinte operação *PATCH*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O PATCH `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}` tem , , parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Se substituirmos o URI pelos valores acima, então o URI ficará assim.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo da solicitação

THe seguindo definições comuns são usados para criar um corpo de solicitação

Para obter mais detalhes, consulte [a documentação da API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Nome  |Obrigatório  |Type  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag Opcional       |
|local     |  true       |String         |   Localização do recurso      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|marcas     |         | Objeto        |     Marcações de recursos    |

#### <a name="example-request-body"></a>Exemplo do corpo de solicitação

O exemplo a seguir é usado para atualizar o estado de exclusão suave para 'desativado'.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Respostas

A resposta bem-sucedida para a operação 'PATCH' é mostrada abaixo:

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exemplo de resposta

Uma vez que a solicitação 'PATCH' é enviada, uma resposta de 200 (bem-sucedida) é retornada.

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

## <a name="next-steps"></a>Próximas etapas

[Crie uma política de backup para fazer backup de uma VM do Azure neste Vault](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs REST do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
