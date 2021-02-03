---
title: Use Azure PowerShell para configurar o backup contínuo e a restauração pontual no Azure Cosmos DB.
description: Saiba como provisionar uma conta com backup contínuo e restaurar dados usando o Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 00d9e2c3b77ce4ddae105d8115cd62b5f7796e30
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527240"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-powershell"></a>Configurar e gerenciar backup contínuo e restauração pontual-usando Azure PowerShell

O recurso de restauração pontual do Azure Cosmos DB ajuda você a se recuperar de uma alteração acidental dentro de um contêiner, restaurar uma conta excluída, um banco de dados ou um contêiner ou para restaurar em qualquer região (onde os backups existiam). O modo de backup contínuo permite que você faça a restauração para qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como provisionar uma conta com backup contínuo e restaurar dados usando o Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Instalar Azure PowerShell

1. Execute o seguinte comando de Azure PowerShell para instalar o `Az.CosmosDB` módulo de visualização, que contém os comandos relacionados à restauração pontual:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Depois de instalar os módulos, faça logon no Azure usando

   ```azurepowershell
   Connect-AzAccount
   ```

1. Selecione uma assinatura específica com o seguinte comando:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Provisionar uma conta da API do SQL com backup contínuo

Para provisionar uma conta com backup contínuo, adicione um argumento `-BackupPolicyType Continuous` junto com o comando de provisionamento regular.

O cmdlet a seguir é um exemplo de uma conta de gravação de região única `pitracct2` com a política de backup contínua criada na região "oeste dos EUA" no grupo de recursos "myrg":

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Provisionar uma conta da API do MongoDB com backup contínuo

O cmdlet a seguir é um exemplo de conta de backup contínua "pitracct2" criada na região "oeste dos EUA" no grupo de recursos "myrg":

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Disparar uma operação de restauração

O cmdlet a seguir é um exemplo para disparar uma operação de restauração com o comando Restore usando a conta de destino, a conta de origem, o local, o grupo de recursos e o carimbo de data/hora:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Exemplo 1:** Restaurando a conta inteira:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Exemplo 2:** Restaurando coleções e bancos de dados específicos. Este exemplo restaura as coleções mycol1, myCol2 de myDB1 e todo o banco de dados myDB2, que inclui todos os contêineres sob ele.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerar recursos restauráveis para a API do SQL

Os cmdlets de enumeração ajudam a descobrir os recursos que estão disponíveis para restauração em vários carimbos de data/hora. Além disso, eles também fornecem um feed de eventos importantes sobre a conta restaurável, o banco de dados e os recursos de contêiner.

**Listar todas as contas que podem ser restauradas na assinatura atual**

Execute o `Get-AzCosmosDBRestorableDatabaseAccount` comando do PowerShell para listar todas as contas que podem ser restauradas na assinatura atual.

A resposta inclui todas as contas de banco de dados (ao vivo e excluídas) que podem ser restauradas e as regiões das quais elas podem ser restauradas.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Assim como o "CreationTime" ou "Requery" para a conta, também há um "CreationTime" ou um "de exclusão" para a região. Esses tempos permitem que você escolha a região correta e um intervalo de tempo válido para restaurar nessa região.

**Listar todas as versões de bancos de dados SQL em uma conta de banco de dados ao vivo**

A listagem de todas as versões de bancos de dados permite que você escolha o banco de dados correto em um cenário em que a hora real da existência do banco de dados é desconhecida.

Execute o seguinte comando do PowerShell para listar todas as versões de bancos de dados. Esse comando funciona apenas com contas dinâmicas. Os parâmetros "DatabaseAccountInstanceId" e "LocationName" são obtidos das propriedades "Name" e "Location" na resposta do `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet. O atributo "DatabaseAccountInstanceId" refere-se à propriedade "instanceId" da conta do banco de dados de origem que está sendo restaurada:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Listar todas as versões de contêineres SQL de um banco de dados em uma conta de banco de dados ao vivo.**

Use o comando a seguir para listar todas as versões de contêineres SQL. Esse comando funciona apenas com contas dinâmicas. O parâmetro "DatabaseRid" é o "ResourceId" do banco de dados que você deseja restaurar. É o valor do atributo "ownerResourceid" encontrado na resposta do `Get-AzCosmosdbSqlRestorableDatabase` cmdlet. A resposta também inclui uma lista de operações executadas em todos os contêineres dentro deste banco de dados.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Localizar bancos de dados ou contêineres que podem ser restaurados em qualquer carimbo de data/hora específico**

Use o comando a seguir para obter a lista de bancos de dados ou contêineres que podem ser restaurados em qualquer carimbo de data/hora específico. Esse comando funciona apenas com contas dinâmicas.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Enumerar recursos restauráveis para o MongoDB

Os comandos de enumeração descritos abaixo ajudam a descobrir os recursos que estão disponíveis para restauração em vários carimbos de data/hora. Além disso, eles também fornecem um feed de eventos importantes sobre a conta restaurável, o banco de dados e os recursos de contêiner. Esses comandos só funcionam para contas dinâmicas e são semelhantes aos comandos da API do SQL, mas com "MongoDB" no nome do comando em vez de "SQL".

**Listar todas as versões de bancos de dados do MongoDB em uma conta de banco de dados ao vivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Listar todas as versões de coleções do MongoDB de um banco de dados em uma conta de banco de dados ao vivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Listar todos os recursos de uma conta de banco de dados MongoDB que estão disponíveis para restauração em um determinado carimbo de data/hora e região**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando [CLI do Azure](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md)ou [portal do Azure](continuous-backup-restore-portal.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
