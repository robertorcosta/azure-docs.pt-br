---
title: Mover um namespace de hubs de eventos do Azure para outra região | Microsoft Docs
description: Este artigo mostra como mover um namespace de hubs de eventos do Azure da região atual para outra região.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606802"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mover um namespace de hubs de eventos do Azure para outra região
Há vários cenários em que você deseja mover o namespace de seus hubs de eventos existentes de uma região para outra. Por exemplo, talvez você queira criar um namespace com a mesma configuração para teste. Você também pode querer criar um namespace secundário em outra região como parte do [planejamento de recuperação de desastre](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Este artigo mostra como exportar um modelo de Azure Resource Manager para um namespace de hubs de eventos existente e, em seguida, usar o modelo para criar um namespace com as mesmas definições de configuração em outra região. No entanto, esse processo não move eventos que ainda não foram processados. Você precisa processar os eventos do namespace original antes de excluí-lo.

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se os serviços e recursos que sua conta usa têm suporte na região de destino.
- Para recursos de visualização, verifique se sua assinatura está na lista de permissões para a região de destino.
- Se você tiver o **recurso de captura** habilitado para hubs de eventos no namespace, mova o [armazenamento do Azure ou Azure data Lake Store gen 2](../storage/common/storage-account-move.md) ou Azure data Lake Store contas [Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o namespace dos hubs de eventos. Você também pode mover o grupo de recursos que contém os namespaces de armazenamento e hubs de eventos para a outra região seguindo as etapas semelhantes às descritas neste artigo. 
- Se o namespace de hubs de eventos estiver em um **cluster de hubs de eventos**, [crie um cluster dedicado](event-hubs-dedicated-cluster-create-portal.md) na **região de destino** antes de percorrer as etapas neste artigo. 

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Este modelo contém configurações que descrevem seu namespace de hubs de eventos.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione seu namespace de hubs de eventos.

3. Selecione **configurações** > de >**modelo de exportação**.

4. Escolha **baixar** na página **Exportar modelo** .

    ![Baixar modelo do Resource Manager](./media/move-across-regions/download-template.png)

5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha.

   Esse arquivo zip contém os arquivos. JSON que incluem o modelo e os scripts para implantar o modelo.


## <a name="move"></a>Mover

Implante o modelo para criar um namespace de hubs de eventos na região de destino. 


1. Na portal do Azure, selecione **criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.

3. Selecione **Implantação de modelo**.

4. Selecione **Criar**.

5. Selecione **Criar seu próprio modelo no editor**.

6. Selecione **carregar arquivo**e siga as instruções para carregar o arquivo **Template. JSON** que você baixou na última seção.

7. Selecione **salvar** para salvar o modelo. 

8. Na página **implantação personalizada** , siga estas etapas: 

    1. Selecione uma **assinatura**do Azure. 

    2. Selecione um **grupo de recursos** existente ou crie um. Se o namespace de origem estava em um cluster de hubs de eventos, selecione o grupo de recursos que contém o cluster na região de destino. 

    3. Selecione o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 

    4. Na seção **configurações** , execute as seguintes etapas:
    
        1. Insira o novo **nome do namespace**. 

            ![Implantar modelo do Resource Manager](./media/move-across-regions/deploy-template.png)

        2. Se o namespace de origem estava em **um cluster de hubs de eventos**, insira os nomes do grupo de **recursos** e do cluster de hubs de **eventos** como parte da **ID externa**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o Hub de eventos em seu namespace usar uma conta de armazenamento para capturar eventos, especifique o nome do grupo de recursos `StorageAccounts_<original storage account name>_external` e a conta de armazenamento para o campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione a caixa de seleção **Concordo com os termos e condições declarados acima**. 
    
    6. Agora, selecione **selecionar compra** para iniciar o processo de implantação. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser começar novamente, poderá excluir o namespace de **hubs de eventos de destino**e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um namespace de hubs de eventos, exclua o **namespace de hubs de eventos de origem**. Certifique-se de que você processou todos os eventos no namespace antes de excluir o namespace. 

Para excluir um namespace de hubs de eventos (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **hubs de eventos**e selecione **hubs de eventos** nos resultados da pesquisa. Você vê os namespaces dos hubs de eventos em uma lista.

2. Selecione o namespace de destino a ser excluído e selecione **excluir** na barra de ferramentas. 

    ![Excluir namespace-botão](./media/move-across-regions/delete-namespace-button.png)

3. Na página **excluir recursos***, verifique os recursos selecionados e confirme a exclusão digitando **Sim**e, em seguida, selecione **excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um namespace de hubs de eventos do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
