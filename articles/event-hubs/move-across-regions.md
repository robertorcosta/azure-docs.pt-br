---
title: Mova um namespace do Azure Event Hubs para outra região | Microsoft Docs
description: Este artigo mostra como mover um namespace do Azure Event Hubs da região atual para outra região.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606802"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mova um namespace do Azure Event Hubs para outra região
Existem vários cenários em que você gostaria de mover seu espaço de nome do Event Hubs existente de uma região para outra. Por exemplo, você pode querer criar um namespace com a mesma configuração para testes. Você também pode querer criar um namespace secundário em outra região como parte do planejamento de recuperação de [desastres](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Este artigo mostra como exportar um modelo do Azure Resource Manager para um namespace existente do Event Hubs e, em seguida, usar o modelo para criar um namespace com as mesmas configurações em outra região. No entanto, esse processo não move eventos que ainda não são processados. Você precisa processar os eventos do namespace original antes de excluí-lo.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os serviços e recursos que sua conta usa sejam suportados na região de destino.
- Para recursos de visualização, certifique-se de que sua assinatura esteja listada em branco para a região-alvo.
- Se você tiver **o recurso de captura** ativado para hubs de eventos no namespace, mova o [Azure Storage ou o Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) ou a [azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o namespace do Event Hubs. Você também pode mover o grupo de recursos que contém espaços de nome de Storage e Event Hubs para a outra região, seguindo etapas semelhantes às descritas neste artigo. 
- Se o namespace do Event Hubs estiver em um **cluster Event Hubs,** [crie um cluster dedicado](event-hubs-dedicated-cluster-create-portal.md) na região de **destino** antes de passar por etapas neste artigo. 

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo de Gerenciador de recursos. Este modelo contém configurações que descrevem o namespace do Event Hubs.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **Todos os recursos** e selecione o namespace do Event Hubs.

3. Selecione >**modelo de exportação** **de configurações** > .

4. Escolha **Baixar** na página **Exportar modelo.**

    ![Baixar modelo do Gerenciador de recursos](./media/move-across-regions/download-template.png)

5. Localize o arquivo .zip que você baixou do portal e descompacte esse arquivo para uma pasta de sua escolha.

   Este arquivo zip contém os arquivos .json que incluem o modelo e scripts para implantar o modelo.


## <a name="move"></a>Mover

Implante o modelo para criar um namespace do Event Hubs na região de destino. 


1. No portal Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.

3. Selecione **Implantação de modelo**.

4. Selecione **Criar**.

5. Selecione **Criar seu próprio modelo no editor**.

6. Selecione **'Carregar arquivo'** e siga as instruções para carregar o arquivo **template.json** que você baixou na última seção.

7. Selecione **Salvar** para salvar o modelo. 

8. Na página **de implantação Personalizada,** siga estas etapas: 

    1. Selecione uma **assinatura**do Azure . 

    2. Selecione um **grupo de recursos** existente ou crie um. Se o namespace de origem estiver em um cluster Event Hubs, selecione o grupo de recursos que contém cluster na região de destino. 

    3. Selecione o **local** ou região do destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 

    4. Na seção **CONFIGURAÇÕES,** faça as seguintes etapas:
    
        1. digite o novo **nome do namespace**. 

            ![Implantar modelo de gerenciador de recursos](./media/move-across-regions/deploy-template.png)

        2. Se o namespace de origem estiver em um **cluster Event Hubs,** digite nomes de grupo de **recursos** e cluster **Event Hubs** como parte do **ID externo**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o hub de eventos em seu namespace usar uma conta de armazenamento `StorageAccounts_<original storage account name>_external` para capturar eventos, especifique o nome do grupo de recursos e a conta de armazenamento para campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione a caixa de seleção **Concordo com os termos e condições declarados acima**. 
    
    6. Agora, **selecione Selecionar Compra** para iniciar o processo de implantação. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser recomeçar, você pode excluir o namespace do **Event Hubs de destino**e repetir as etapas descritas nas seções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para comprometer as alterações e concluir a movimentação de um namespace do Event Hubs, exclua o **namespace do Event Hubs de origem**. Certifique-se de que você processou todos os eventos no namespace antes de excluir o namespace. 

Para excluir um namespace do Event Hubs (origem ou destino) usando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, digite **Event Hubs**e selecione **Event Hubs** a partir dos resultados da pesquisa. Você vê os espaços de nomes do Event Hubs em uma lista.

2. Selecione o namespace de destino para excluir e **selecione Excluir** na barra de ferramentas. 

    ![Excluir namespace - botão](./media/move-across-regions/delete-namespace-button.png)

3. Na página **Excluir recursos*** verifique os recursos selecionados e confirme a exclusão digitando **sim**e, em seguida, **selecione Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um namespace do Azure Event Hubs de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
