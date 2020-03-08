---
title: Como mover o recurso de serviço entre regiões
titleSuffix: Azure Cognitive Search
description: Este artigo mostrará como mover seus recursos do Azure Pesquisa Cognitiva de uma região para outra na nuvem do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: 183a937a232dbd28962bb7d6ef42b0d78b8a81fd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850679"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Mover o serviço de Pesquisa Cognitiva do Azure para outra região do Azure

Ocasionalmente, os clientes consultam sobre como mover um serviço de pesquisa existente para outra região. No momento, não existem mecanismos ou ferramentas internas para ajudá-lo com essa tarefa. Ele continua sendo um processo manual, descrito abaixo neste artigo.

> [!NOTE]
> Na portal do Azure, todos os serviços têm um comando **Exportar modelo** . No caso do Azure Pesquisa Cognitiva, esse comando produz uma definição básica de um serviço (nome, local, camada, réplica e contagem de partições), mas não reconhece o conteúdo do serviço nem carrega chaves, funções ou logs. Embora o comando exista, não é recomendável usá-lo para mover um serviço de pesquisa.

## <a name="steps-for-moving-a-service"></a>Etapas para mover um serviço

Se você precisar mover um serviço de pesquisa para uma região diferente, sua abordagem deverá ser semelhante às etapas abaixo:

1. Identifique os serviços relacionados para entender o impacto total da realocação de um serviço. Você pode estar usando o armazenamento do Azure para registro em log, armazenamento de conhecimento ou como uma fonte de dados externa. Você pode estar usando serviços cognitivas para enriquecimento de ia. O acesso a serviços em outras regiões é comum, mas vem com encargos de largura de banda adicionais. Serviços cognitivas e Pesquisa Cognitiva do Azure devem estar na mesma região se você estiver usando o enriquecimento de ia.

1. Inventariar seu serviço existente para obter uma lista completa de objetos no serviço. Se você habilitou o registro em log, crie e arquive os relatórios que talvez precisem para um registro histórico.

1. Verifique os preços e a disponibilidade na nova região para garantir a disponibilidade do Azure Pesquisa Cognitiva mais todos os serviços relacionados que você talvez queira criar na mesma região. Verifique a paridade do recurso. Alguns recursos de visualização têm a disponibilidade restrita.

1. Crie um serviço na nova região e Republique-o do código-fonte quaisquer índices, indexadores, fontes de dados, habilidades, lojas de conhecimento e mapas de sinônimos existentes. Os nomes de serviço devem ser exclusivos, portanto, não é possível reutilizar o nome existente.

1. Recarregue os índices e as lojas de conhecimento, se aplicável. Você usará o código do aplicativo para enviar dados JSON por push para um índice ou executar novamente indexadores para efetuar pull de documentos de fontes externas. 

1. Habilite o registro em log e, se estiver usando-os, recrie as funções de segurança.

1. Atualize os aplicativos cliente e os conjuntos de testes para usar o novo nome de serviço e as chaves de API e testar todos os aplicativos.

1. Exclua o serviço antigo depois que o novo serviço for totalmente testado e operacional.

## <a name="next-steps"></a>Próximas etapas

+ [Escolher uma camada](search-sku-tier.md)
+ [Criar um serviço de pesquisa](search-create-service-portal.md)
+ [Carregar documentos de pesquisa](search-what-is-data-import.md)
+ [Habilitar o registro em log](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->