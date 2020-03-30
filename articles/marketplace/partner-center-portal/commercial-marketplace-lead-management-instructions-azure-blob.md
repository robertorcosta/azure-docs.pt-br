---
title: Tabela Azure em programa de mercado comercial| Mercado Azure
description: Configure o gerenciamento de chumbo para o Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285241"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instruções de gerenciamento de chumbo para Azure Blob

>[!Caution]
>A opção Azure Blob para processar leads da sua oferta de marketplace foi depreciada. Se você tiver atualmente uma oferta publicada com a configuração de gerenciamento de leads para o Azure Blob, você não está mais recebendo leads de clientes. Atualize sua configuração de gerenciamento de chumbo para qualquer uma das outras opções de gerenciamento de chumbo. Conheça as outras opções na [landing page de gerenciamento de líderes](./commercial-marketplace-get-customer-leads.md)."

Se o sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) não for explicitamente suportado no Partner Center para receber leads Azure Marketplace e AppSource, você pode usar um Azure Blob para lidar com esses leads. Em seguida, você pode optar por exportar os dados e importá-los para o seu sistema de CRM. As instruções deste artigo lhe darão o processo de criação de uma conta do Azure Storage e um Azure Blob sob essa conta. Além disso, você pode criar um novo fluxo usando o Microsoft Flow para enviar uma notificação por e-mail quando sua oferta recebe um lead.


## <a name="how-to-configure-azure-blob"></a>Como configurar o Azure Blob

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Depois que sua conta do Azure estiver ativa, faça login no [portal Azure](https://portal.azure.com).
1. No portal Azure, crie uma conta de armazenamento usando o procedimento a seguir.  
    1. Selecione **+Criar um recurso** na barra de menuà esquerda.  O **painel Novo** (lâmina) será exibido à direita.
    2. Selecione **Armazenamento** no **painel Novo.**  Uma **lista em destaque** é exibida à direita.
    3. Selecione a **Conta de armazenamento** para iniciar a criação da conta.  Siga as instruções do artigo [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para obter mais informações sobre contas de armazenamento, selecione [o tutorial Quickstart](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que sua conta de armazenamento seja provisionada, um processo que normalmente leva alguns minutos.  Em seguida, acesse sua conta de armazenamento a partir da página **inicial** do portal Azure selecionando **Ver todos os seus recursos** ou selecionando Todos os **recursos** da barra de menude navegação esquerda do portal Azure.

    ![Acesse sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. No painel da conta de armazenamento, selecione **as teclas de acesso** e copie o valor da *seqüência de string Conexão* para a chave. Salve esse valor, pois este é o valor da *string de conexão de conta de armazenamento* que você precisará fornecer no portal de publicação para receber leads para sua oferta de marketplace.

     Um exemplo de picada de conexão é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na página da sua conta de armazenamento, selecione **Blobs**.

   ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página blobs, selecione o botão **+ Contêiner.**

8. Digite um **nome** para o seu novo recipiente. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-). Para obter mais informações sobre nomes de contêineres e bolhas, consulte [Nomes e referências de contêineres, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Guarde esse valor, pois este é o valor *do Container Name* que você precisa fornecer no portal de publicação para receber leads para sua oferta de marketplace.

9. Defina o nível de acesso público ao contêiner como **Privado (sem acesso anônimo)**.

10. Selecione **OK** para criar o contêiner.

    ![Novo container](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configure sua oferta para enviar leads para o Azure Blob

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página **de configuração oferta** para obter sua oferta.
2. Selecione **Conectar** na seção Gerenciamento de líderes.

    ![Conectar oferta](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Na janela pop-up de detalhes do Conexão, selecione **Azure Blob** para o Destino Principal.

    ![Detalhede conectar](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Forneça a **seqüência de conexão de conta de nome** e **armazenamento** do contêiner que você obteve ao seguir estas instruções.

    * Exemplo do nome do contêiner:`marketplaceleadcontainer`
    * Exemplo da seqüência de conexão da conta de armazenamento: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecione **Salvar**.

    > [!NOTE]
    > Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta.


