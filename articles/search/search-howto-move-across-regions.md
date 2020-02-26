---
title: Como mover o recurso de serviço entre regiões
titleSuffix: Azure Cognitive Search
description: Este artigo mostrará como mover seus recursos do Azure Pesquisa Cognitiva de uma região para outra na nuvem do Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599295"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Mover o serviço de Pesquisa Cognitiva do Azure para outra região do Azure

Para mover sua conta de serviço cognitiva do Azure de uma região para outra, você criará um modelo de exportação para mover suas assinaturas. Depois de mover sua assinatura, você precisará mover seus dados e recriar o serviço.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Exportar um modelo.
> * Modifique o modelo: adicionando a região de destino, os nomes de conta de armazenamento e de pesquisa.
> * Implante o modelo para criar novas contas de pesquisa e de armazenamento.
> * Verifique o status do serviço na nova região
> * Limpe os recursos na região de origem.

## <a name="prerequisites"></a>Prerequisites

- Verifique se os serviços e recursos que sua conta usa têm suporte na região de destino.

- Para recursos de visualização, verifique se sua assinatura está na lista de permissões para a região de destino. Para obter mais informações sobre recursos de visualização, consulte [lojas de conhecimento](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [enriquecimento incremental](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)e [ponto de extremidade privado](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Avaliação e planejamento

Ao mover o serviço de pesquisa para a nova região, você precisará [mover seus dados para o novo serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) e, em seguida, recriar seus índices, habilidades e armazenamentos de conhecimento. Você deve registrar as configurações atuais e copiar arquivos JSON para tornar a recriação de seu serviço mais fácil e rápida.

## <a name="moving-your-search-services-resources"></a>Movendo os recursos do serviço de pesquisa

Para começar, você vai exportar e modificar um modelo do Resource Manager.

### <a name="export-a-template"></a>Exportar um modelo

1. Entre no [portal do Azure](https://portal.azure.com).

2. Vá para a página do grupo de recursos.

> [!div class="mx-imgBorder"]
> ![exemplo de página do grupo de recursos](./media/search-move-resource/export-template-sample.png)

3. Selecione **Todos os recursos**.

3. No menu de navegação à esquerda, selecione **Exportar modelo**.

4. Escolha **baixar** na página **Exportar modelo** .

5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha.

O arquivo zip contém os arquivos. JSON que compõem o modelo e os scripts para implantar o modelo.

### <a name="modify-the-template"></a>Modificar o modelo

Você modificará o modelo alterando as regiões e os nomes de conta de armazenamento e pesquisa. Os nomes devem seguir as regras para cada serviço e convenções de nomenclatura de região. 

Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.

1. No Portal do Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.

3. Selecione **Implantação de modelo**.

4. Selecione **Criar**.

5. Selecione **Criar seu próprio modelo no editor**.

6. Selecione **carregar arquivo**e siga as instruções para carregar o arquivo **Template. JSON** que você baixou e descompactou na seção anterior.

7. No arquivo **Template. JSON** , nomeie a pesquisa de destino e as contas de armazenamento definindo o valor padrão dos nomes de conta de armazenamento e de pesquisa. 

8. Edite a propriedade **Location** no arquivo **Template. JSON** para a região de destino dos serviços de pesquisa e armazenamento. Este exemplo define a região de destino como `centralus`.

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

### <a name="deploy-the-template"></a>Implantar o modelo

1. Salve o arquivo **Template. JSON** .

2. Insira ou selecione os valores de propriedade:

- **Assinatura**: selecione uma assinatura do Azure.

- **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.

- **Local**: selecione um local do Azure.

3. Clique na caixa de seleção **eu concordo com os termos e condições declarados acima** e clique no botão **selecionar compra** .

## <a name="verifying-your-services-status-in-new-region"></a>Verificando o status dos serviços em Nova região

Para verificar a movimentação, abra o novo grupo de recursos e seus serviços serão listados com a nova região.

Para mover seus dados da região de origem para a região de destino, consulte as diretrizes deste artigo para [mover seus dados para a nova conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Limpar recursos em sua região original

Para confirmar as alterações e concluir a movimentação de sua conta de serviço, exclua a conta de serviço de origem.

## <a name="next-steps"></a>Próximas etapas

[Criar um índice](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Criar um conjunto de habilidades](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Criar uma loja de conhecimento](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

