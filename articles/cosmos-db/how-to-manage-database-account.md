---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Saiba como gerenciar recursos de Azure Cosmos DB usando os modelos portal do Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247378"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerenciar uma conta do Azure Cosmos

Este artigo descreve como gerenciar várias tarefas em uma conta do Azure Cosmos usando o portal do Azure, o Azure PowerShell, a CLI do Azure e modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

Consulte [criar uma conta de Azure Cosmos DB com CLI do Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>PowerShell do Azure

Consulte [criar uma conta de Azure Cosmos DB com o PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Modelo do Azure Resource Manager

Este modelo de Azure Resource Manager criará uma conta do Azure Cosmos para a API do SQL configurada com duas regiões e opções para selecionar nível de consistência, failover automático e vários mestres. Para implantar esse modelo, clique em Implantar no Azure na página do arquivo Leiame, [Criar uma conta do Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar ou remover regiões de sua conta de banco de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

1. Para adicionar regiões, selecione os hexágonos no mapa com o rótulo **+** que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, escolha a opção **+Adicionar região** e escolha uma região no menu suspenso.

1. Para remover regiões, desmarque uma ou mais regiões do mapa marcando os hexágonos azuis com marcas de seleção. Ou escolha o ícone de "cesto de lixo" (🗑) ao lado da região no canto direito.

1. Para salvar suas alterações, escolha **OK**.

   ![Adicionar ou remover o menu de regiões](./media/how-to-manage-database-account/add-region.png)

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deverá realizar o failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você poderá adicionar ou remover qualquer região, se você tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

Consulte [Adicionar ou remover regiões com CLI do Azure](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>PowerShell do Azure

Consulte [Adicionar ou remover regiões com o PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra a guia **Replicar Dados Globalmente** e selecione **Habilitar** para habilitar gravações de várias regiões. Depois que você habilitar gravações de várias regiões, todas as regiões de leitura que você tiver na conta no momento se tornarão regiões de leitura e gravação.

![Captura de tela da configuração de vários mestres da conta do Azure Cosmos](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

Consulte [habilitar regiões de várias gravações com CLI do Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>PowerShell do Azure

Consulte [habilitar regiões de várias gravações com o PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>modelo do Resource Manager

Uma conta pode ser migrada de mestre único para vários mestres com a implantação do modelo do Resource Manager usado para criar a conta e a configuração `enableMultipleWriteLocations: true`. O modelo do Azure Resource Manager a seguir é um modelo mínimo possível que implantará uma conta do Azure Cosmos para a API do SQL com duas regiões e vários locais de gravação habilitados.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Habilitar failover automático a conta do Azure Cosmos

A opção Failover automático permite que o Azure Cosmos DB faça failover para a região com a prioridade mais alta de failover sem nenhuma ação do usuário, caso uma região fique indisponível. Quando o failover automático estiver habilitado, a prioridade de região poderá ser modificada. A conta precisa ter duas ou mais regiões para habilitar o failover automático.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

Consulte [habilitar failover automático com CLI do Azure](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>PowerShell do Azure

Consulte [habilitar failover automático com o PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para a conta do Azure Cosmos

Depois que uma conta do Cosmos for configurada para failover automático, a prioridade de failover para regiões poderá ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de gravação (prioridade de failover igual a zero) quando a conta está configurada para failover automático. Para alterar a região de gravação, é necessário desabilitar o failover automático e fazer um failover manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**.

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**.

4. Para modificar a prioridade de failover, arraste as regiões de leitura nos três pontos à esquerda da linha que aparecem quando o mouse é passado sobre ela.

5. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

Consulte [definir a prioridade de failover com CLI do Azure](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>PowerShell do Azure

Consulte [definir a prioridade de failover com o PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Fazer um failover manual em uma conta do Azure Cosmos

> [!IMPORTANT]
> A conta do Azure Cosmos precisa ser configurada para failover manual para essa operação ser bem-sucedida.

O processo de execução de um failover manual envolve a alteração da região de gravação da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> As contas de vários mestres não podem ter um failover manual. Para aplicativos que usam o SDK do Azure Cosmos, o SDK detectará quando uma região ficar indisponível e, em seguida, fará o redirecionamento automático para a próxima região mais perto se a API de hospedagem múltipla estiver sendo usada no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

2. Na parte superior do menu, escolha **Failover Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **Failover Manual**, escolha a nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção alterará sua região de gravação.

4. Para disparar o failover, escolha **OK**.

   ![Menu do portal de failover manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

Consulte [disparar failover manual com CLI do Azure](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>PowerShell do Azure

Consulte [disparar failover manual com o PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como o banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar o Azure Cosmos DB usando o Azure PowerShell](manage-with-powershell.md)
* [Gerenciar o Azure Cosmos DB usando a CLI do Azure](manage-with-cli.md)
