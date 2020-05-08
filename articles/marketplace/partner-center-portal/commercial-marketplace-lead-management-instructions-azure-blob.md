---
title: Gerenciamento de leads com o armazenamento de BLOBs do Azure-Microsoft Commercial Marketplace
description: Saiba como usar o blob do Azure para configurar clientes potenciais para o Microsoft AppSource e o Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 076edc62a467701eaf0de23f280cdaf2abd945de
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792710"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Usar o armazenamento de BLOBs do Azure para gerenciar clientes potenciais do Marketplace comercial

>[!Caution]
>O suporte do Marketplace comercial para o armazenamento de BLOBs do Azure foi preterido e não é mais uma opção para processar os leads de sua oferta. Se, no momento, você tiver uma oferta de Marketplace comercial com gerenciamento de Lead configurado para o blob do Azure, você não receberá mais clientes potenciais do cliente. Atualize sua configuração de gerenciamento de leads para qualquer uma das outras opções de gerenciamento de Lead. Saiba mais sobre as outras opções na [página inicial de gerenciamento de leads](./commercial-marketplace-get-customer-leads.md).

 Se o seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito no Partner Center para receber Microsoft AppSource e leads do Azure Marketplace, você poderá usar o armazenamento de BLOBs do Azure. Você pode optar por exportar os dados e importá-los para o sistema CRM. As instruções neste artigo fornecerão o processo de criação de uma conta de armazenamento do Azure e um blob sob essa conta. Além disso, você pode criar um novo fluxo usando o Power Automate para enviar uma notificação por email quando sua oferta receber um cliente potencial.

>[!NOTE]
>O conector de automatização de energia usado nestas instruções requer uma assinatura paga para automatizar a energia. Certifique-se de considerar isso antes de seguir as instruções neste artigo.

## <a name="configure-azure-blob-storage"></a>Configurar o armazenamento de BLOBs do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2. Depois que sua conta do Azure estiver ativa, entre no [portal do Azure](https://portal.azure.com).

3. No portal do Azure, crie uma conta de armazenamento usando o procedimento a seguir.  
    1. Selecione **+ criar um recurso** na barra de menus à esquerda.  O **novo** painel (folha) será exibido à direita.
    2. Selecione **armazenamento** no painel **novo** .  Uma lista em **destaque** é exibida à direita.
    3. Selecione a **conta de armazenamento** para iniciar a criação da conta.  Siga as instruções no artigo [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para obter mais informações sobre contas de armazenamento, selecione [tutorial de início rápido](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que sua conta de armazenamento seja provisionada, um processo que normalmente leva alguns minutos.  Em seguida, acesse sua conta de armazenamento na **Home** page do portal do Azure selecionando **Ver todos os seus recursos** ou selecionando **todos os recursos** na menu de navegação à esquerda da portal do Azure.

    ![Acessar sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. No painel da sua conta de armazenamento, selecione **chaves de acesso** e copie o valor da *cadeia de conexão* para a chave. Salve esse valor, pois esse é o valor da *cadeia de conexão da conta de armazenamento* que você precisará fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

     Um exemplo de um Stinger de conexão é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na página da sua conta de armazenamento, selecione **BLOBs**.

   ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página BLOBs, selecione o botão **+ contêiner** .

8. Digite um **nome** para o novo contêiner. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-). Para obter mais informações sobre nomes de contêiner e de blobs, confira [Nomeando e referenciando contêineres, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Salve esse valor, pois esse é o valor do *nome do contêiner* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

9. Defina o nível de acesso público para o contêiner como **privado (sem acesso anônimo)**.

10. Selecione **OK** para criar o contêiner.

    ![Novo contêiner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Configurar sua oferta para enviar clientes potenciais ao armazenamento de BLOBs do Azure

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
2. Selecione **conectar** na seção Gerenciamento de leads.

    ![Conectar oferta](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Na janela pop-up detalhes da conexão, selecione **blob do Azure** para o destino do cliente potencial.

    ![Detalhes do Connect](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Forneça o **nome do contêiner** e a **cadeia de conexão da conta de armazenamento** obtida seguindo estas instruções.

    * Exemplo de nome do contêiner:`marketplaceleadcontainer`
    * Exemplo de cadeia de conexão da `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![conta de armazenamento: detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Clique em **Salvar**.

    > [!NOTE]
    > Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.


