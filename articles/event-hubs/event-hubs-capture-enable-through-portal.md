---
title: Hubs de eventos - Capture eventos de streaming usando o portal Azure
description: Este artigo descreve como habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure usando o portal do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187356"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure

O Azure [Event Hubs Capture][capture-overview] permite que você entregue automaticamente os dados de streaming em Hubs de Eventos para um [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou a conta [Azure Data Lake Storage Gen1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) de sua escolha.

Você pode configurar Captura na hora da criação do hub de eventos usando o [portal do Azure](https://portal.azure.com). Você pode capturar os dados em um contêiner de armazenamento Azure [Blob](https://azure.microsoft.com/services/storage/blobs/) ou em uma conta [do Azure Data Lake Storage Gen 1 ou Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Para saber mais, confira [Visão geral da captura dos Hubs de Eventos][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Capture dados para o Armazenamento Azure

Quando você cria um hub de eventos, pode habilitar a Captura clicando no botão **Ativado** na tela **Criar Hub de Eventos** do portal. Em seguida, especifique uma Conta de Armazenamento e um contêiner clicando em **Armazenamento do Azure** na caixa **Provedor da Captura**. Já que a Captura dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela de tempo para captura][1]

> [!NOTE]
> Você pode habilitar ou desabilitar a emissão de arquivos vazios quando nenhum evento ocorre durante a janela de Captura. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Capture dados para Azure Data Lake Storage Gen 2 

1. Seguir Criar um artigo [de conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) para criar uma conta do Azure Storage. Defina **o namespace hierárquico** **para Habilitado** na guia **Avançado** para torná-lo uma conta Azure Data Lake Storage Gen 2.
2. Ao criar um hub de eventos, faça as seguintes etapas: 

    1. Selecione **'''** para **capturar**. 
    2. Selecione **o Azure Storage** como provedor de captura. A opção **Azure Data Lake Store** que você vê para o **provedor de captura** é para o Gen 1 do Azure Data Lake Storage. Para usar um Gen 2 do Azure Data Lake Storage, selecione **o Azure Storage**.
    2. Selecione o botão **Selecionar contêiner.** 

        ![Habilitar a captura para data lake storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecione a conta **Azure Data Lake Storage Gen 2** da lista. 

    ![Selecione Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecione o **contêiner** (sistema de arquivos no Data Lake Storage Gen 2).

    ![Selecione o sistema de arquivos no armazenamento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na página **Criar hub de eventos,** selecione **Criar**. 

    ![Selecione o botão Criar](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > O contêiner que você cria em um Azure Data Lake Storage Gen 2 usando esta interface de usuário (UI) é mostrado em **sistemas de arquivo** no Storage **Explorer**. Da mesma forma, o sistema de arquivos que você cria em uma conta Data Lake Storage Gen 2 aparece como um contêiner nesta ui. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Capture dados para Azure Data Lake Storage Gen 1 

Para capturar dados no Azure Data Lake Storage Gen 1, você cria uma conta Data Lake Storage Gen 1 e um hub de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Crie uma conta e pastas do Azure Data Lake Storage Gen 1

1. Crie uma conta de armazenamento no lago de dados, seguindo as instruções em [Iniciar com o Azure Data Lake Storage Gen 1 usando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na [seção Atribuir permissões aos Centros de Eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para criar uma pasta dentro da conta Data Lake Storage Gen 1 na qual você deseja capturar os dados dos Hubs de eventos e atribuir permissões aos Event Hubs para que ele possa gravar dados na sua conta Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. O hub de eventos deve estar na mesma assinatura do Azure que a conta Azure Data Lake Storage Gen 1 que você criou. Crie o hub de eventos clicando no botão **Ativado** em **Capturar** na página **Criar Hub de Eventos** do portal. 
2. Na página **Criar Hub de Eventos** do portal, selecione **Azure Data Lake Store** na caixa **Provedor da Captura**.
3. Em **Select Store** ao lado da lista de paradas do Data Lake **Store,** especifique a conta Data Lake Storage Gen 1 que você criou anteriormente e, no campo Data Lake **Path,** digite o caminho para a pasta de dados que você criou.

    ![Selecionar conta do Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura em um hub de eventos existente

Você pode configurar a Captura em hubs de eventos existentes que estejam em namespaces dos Hubs de Eventos. Para habilitar a Captura em um hub de eventos existente ou alterar as configurações da Captura, clique no namespace para carregar a tela de visão geral e, em seguida, clique no hub de eventos para o qual você deseja habilitar ou alterar a configuração da Captura. Por fim, clique na opção **Captura** do lado esquerdo da página aberta e edite as configurações, conforme exibido nas figuras abaixo:

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

![Configurar Armazenamento de Blobs do Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configurar o Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Configurar o Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a captura de Hubs de Eventos lendo a [visão geral da Captura de Hubs de Eventos][capture-overview].
- Você também pode configurar a Captura dos Hubs de Eventos usando modelos do Azure Resource Manager. Para saber mais, confira [Habilitar Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Saiba como criar uma assinatura da Grade de Eventos do Azure com um namespace de Hubs de Eventos como sua fonte](store-captured-data-data-warehouse.md)
- [Comece com a Loja azure Data Lake usando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
