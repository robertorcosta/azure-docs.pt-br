---
title: Configurar o gerenciamento de leads do Microsoft Commercial Marketplace com uma tabela do Azure
description: Saiba como usar uma tabela do Azure para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 9814b03e348fc807c04364afbf027369f917670a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131138"
---
# <a name="configure-lead-management-by-using-an-azure-table"></a>Configurar o gerenciamento de leads usando uma tabela do Azure

Se o sistema CRM (gerenciamento de relacionamento com o cliente) não tiver suporte explícito no Partner Center para receber Microsoft AppSource e leads do Azure Marketplace, você poderá usar uma tabela do Azure para lidar com esses leads. Você pode optar por exportar os dados e importá-los para o sistema CRM. As instruções neste artigo orientam você pelo processo de criação de uma conta de armazenamento do Azure e de uma tabela do Azure nessa conta. Além disso, você pode criar um novo fluxo usando o Power Automate para enviar uma notificação por email quando sua oferta receber um cliente potencial.

## <a name="configure-an-azure-table"></a>Configurar uma tabela do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Depois que sua conta do Azure estiver ativa, entre no [portal do Azure](https://portal.azure.com).
1. No portal do Azure, crie uma conta de armazenamento usando o seguinte procedimento:

    1. Selecione **+ criar um recurso** na barra de menus à esquerda. O **novo** painel aparece à direita.
    1. Selecione **armazenamento** no painel **novo** . Uma lista em **destaque** é exibida à direita.
    1. Selecione a **conta de armazenamento** para iniciar a criação da conta. Siga as instruções em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Para obter mais informações sobre contas de armazenamento, consulte o [tutorial de início rápido](https://docs.microsoft.com/azure/storage/). Para obter mais informações sobre preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

1. Aguarde até que sua conta de armazenamento seja provisionada. Esse processo normalmente leva alguns minutos. Em seguida, acesse sua conta de armazenamento na **Home** page do portal do Azure selecionando **Ver todos os seus recursos**. Você também pode selecionar **todos os recursos** na barra de menus à esquerda do portal do Azure.

    ![Acessar sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. No painel da sua conta de armazenamento, selecione **chaves de acesso** e copie o valor da **cadeia de conexão** para a chave. Salve esse valor porque é o valor da **cadeia de conexão da conta de armazenamento** que você precisará fornecer no portal de publicação para receber clientes potenciais para sua oferta do Azure Marketplace. 

    Aqui está um exemplo de uma cadeia de conexão.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. No painel da sua conta de armazenamento, selecione **tabelas**e selecione **+ tabela** para criar uma tabela. Insira um nome para a tabela e selecione **OK**. Salve esse valor porque você precisará dele se quiser configurar um fluxo para receber notificações por email quando os clientes potenciais forem recebidos.

    ![Tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Você pode usar [Gerenciador de armazenamento do Azure](https://archive.codeplex.com/?p=azurestorageexplorer) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na tabela do Azure. 

## <a name="optional-use-power-automate-with-an-azure-table"></a>Adicional Usar a automatização de energia com uma tabela do Azure 

Você pode usar a [automatização de energia](https://docs.microsoft.com/flow/) para automatizar as notificações sempre que um cliente potencial for adicionado a uma tabela do Azure. Se você não tiver uma conta, poderá se [inscrever para obter uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

Use este exemplo como um guia para criar um fluxo simples que automaticamente envia uma notificação por email quando um novo cliente potencial for adicionado a uma tabela do Azure. Este exemplo configura uma recorrência para enviar informações do Lead a cada hora se o armazenamento de tabela for atualizado.

1. Entre em sua conta de energia automatizada.
1. Na barra à esquerda, selecione **meus fluxos**.
1. Na barra superior, selecione **+ novo**. 
1. Na lista suspensa, selecione **+ agendado--em branco**.

   ![Meus fluxos + agendados--de em branco](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Na janela **criar um fluxo agendado** , para **repetir a cada**, selecione **1** para o intervalo e a **hora** da frequência. Além disso, dê um nome ao fluxo, se desejar. Selecione **Criar**.

   >[!NOTE]
   >Embora este exemplo use um intervalo de uma hora, você pode selecionar o intervalo e a frequência que é melhor para suas necessidades de negócios.

   ![Criar um fluxo agendado](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selecione **+ Nova Etapa**.
1. Na janela **escolher uma ação** , procure obter mais **tempo**. Em seguida, em **ações**, selecione **obter última vez**.

   ![Escolher uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Na janela **obter tempo anterior** , defina **intervalo** como **1**. Na lista suspensa **unidade de tempo** , selecione **hora**.

    >[!IMPORTANT]
    >Verifique se o intervalo e a unidade de tempo que você enviou na etapa 8 correspondem ao intervalo e à frequência que você configurou para recorrência na etapa 5.

    ![Definir intervalo de obtenção de tempo anterior](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente. Para verificar o fluxo, selecione **Verificador de fluxo** na barra de menus do **fluxo** .

   No próximo conjunto de etapas, você se conectará à tabela do Azure e configurará a lógica de processamento para lidar com novos clientes potenciais.

1. Após a etapa 8, selecione **+ nova etapa**. Em seguida, procure **obter entidades** na janela **escolher uma ação** .
1. Em **ações**, selecione **obter entidades (armazenamento de tabelas do Azure)**.
1. Na janela **armazenamento de tabelas do Azure** , forneça informações para as seguintes caixas e selecione **criar**:

    * **Nome da conexão**: forneça um nome significativo para a conexão que você está estabelecendo entre esse fluxo e a tabela do Azure.
    * **Nome da conta de armazenamento**: forneça o nome da conta de armazenamento para sua tabela do Azure. Você pode encontrar esse nome na página **chaves de acesso** da conta de armazenamento.
    * **Chave de armazenamento compartilhado**: forneça o valor de chave para sua conta de armazenamento para sua tabela do Azure. Você pode encontrar esse valor na página **chaves de acesso** da conta de armazenamento.

      ![Janela de armazenamento de tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Depois de selecionar **criar**, a janela **obter entidades** é exibida. Aqui, selecione **Mostrar opções avançadas**e forneça informações para as seguintes caixas:

   * **Tabela**: selecione o nome do armazenamento de tabelas do Azure (da etapa 6 das instruções na seção "configurar uma tabela do Azure"). A imagem a seguir mostra o prompt quando a tabela "marketplaceleads" é selecionada para este exemplo.

     ![Janela obter entidades](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Consulta de filtro**: Selecione esta caixa e cole essa função na caixa:`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Caixa de consulta obter entidades, filtrar](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Agora que você concluiu a configuração da conexão com a tabela do Azure, selecione **nova etapa** para adicionar uma condição para verificar a tabela do Azure em busca de novos clientes potenciais.

1. Na janela **escolher uma ação** , selecione **ações**. Em seguida, selecione **controle de condição**.

    ![Escolha uma janela de ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Na janela **condição** , selecione **escolher um valor**. Em seguida, selecione **expressão** na janela pop-up.

1. Cole `length(body('Get_entities')?['value'])` na caixa **FX** . Selecione **OK** para adicionar essa função. 

1. Ao terminar de configurar a condição:
    1. Select **é maior que** a partir da lista suspensa.
    2. Insira **0** como o valor.

        ![Janela de condição](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nas próximas etapas, você configura a ação a ser tomada com base no resultado da condição:

   * Se a condição for resolvida como **se não**, não faça nada.
   * Se a condição for resolvida para **Em caso afirmativo**, dispare uma ação que conecta sua conta do Office 365 para enviar um email. 

1. Selecione **Adicionar uma ação** em **se sim**.

    ![Janela de condição, se Sim, adicionar uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selecione **enviar um email (Outlook do Office 365)**.

    ![Janela de condição, se Sim, enviar um email](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Para usar um provedor de email diferente, procure e selecione **Enviar uma notificação por email (email)** como a ação em vez disso. As instruções mostram como configurar o usando o Outlook do Office 365, mas as instruções são semelhantes para um provedor de email diferente.

1. Na janela do Outlook do Office 365, forneça informações para as seguintes caixas:

    1. **Para**: Insira um endereço de email para todos que receberão essa notificação.
    1. **Assunto**: forneça um assunto para o email. Um exemplo é um **novo leads!**
    1. **Corpo**: Adicione o texto que você deseja incluir em cada email (opcional) e, em seguida, Cole `body('Get_entities')?['value']`-o.

    >[!NOTE]
    >Você pode inserir os pontos de dados estáticos ou dinâmicos adicionais para o corpo deste email.

    ![Janela de condição, se Sim, janela do Outlook do Office 365](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selecione **Salvar** para salvar o fluxo. A automatização de energia testa automaticamente o fluxo em busca de erros. Se não houver nenhum erro, seu fluxo começará a ser executado após ser salvo.

A imagem a seguir mostra um exemplo de como o fluxo final deve ser examinado.

![Um exemplo do fluxo final](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerenciar seu fluxo

O gerenciamento do fluxo após sua execução é fácil. Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A imagem a seguir mostra as opções disponíveis para gerenciar um fluxo.

 ![Gerenciar um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua em execução até você pará-lo usando a opção **Desativar fluxo**.

Se você não estiver obtendo notificações por email de Lead, isso significa que novos clientes potenciais não foram adicionados à tabela do Azure. Se houver alguma falha de fluxo, você receberá um email como este exemplo.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurar sua oferta para enviar clientes potenciais para a tabela do Azure

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga estas etapas.

1. Vá para a página de **instalação da oferta** de sua oferta.
1. Selecione **conectar** na seção **Gerenciamento de leads** .
1. Na janela pop-up **detalhes da conexão** , selecione **tabela do Azure** para o **destino do cliente potencial**. Cole a cadeia de conexão da conta de armazenamento do Azure criada seguindo as etapas anteriores na caixa **cadeia de conexão da conta de armazenamento** .
1. **Email de contato**: forneça emails para pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode fornecer vários emails separando-os com pontos-e-vírgulas.
1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **validar** . Se for bem-sucedido, você terá um líder de teste no destino do cliente potencial.

>[!NOTE]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft envia os clientes potenciais para a tabela do Azure. Se você configurou um fluxo, um email também será enviado para o endereço de email que você configurou.

![Gerenciamento de clientes potenciais](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gerenciamento de leads, detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gerenciamento de leads, conta de armazenamento de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

