---
title: Armazenamento de mesa azul | Mercado Azure
description: Configure o gerenciamento de chumbo no armazenamento da tabela Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280347"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instruções de gerenciamento de chumbo para armazenamento de tabelas

Este artigo descreve como configurar o armazenamento da Tabela Azure para gerenciar leads de vendas. O armazenamento de mesa ajuda a armazenar e modificar as informações do cliente.

## <a name="configure-table-storage"></a>Configurar o armazenamento de Tabela

1. Se você não tiver uma conta no Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Depois que sua conta estiver ativa, faça login no [portal Azure](https://portal.azure.com).
1. No portal Azure, siga estas etapas:  
    1. Selecione **+Criar um recurso** no painel do lado esquerdo. O **novo** painel será aberto.
    1. No **painel Novo,** selecione **Armazenamento**. Uma **lista em destaque** será aberta no lado direito.
    1. Selecione **a conta de armazenamento**. Em seguida, siga as instruções em [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Para obter mais informações sobre contas de armazenamento, consulte [tutoriais quickstart](https://docs.microsoft.com/azure/storage/). Para obter informações sobre preços, consulte [os preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Aguarde até que sua conta de armazenamento seja provisionada, o que normalmente leva alguns minutos. Em seguida, acesse a conta da página inicial do portal Azure: Selecione **Ver todos os seus recursos** ou Todos os **recursos** no painel de navegação.

    ![Acesse sua conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. No painel da sua conta de armazenamento, copie a seqüência de conexão da conta de armazenamento para a chave. Cole-o no campo **String de conexão** para a conta de armazenamento no Portal de Parceiros em Nuvem.

    Seqüência de conexão exemplo:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Você pode usar [o Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ou uma ferramenta semelhante para ver os dados no armazenamento da sua tabela. Você também pode exportar dados a partir dele.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Use o Microsoft Flow with Table storage *(opcional)*

Você pode usar [o Microsoft Flow](https://docs.microsoft.com/flow/) para enviar automaticamente notificações quando um lead é adicionado ao armazenamento da tabela. Se você não tiver uma conta no Microsoft Flow, [inscreva-se em uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

Este exemplo mostra como criar um fluxo básico. O fluxo envia automaticamente uma notificação por e-mail de hora em hora quando novos leads são adicionados ao armazenamento da tabela.

1. Entre na sua conta da Microsoft Flow.
1. No painel de navegação do lado esquerdo, selecione **Meus fluxos**.
1. Na barra de navegação superior, selecione **+Novo**.  
1. Na lista de paradas, selecione **+Criar em branco**.
1. Em **Criar um fluxo em branco,** selecione Criar em **branco**.

   ![Criar um novo fluxo do zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na barra de pesquisa de conectores e gatilhos, digite **Gatilhos**.
1. Em **Gatilhos**, selecione **Recorrência**.
1. Na janela **Recorrência,** mantenha a configuração padrão de **1** para **intervalo**. Na lista de isto de **freqüência,** selecione **Hora**.

   >[!NOTE] 
   >Este exemplo usa um intervalo de uma hora. Mas você pode selecionar um intervalo e frequência que melhor se adapte às necessidades do seu negócio.

   ![Defina uma frequência de 1 hora para recorrência](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selecione **+Novo passo**.
1. Procure **Obter tempo passado**e, em seguida, **selecione Obter tempo passado** em Escolher uma **ação**.

    ![Encontre e selecione a ação "passar o tempo"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Na janela **De obter tempo passado,** defina o **intervalo** para **1**.  Na lista de parada da **unidade Tempo,** selecione **Hora**.
    >[!IMPORTANT] 
    >Certifique-se **de** que a unidade Intervalo e **Hora corresponda** ao intervalo e frequência que você configurou para recorrência (etapa 8).

    ![Defina o intervalo de tempo de passar](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente: Selecione **verificador** de fluxo na barra de menu Flow.

No próximo conjunto de etapas, você se conecta à sua tabela de armazenamento e configura a lógica de processamento para lidar com novos leads.

1. Após a etapa **de obter passado o tempo,** selecione **+Novo passo**e, em seguida, procure obter **entidades**.
1. Em **Ações**, selecione **Obter entidades**e, em seguida, selecione **Mostrar opções avançadas**.
1. Na janela **Obter entidades,** preencha os seguintes campos:

   - **Tabela:** o nome do seu armazenamento de mesa. A imagem a seguir mostra "MarketPlaceLeads" inserida:

     ![Escolha um valor personalizado para o nome da tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Consulta ao filtro :** Quando você seleciona este campo, o ícone **'Obter tempo passado'** é exibido em uma janela pop-up. Selecione **Tempo passado** para usar esse valor como carimbo de data e hora para filtrar a consulta. Ou, você pode colar a seguinte função no campo:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Configure a função de consulta do filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selecione **Nova etapa** para adicionar uma condição para digitaleiar o armazenamento da tabela em busca de novos leads.

   ![Use "Novo passo" para adicionar uma condição para escanear o armazenamento da tabela](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Na **janela Escolher uma janela de ação,** selecione **Ações**e selecione **Controle de condições**.

     ![Adicione um controle de condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Na janela **Condição,** **selecione Escolha um valor**e selecione **'Expressão'** na janela pop-up.
1. Cole `length(body('Get_entities')?['value'])` no campo ***fx.*** Selecione **OK** para adicionar essa função. 



     ![Adicione uma função para a condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Configure a ação para executar com base no resultado da condição.

    1. A **seleção é maior do que** na lista de paradas.
   1. Digite **0** como valor.

     ![Configure uma ação com base nos resultados das condições](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Se a condição se resolver para "Se não", não faça nada.

    Se a condição for resolvida com "Se sim", acione uma ação que conecta sua conta do Office 365 para enviar um e-mail:
   1. Selecione **Adicionar uma ação**.
   1. Selecione **Enviar um email**.
   1. Na janela Enviar uma janela **de e-mail,** digite informações nos seguintes campos:

      - **Para**: um endereço de e-mail para todos que receberão a notificação.
      - **Assunto**: assunto para o e-mail. Por exemplo: *Novas pistas!*
      - **Corpo**: o texto que você deseja incluir em cada e-mail (opcional). Cole também `body('Get_entities')?['value']` como uma função para inserir informações de chumbo.

        >[!NOTE] 
        >Você pode inserir pontos de dados estáticas ou dinâmicos adicionais no corpo do e-mail.

      ![Configurar o email de notificação de cliente potencial](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selecione **Salvar** para salvar o fluxo. O Microsoft Flow irá testá-lo automaticamente para erros. Se não houver erros, seu fluxo começa a ser executado depois de salvo.

    A imagem a seguir mostra um exemplo de como o fluxo final deve ser.

    [![Sequência do fluxo final](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Selecione a imagem para ampliá-la.*)

### <a name="manage-your-flow"></a>Gerencie seu fluxo

É fácil gerenciar seu fluxo depois de correr. Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A imagem a seguir mostra as opções de gerenciamento de fluxo.

 ![Opções de gerenciamento de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo continua funcionando até que você **selecione Desligar o fluxo**.

Se você não estiver recebendo nenhuma notificação de e-mail principal, nenhum novo leads foi adicionado ao armazenamento da tabela.
Você receberá um e-mail como o seguinte exemplo se ocorrer uma falha de fluxo:

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Próximas etapas

[Configurar leads de cliente](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
