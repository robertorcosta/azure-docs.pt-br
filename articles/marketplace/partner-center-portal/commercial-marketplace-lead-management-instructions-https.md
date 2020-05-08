---
title: Gerenciamento de leads com um ponto de extremidade HTTPS-Microsoft Commercial Marketplace
description: Saiba como usar a automatização de energia e um ponto de extremidade HTTPS para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7a4fc57b3be8dd59997ef2bfc9624892cf726160
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790976"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Usar um ponto de extremidade HTTPS para gerenciar clientes potenciais do Marketplace comercial

Se o seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito no Partner Center para receber Microsoft AppSource e leads do Azure Marketplace, você poderá usar um ponto de extremidade HTTPS na [automatização de energia](https://powerapps.microsoft.com/automate-processes/) para lidar com esses leads. Com um ponto de extremidade HTTPS, os clientes potenciais do Marketplace comercial podem ser enviados como uma notificação por email ou podem ser gravados em um sistema de CRM com suporte para a automatização de energia.

Este artigo explica como criar um novo fluxo no Power Automate para gerar a URL HTTP POST que você usará para configurar clientes potenciais no Partner Center. Ele também inclui etapas para testar seu fluxo com o [postmaster](https://www.getpostman.com/downloads/).

>[!NOTE]
>O conector de automatização de energia usado nestas instruções requer uma assinatura paga para automatizar a energia. Certifique-se de considerar isso antes de configurar esse fluxo.

## <a name="create-a-flow-by-using-power-automate"></a>Criar um fluxo usando o Power Automate

1. Abra a página da Web [Power Automate](https://flow.microsoft.com/) . Escolha **Entrar**. Se você ainda não tiver uma conta, selecione **inscrever-se gratuitamente** para criar uma conta gratuita de energia automatizada.

1. Entre e selecione **meus fluxos** no menu.

    ![Entrar em meus fluxos](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Em **+ novo**, selecione **+ instantâneo – de em branco**.

    ![Meus fluxos + automatizados--de em branco](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie o fluxo e, em **escolha como disparar esse fluxo**, selecione **quando uma solicitação HTTP é recebida**.

    ![Criar uma janela de fluxo automatizada botão ignorar](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique na etapa de fluxo para expandi-la.

    ![Expandir a etapa de fluxo](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Use um dos seguintes métodos para configurar o **esquema JSON do corpo da solicitação**:

    - Copie o esquema JSON na caixa de texto **esquema JSON do corpo da solicitação** .
    - Selecione **Use o conteúdo de amostra para gerar o esquema**. Na caixa de texto **Inserir ou colar um exemplo de carga JSON** , Cole o exemplo de JSON. Selecione **Concluído** para criar o esquema.

    **JSON schema**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **Exemplo de JSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>Neste ponto da configuração, você pode optar por se conectar a um sistema CRM ou configurar uma notificação por email. Siga as instruções restantes com base em sua escolha.

### <a name="connect-to-a-crm-system"></a>Conectar-se a um sistema CRM

1. Selecione **+ Nova Etapa**.
1. Escolha o sistema CRM de sua escolha pesquisando-o onde ele diz **Pesquisar conectores e ações**. Selecione-o na guia **ações** com a ação para criar um novo registro. A tela a seguir mostra **criar um novo registro (Dynamics 365)** como um exemplo.

    ![Criar um novo registro](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Forneça o **nome da organização** associada ao sistema CRM. Selecione **clientes potenciais** na lista suspensa **nome da entidade** .

    ![Selecionar clientes potenciais](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. A automatização de energia mostra um formulário para fornecer informações de Lead. É possível mapear itens da solicitação de entrada, escolhendo adicionar conteúdo dinâmico. A tela a seguir mostra **OfferTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mapeie os campos desejados e, em seguida, selecione **salvar** para salvar o fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela **quando uma solicitação HTTP é recebida** . Copie essa URL usando o controle de cópia, que está localizado à direita da URL HTTP POST. O uso do controle de cópia é importante para que você não perca nenhuma parte da URL inteira. Salve esta URL porque você precisará dela quando configurar o gerenciamento de Lead no portal de publicação.

    ![Quando uma solicitação HTTP é recebida](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurar notificação por email

1. Agora que você concluiu o esquema JSON, selecione **+ nova etapa**.
1. Em **Escolha uma ação**, selecione **Ações**.
1. Na guia **ações** , selecione **enviar um email (Outlook do Office 365)**.

    >[!NOTE]
    >Se você quiser usar um provedor de email diferente, procure e selecione **Enviar uma notificação por email (email)** como a ação.

    ![Adicionar uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Na janela **enviar um email** , configure os seguintes campos obrigatórios:

   - **Para**: Insira pelo menos um endereço de email válido para o qual os clientes potenciais serão enviados.
   - **Assunto**: a energia automatizada oferece a opção de adicionar conteúdo dinâmico, como o **leadname** mostrado na tela a seguir. Comece inserindo um nome de campo. Em seguida, selecione a lista de seleção de conteúdo dinâmico na janela pop-up. 

        >[!NOTE] 
        > Ao adicionar nomes de campo, você pode seguir cada nome com dois-pontos (:) e, em seguida, selecione **Enter** para criar uma nova linha. Depois que os nomes de campo forem adicionados, você poderá adicionar cada parâmetro associado da lista de seleção dinâmica.

        ![Adicionar uma ação de email usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: na lista de seleção de conteúdo dinâmico, adicione as informações desejadas no corpo do email. Por exemplo, use LastName, FirstName, email e Company. Quando você terminar de configurar a notificação por email, ele será semelhante ao exemplo na tela a seguir.


       ![Exemplo de notificação por email](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selecione **Salvar** para concluir seu fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela **quando uma solicitação HTTP é recebida** . Copie essa URL usando o controle de cópia, que está localizado à direita da URL HTTP POST. O uso desse controle é importante para que você não perca nenhuma parte da URL inteira. Salve esta URL porque você precisará dela quando configurar o gerenciamento de Lead no portal de publicação.

   ![URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testando

Você pode testar sua configuração com o [postmaster](https://app.getpostman.com/app/download/win64). Um download online do postmaster está disponível para o Windows. 

1. Inicie o postmaster e selecione **nova** > **solicitação** para configurar sua ferramenta de teste. 

   ![Solicitação para configurar sua ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Preencha o formulário de **solicitação de salvamento** e, em seguida, salve na pasta que você criou.

   ![Salvar formulário de solicitação](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selecione **postar** na lista suspensa. 

   ![Testar meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Cole a URL HTTP POST do fluxo que você criou no Power Automate onde ele diz **Inserir URL de solicitação**.

   ![Cole a URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Volte para a [automatização de energia](https://flow.microsoft.com/). Localize o fluxo que você criou para enviar leads acessando **meus fluxos** da barra de menus do Power mate. Selecione as reticências ao lado do nome do fluxo para ver mais opções e selecione **Editar**.


1. Selecione **teste** no canto superior direito, selecione **executarei a ação do gatilho**e, em seguida, selecione **testar**. Você verá uma indicação na parte superior da tela que o teste iniciou.

   ![Executarei a opção de ação do gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Volte para o seu aplicativo de postmaster e selecione **Enviar**.

   ![Botão enviar](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Volte para o fluxo e verifique o resultado. Se tudo funcionar conforme o esperado, você verá uma mensagem que indica que o fluxo foi bem-sucedido.

   ![Verificar os resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Você também deve ter recebido um email. Verifique sua caixa de entrada de email. 

    >[!NOTE] 
    >Se você não vir um email do teste, verifique suas pastas de spam e lixo eletrônico. Na tela a seguir, você observará apenas os rótulos de campo que adicionou quando configurou a notificação por email. Se esse for um cliente potencial real gerado por meio de sua oferta, você também verá as informações reais do contato do cliente potencial no corpo e na linha de assunto.

   ![Email recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga estas etapas.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá para a guia **instalação da oferta** .

1. Na seção **Gerenciamento de leads** , selecione **conectar**. 
    ![Botão conectar do Lead Management](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

1. Na janela pop-up **detalhes da conexão** , selecione **ponto de extremidade https** para o **destino do cliente potencial**. Cole a URL HTTP POST do fluxo que você criou seguindo as etapas anteriores no campo **URL do ponto de extremidade https** .
    ![Email de contato de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Em **email de contato**, insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode fornecer vários emails separando-os com um ponto e vírgula.

1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **validar** . Se for bem-sucedido, você terá um líder de teste no destino do cliente potencial.

>[!NOTE] 
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft envia os leads para o fluxo. Os clientes potenciais são roteados para o sistema de CRM ou o endereço de email que você configurou.
