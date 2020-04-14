---
title: Mesa Azure | Mercado Azure
description: Configure o gerenciamento de clientes potenciais para a tabela do Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a36c411b9ababc42adb51d82a316df4252c01e24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251905"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configure o gerenciamento de chumbo usando uma tabela Azure

Se o sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) não for explicitamente suportado no Partner Center para receber leads Azure Marketplace e AppSource, você pode usar uma Tabela Azure para lidar com esses leads. Em seguida, você pode optar por exportar os dados e importá-los para o seu sistema de CRM. As instruções deste artigo irão levá-lo ao processo de criação de uma conta do Azure Storage e de uma Tabela Azure sob essa conta. Além disso, você pode criar um novo fluxo usando o Microsoft Flow para enviar uma notificação por e-mail quando sua oferta recebe um lead.

## <a name="configure-azure-table"></a>Configurar a tabela Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
2. Depois que sua conta do Azure estiver ativa, faça login no [portal Azure](https://portal.azure.com).
3. No portal Azure, crie uma conta de armazenamento usando o procedimento a seguir.  
    1. Selecione **+Criar um recurso** na barra de menuà esquerda.  O **painel Novo** (lâmina) será exibido à direita.
    2. Selecione **Armazenamento** no **painel Novo.**  Uma **lista em destaque** é exibida à direita.
    3. Selecione **A Conta de Armazenamento** para iniciar a criação da conta.  Siga as instruções do artigo [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Para obter mais informações sobre contas de armazenamento, selecione [o tutorial Quickstart](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que sua conta de armazenamento seja provisionada, um processo que normalmente leva alguns minutos.  Em seguida, acesse sua conta de armazenamento a partir da página **inicial** do portal Azure selecionando **Ver todos os seus recursos** ou selecionando Todos os **recursos** da barra de menu de navegação esquerda do portal Azure.

    ![Acesse sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. No painel da conta de armazenamento, selecione **as teclas de acesso** e copie o valor da *seqüência de string Conexão* para a chave. Salve esse valor, pois este é o valor da *string de conexão de conta de armazenamento* que você precisará fornecer no portal de publicação para receber leads para sua oferta de marketplace. 

    Um exemplo de picada de conexão é:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. No painel da sua conta de armazenamento, selecione **Tabelas** e selecione **+Tabela** para criar uma tabela. Digite um nome para sua tabela e selecione **OK**. Salve esse valor como você precisará se quiser configurar um Fluxo MS para receber notificações de e-mail quando os leads forem recebidos.

    ![Tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Você pode usar o [gerenciador de armazenamento do Azure](https://archive.codeplex.com/?p=azurestorageexplorer) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na Tabela Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Opcional) Use o Microsoft Flow com uma tabela Azure  

Você pode usar o [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar as notificações sempre que um cliente potencial for adicionado à tabela do Azure. Se você não tem uma conta, você pode [se inscrever em uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

Use este exemplo como um guia para criar um fluxo simples que automaticamente envia uma notificação por email quando um novo cliente potencial for adicionado a uma tabela do Azure. Este exemplo configura uma recorrência para enviar informações de chumbo a cada hora se o armazenamento da tabela for atualizado.

1. Faça login na sua conta do Microsoft Flow.
2. Na barra de navegação à esquerda, selecione **Meus Fluxos**.
3. Na barra de navegação à esquerda, selecione **+ Novo**.  
4. Na lista de isto, selecione **+ Programado - em branco**

   ![Meus fluxos **+ Programados - de branco**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.    Na *Compilação uma* janela de fluxo programada em *Repetir cada* "1" selecionado para intervalo e "hora" para freqüência. Além disso, dê um nome ao fluxo, se quiser. Selecione **Criar**.

>[!Note]
>Embora este exemplo use um intervalo de 1 hora, você pode selecionar o intervalo e a frequência que é melhor para as necessidades do seu negócio.

![Construa um fluxo programado.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selecione **+ Nova Etapa**.
7. Na *escolha de uma* janela de ação, procure "passar o tempo" e selecione **'Passar tempo'** em Ações.

   ![Escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Na janela **Obter horário passado**, defina **Intervalo** como 1. Na lista suspensa **Unidade de tempo**, selecione **Hora**.

    >[!Important]
    >Certifique-se de que esta unidade de intervalo e hora corresponda ao intervalo e frequência configurado para Recorrência na etapa 5.

    ![Definir intervalo de tempo passado](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente. Para verificar seu fluxo, selecione **Verificador de Fluxo** na barra de menus do Fluxo.

Nos próximos passos, você se conectará à sua tabela Azure e configurará a lógica de processamento para lidar com novos leads.

9. Após a etapa de passar do tempo, selecione **+ Novo passo**e procure por "Obter entidades" na janela Escolha *uma* ação.
10. Em **Ações,** **selecione Obter entidades (Armazenamento de tabela do Azure)**.
11.    Na janela **Armazenamento de tabela do Azure,** forneça informações para os seguintes campos e selecione **Criar**:
* *Nome de conexão* - forneça um nome significativo para a conexão que você está estabelecendo entre esse fluxo e a Tabela Azure.
* *Nome da conta* de armazenamento - forneça o nome da conta de armazenamento da sua tabela Azure. Você pode encontrar isso na página de chaves de **acesso** da conta de armazenamento.
* *Chave de armazenamento compartilhado* - forneça o valor-chave para a conta da sua loja para a sua tabela Azure. Você pode encontrar isso na página de chaves de **acesso** da conta de armazenamento.
    ![Armazenamento de mesa azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

Depois de clicar em Criar você verá uma janela *Obter entidades.* Aqui selecione **Mostrar opções avançadas** e fornecer informações para os seguintes campos:
* *Tabela* - Selecione o nome do seu Armazenamento de Tabela azure (a partir da etapa 6 de instruções sobre como configurar uma tabela Azure). A próxima captura de tela mostra o prompt quando a tabela "marketplaceleads" é selecionada para este exemplo.
    ![Azure Table obter entidades.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

* *Consulta ao filtro* - Selecione este campo e `Timestamp gt datetime'@{body('Get_past_time')}'` ![cole esta função no campo: Azure Table obter entidades - Consulta de filtro.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Agora que você completou a configuração da conexão com a tabela Azure, selecione **Nova etapa** para adicionar uma condição para digitalizar a tabela Azure para novos leads. 

13. Na **janela Escolher uma janela de ação,** selecione **''''''** **Condition control**

    ![Tabela Azure - Escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Na janela **Condição**, selecione o campo **Escolher um valor** e, em seguida, selecione **Expressão** na janela pop-up.

15. Cole `length(body('Get_entities')?['value'])` no campo ***fx***. Selecione **OK** para adicionar essa função. 

16. Ao terminar de configurar a condição:
    1. Selecione "é maior que" na lista de desíficadas.
    2. Digite 0 como o valor

        ![Mesa Azure - Condição.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Nos próximos passos você definirá a ação a ser tomada com base no resultado da condição.

* Se a condição se resolver para **Se não,** não faça nada.
* Se a condição for resolvida para **Em caso afirmativo**, dispare uma ação que conecta sua conta do Office 365 para enviar um email. 

17. Selecione **Adicionar uma ação** em Se **sim**.

    ![Tabela Azure - Condição, **Se sim**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selecione **Enviar um e-mail (Office 365 Outlook)**.

    ![Tabela Azure - Condição, **Se sim**, envie e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Se você quiser usar uma pesquisa diferente do provedor de e-mail e selecionar Enviar uma notificação de e-mail (Mail) como a ação em vez disso. As instruções mostrarão como configurar usando o Office 365 Outlook, mas as instruções são semelhantes para um provedor de e-mail diferente.

19. Na janela **Office 365 Outlook,** forneça informações para os seguintes campos:

    1. **Para** -Insira um endereço de email para todos que obterão essa notificação.
    1. **Assunto** - Fornecer um assunto para o e-mail. Por exemplo: Novas pistas!
    1. **Corpo** - Adicione o texto que deseja incluir em cada e-mail (opcional) e, em seguida, cole no corpo `body('Get_entities')?['value']`.

    >[!Note]
    >Você pode inserir os pontos de dados estáticos ou dinâmicos adicionais para o corpo deste email.

    ![Tabela Azure - Condição, **Se sim**, janela do Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selecione **Salvar** para salvar o fluxo. O Microsoft Flow automaticamente irá testar o fluxo de erros. Se não houver erros, seu fluxo começa a ser executado depois de salvo.

A próxima captura de tela mostra um exemplo de como o fluxo final deve parecer.

![Um exemplo do fluxo final.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerencie seu fluxo

Gerenciar seu fluxo depois de executado é fácil. Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A próxima captura de tela mostra as opções que estão disponíveis para gerenciar um fluxo. 

 ![Gerenciar um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua em execução até você pará-lo usando a opção **Desativar fluxo**.

Se você não está recebendo nenhuma notificação de e-mail principal, isso significa que novos leads não foram adicionados à tabela Azure. Se houver alguma falha de fluxo, você receberá um e-mail como o exemplo na próxima captura de tela.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configure sua oferta para enviar leads para a Tabela Azure

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga os passos abaixo:

1. Navegue até a página **de configuração oferta** para obter sua oferta.
2. Selecione **Conectar** na seção Gerenciamento de líderes.
3. Na janela pop-up de detalhes da Conexão, selecione **Azure Table** for the **Lead Destination**e cole na seqüência de conexões da conta de armazenamento Azure criada seguindo etapas anteriores no campo string **de conexão de conta de armazenamento.**
4. **E-mail de contato** - Forneça e-mails para pessoas da sua empresa que devem receber notificações por e-mail quando um novo lead é recebido. Você pode fornecer vários e-mails separando-os com ponto e vírgula.
5. Selecione **Ok**.

Para ter certeza de que você se conectou com sucesso a um destino de chumbo, clique no botão validar. Se for bem sucedido, você terá uma pista de teste no destino principal.

>[!Note]
>Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta.

Quando os leads são gerados, a Microsoft envia leads para a Tabela Azure. Se você configurou um fluxo, um e-mail também será enviado para o endereço de e-mail que você configurou.

![Gerenciamento de clientes potenciais](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gerenciamento de lead - detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gerenciamento de chumbo - conta de armazenamento de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

