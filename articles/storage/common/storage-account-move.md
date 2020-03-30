---
title: Mova uma conta do Azure Storage para outra região | Microsoft Docs
description: Mostra como mover uma conta do Azure Storage para outra região.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838712"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Mova uma conta do Azure Storage para outra região

Para mover uma conta de armazenamento, crie uma cópia da sua conta de armazenamento em outra região. Em seguida, mova seus dados para essa conta usando o AzCopy ou outra ferramenta de sua escolha.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> 
> * Exportar um modelo.
> * Modifique o modelo adicionando a região de destino e o nome da conta de armazenamento.
> * Implante o modelo para criar a nova conta de armazenamento.
> * Configure a nova conta de armazenamento.
> * Mova os dados para a nova conta de armazenamento.
> * Exclua os recursos na região de origem.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os serviços e recursos que sua conta usa sejam suportados na região de destino.

- Para recursos de visualização, certifique-se de que sua assinatura esteja listada em branco para a região-alvo.

<a id="prepare" />

## <a name="prepare"></a>Preparar

Para começar, exporte e modifique um modelo de Gerenciador de recursos. 

### <a name="export-a-template"></a>Exportar um modelo

Este modelo contém configurações que descrevem sua conta de armazenamento. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo usando o portal Azure:

1. Faça login no [portal Azure](https://portal.azure.com).

2. Selecione **Todos os recursos** e selecione sua conta de armazenamento.

3. Selecione >**modelo de exportação** **de configurações** > .

4. Escolha **Baixar** na lâmina **do modelo Exportar.**

5. Localize o arquivo .zip que você baixou do portal e descompacte esse arquivo para uma pasta de sua escolha.

   Este arquivo zip contém os arquivos .json que compõem o modelo e scripts para implantar o modelo.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para exportar um modelo usando o PowerShell:

1. Faça login na sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para assinatura da conta de armazenamento que você deseja mover.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporte o modelo de sua conta de armazenamento de origem. Esses comandos salvam um modelo json no diretório atual.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Modificar o modelo 

Modifique o modelo alterando o nome e a região da conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para implantar o modelo usando o portal Azure:

1. No portal Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.

3. Selecione **Implantação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecione **Criar**.

5. Selecione **Criar seu próprio modelo no editor**.

6. Selecione **'Carregar arquivo'** e siga as instruções para carregar o arquivo **template.json** que você baixou na última seção.

7. No arquivo **template.json,** nomeie a conta de armazenamento de destino definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor padrão `mytargetaccount`do nome da conta de armazenamento para .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Para obter códigos de localização da região, consulte [Azure Localizações](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **central dos EUA.** = **centralus**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para implantar o modelo usando o PowerShell:

1. No arquivo **template.json,** nomeie a conta de armazenamento de destino definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor padrão `mytargetaccount`do nome da conta de armazenamento para .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Edite a propriedade **de localização** no arquivo **template.json** para a região de destino. Este exemplo define a `eastus`região alvo para .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Você pode obter códigos de região executando o comando [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Mover

Implante o modelo para criar uma nova conta de armazenamento na região de destino. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Salve o **arquivo template.json.**

2. Digite ou selecione os valores da propriedade:

- **Assinatura**: selecione uma assinatura do Azure.

- **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.

- **Localização**: Selecione uma localização do Azure.

3. Clique no **Concordo com os termos e condições indicados acima** da caixa de seleção e, em seguida, clique no botão Selecionar **Compra.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Obtenha o ID de assinatura onde você deseja implantar o IP público de destino com [a Assinatura Get-Az](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Use esses comandos para implantar seu modelo:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Configure a nova conta de armazenamento

Alguns recursos não serão exportados para um modelo, então você terá que adicioná-los à nova conta de armazenamento. 

A tabela a seguir lista esses recursos, juntamente com orientações para adicioná-los à sua nova conta de armazenamento.

| Recurso    | Orientação    |
|--------|-----------|
| **Políticas de gerenciamento do ciclo de vida** | [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Sites estáticos** | [Hospede um site estático no Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Assinaturas de evento** | [Reagir aos eventos de armazenamento de Blobs](../blobs/storage-blob-event-overview.md) |
| **Alertas** | [Crie, visualize e gerencie alertas de registro de atividades usando o Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **CDN (Rede de Distribuição de Conteúdo)** | [Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se você configurar um CDN para a conta de armazenamento de origem, basta alterar a origem do seu CDN existente para o ponto final de serviço blob principal (ou o ponto final do site estático principal) da sua nova conta. 

### <a name="move-data-to-the-new-storage-account"></a>Mova dados para a nova conta de armazenamento

Aqui estão algumas maneiras de mover seus dados.

:heavy_check_mark: **Azure Storage Explorer**

  É fácil de usar e adequado para pequenos conjuntos de dados. Você pode copiar contêineres e arquivos e colar na conta-alvo.

  Consulte [o Azure Storage Explorer;](https://azure.microsoft.com/features/storage-explorer/)

:heavy_check_mark: **AzCopy**

  Esta é a abordagem preferida. É otimizado para desempenho.  Uma maneira que é mais rápido, é que os dados são copiados diretamente entre servidores de armazenamento, para que o AzCopy não use a largura de banda da rede do seu computador. Use a AzCopy na linha de comando ou como parte de um script personalizado.

  Veja [Começar com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Fábrica de Dados Azure** 

  Use esta ferramenta somente se precisar de uma funcionalidade que não seja suportada na versão atual do AzCopy. Por exemplo, na versão atual do AzCopy, você não pode copiar blobs entre contas que têm um namespace hierárquico. Além disso, o AzCopy não preserva listas de controle de acesso a arquivos ou carimbos de tempo de arquivo (Por exemplo: criar e modificar carimbos de tempo). 

  Veja estes links:
  - [Copiar dados para ou do armazenamento Do Azure Blob usando a Fábrica de Dados Do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Copiar dados de/para o Azure Data Lake Storage Gen2 usando o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiar dados de ou para o Armazenamento de Arquivos do Azure usando o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Copiar dados para e do Armazenamento de Tabelas do Azure usando o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Descartar ou limpar

Após a implantação, se você quiser recomeçar, você pode excluir a conta de armazenamento de destino e repetir as etapas descritas nas seções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para cometer as alterações e concluir a movimentação de uma conta de armazenamento, exclua a conta de armazenamento de origem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover uma conta de armazenamento usando o portal Azure:

1. No portal Azure, expanda o menu do lado esquerdo para abrir o menu de serviços e escolha contas de **armazenamento** para exibir a lista de suas contas de armazenamento.

2. Localize a conta de armazenamento de destino para excluir e clique com o botão Com o botão **Mais** (**...**) no lado direito da listagem.

3. Selecione **Excluir**e confirme.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzStorageAccount:](/powershell/module/az.resources/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma conta de armazenamento do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
