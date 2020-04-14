---
title: HTTPS Endpoint | Mercado Azure
description: Configurar o gerenciamento de cliente potencial para um ponto de extremidade HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262467"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de cliente potencial usando um ponto de extremidade HTTPS

>[!Note]
>O conector Power Automate usado nestas instruções requer uma assinatura paga do Power Automate. Por favor, contabilize isso antes de seguir as instruções deste documento.

Se o sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) não for explicitamente suportado no Partner Center para receber leads Do Azure Marketplace e AppSource, você pode usar um ponto final HTTPS no Power Automate para lidar com esses leads. Com um ponto final HTTPS, esses leads podem ser enviados como uma notificação por e-mail ou podem ser gravados em um sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) suportado pelo Power Automate. As instruções deste artigo irão guiá-lo através do processo básico para criar um novo fluxo usando o Power Automate, que gerará a URL HTTP POST que você entrará no portal de publicação para o campo de URL do Lead Management > **HTTPS Endpoint.** Além disso, incluem instruções sobre como você pode testar seu fluxo com a ajuda de uma ferramenta chamada [Carteiro](https://www.getpostman.com/downloads/) que pode ser encontrada online.

## <a name="create-a-flow-using-power-automate"></a>Crie um fluxo usando o Power Automate

1. Abra a página da Web do [Flow](https://flow.microsoft.com/). Selecione **Entrar**ou se você ainda não tiver uma conta, **selecione Cadastre-se gratuitamente** para criar uma conta flow gratuita.

2. Inscreva-se e selecione **Meus fluxos** na barra de menus.

3. Selecione **+Automatizado - em branco**.

    ![Meus fluxos + Automatizado - de branco](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Na Construção de uma janela *de fluxo automatizada,* **selecione 'Ignorar'.** 

    ![Construir fluxo automatizado - Pular](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. No campo **Pesquisar conectores e gatilhos**, digite "solicitar" para localizar o conector de Solicitação.
6. Em *Gatilhos*, selecione **Quando uma solicitação HTTP é recebida**. 

    ![conector de solicitação - Gatilhos](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Na *solicitação HTTP é recebida,* copie a janela e cole o esquema JSON abaixo na caixa de texto **Request Body JSON Schema.** Este esquema é usado pela Microsoft para conter seus dados de chumbo.

    ![conector de solicitação - Gatilhos](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!Note]
>Neste ponto da configuração, você pode selecionar para se conectar a um sistema de CRM ou configurar uma notificação de e-mail. Siga as instruções restantes com base na sua escolha.

### <a name="to-connect-to-a-crm-system"></a>Para se conectar a um sistema de CRM

1. Selecione **+ Nova Etapa**.
2. Escolha o sistema de CRM de sua escolha procurando-o onde diz *Conectores e ações de Pesquisa*e selecione-o na seção *Ações* com a ação para criar um novo registro. A captura de tela a seguir mostra **dynamics 365 - Criar** um novo registro como exemplo.

    ![Criar um novo registro](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Forneça o Nome da **Organização** associado ao sistema de CRM. Selecione **Clientes Potenciais** na lista suspensa **Nome da Entidade**.

    ![Selecionar clientes potenciais](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. O Flow mostra um formulário para fornecer informações de clientes potenciais. Você pode mapear itens a partir da solicitação de entrada, optando por adicionar Conteúdo Dinâmico. A captura de tela a seguir mostra **OfferTitle** como exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mapeie os campos desejados e, em seguida, selecione **Salvar** para salvar o fluxo. Uma URL HTTP POST é criada e está acessível na *janela Quando uma solicitação HTTP é recebida.* Copie esta URL usando o controle de cópia que está localizado à direita da URL DO POST HTTP - isso é importante para que você não perca erroneamente qualquer parte de toda a URL. Salve esta URL como você precisará quando estiver configurando o gerenciamento de chumbo no portal de publicação.

    ![Quando uma solicitação HTTP é recebida.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Para configurar as notificações por email

1. Agora que você completou o esquema JSON, selecione **+ Novo passo**.
2. Em **Escolha uma ação**, selecione **Ações**.
3. Em **Ações,** **selecione Enviar um e-mail (Office 365 Outlook)**.

    >[!Note]
    >Se você quiser usar uma pesquisa diferente do provedor de e-mail e selecionar *Enviar uma notificação de e-mail (Mail)* como a ação em vez disso.

    ![Adicionar uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Em Enviar uma janela **de e-mail,** configure os seguintes campos necessários:

   - **Para** - Digite pelo menos um endereço de e-mail válido, para onde os leads serão enviados.
   - **Assunto** – o Flow oferece a opção de adicionar conteúdo dinâmico, como **LeadSource** na captura de tela a seguir. Comece digitando um nome de campo seguido clicando na lista de seleção de conteúdo dinâmico na janela popup. 

        >[!Note] 
        > Ao adicionar nomes de campo, você pode seguir cada um com um ":" e, em seguida, Entrar para criar uma nova linha. Depois de ter seus nomes de campo adicionados, você pode adicionar cada parâmetro associado da lista de seleção dinâmica.

        ![Adicionar uma ação de email usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo** - Na lista de seleção de conteúdo dinâmico, adicione as informações desejadas no corpo do e-mail. Por exemplo, LastName, FirstName e Empresa. <br> <br> Quando tiver terminado de configurar a notificação por email, ela se parecerá com o exemplo na captura de tela a seguir.


       ![Adicionar uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selecione **Salvar** para concluir seu fluxo. Uma URL HTTP POST é criada e está acessível na *janela Quando uma solicitação HTTP é recebida.* Copie esta URL usando o controle de cópia que está localizado à direita da URL DO POST HTTP - isso é importante para que você não perca erroneamente qualquer parte de toda a URL. Salve esta URL como você precisará quando estiver configurando o gerenciamento de chumbo no portal de publicação.

   ![URL HTTP POST ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testes

Você pode testar que tudo funciona como esperado usando as seguintes etapas usando uma ferramenta chamada [Carteiro,](https://app.getpostman.com/app/download/win64)que pode ser baixada online. Isso está disponível para Windows. 

1. Inicie o Carteiro e selecione **Nova** > **solicitação** para configurar sua ferramenta de teste. 

   ![Solicite a configuração da ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Preencha o formulário *Salvar solicitação* e, em seguida, **salve** a pasta que você criou.

   ![Salvar solicitação](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selecione **POST** na lista de desímetros. 

   ![Teste meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Cole a URL HTTP POST do fluxo que você criou no Power Automate onde diz *Enter request URL*.

   ![Cole a URL http post](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Volte para [Flow](https://flow.microsoft.com/) e encontre o fluxo que você criou para enviar leads, indo para **Meus fluxos** da barra de menu Flow.  Selecione os 3 terros ao lado do nome do fluxo e selecione **Editar**.

   ![Meus fluxos - Editar](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selecione **Teste** no canto superior direito, selecione "Executarei a ação do gatilho" e selecione **Teste**. Você verá uma indicação na parte superior da tela indicando que o teste começou

   ![Fluxo de teste - gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Volte para o seu aplicativo Carteiro e selecione **Enviar** ao lado do onde você colou a URL HTTPS.

   ![Teste meu fluxo - Enviar](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Volte ao seu fluxo e verifique o resultado. Se tudo funcionar como esperado, você verá uma mensagem indicando que foi bem sucedida.

   ![Fluxo - Verificar resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Você também deveria ter recebido um e-mail. Verifique sua caixa de entrada de e-mail. 

    >[!Note] 
    >Se você não vir um e-mail do teste, verifique suas pastas de spam e lixo eletrônico. Abaixo você notará apenas as etiquetas de campo adicionadas ao configurar a notificação de e-mail. Se este fosse um lead real gerado a partir de sua oferta, você também veria as informações reais do Contato de Chumbo no corpo e na linha Assunto.

   ![E-mail recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página **de configuração oferta** para obter sua oferta.
2. Selecione **Conectar** na seção Gerenciamento de líderes.
3. Na janela pop-up de detalhes da Conexão, selecione **HTTPS Endpoint** para o **Destino principal** e cole na URL HTTP POST a partir do fluxo criado seguindo etapas anteriores no campo DE URL do ponto **final HTTPS.**
4. **E-mail de contato** - Forneça e-mails para pessoas da sua empresa que devem receber notificações por e-mail quando um novo lead é recebido. Você pode fornecer vários e-mails separando-os com um ponto e vírgula.
5. Selecione **OK**.

Para ter certeza de que você se conectou com sucesso a um destino de chumbo, clique no botão validar. Se for bem sucedido, você terá uma pista de teste no destino principal.

>[!Note] 
>Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta.

Quando clientes potenciais forem gerados, a Microsoft enviará clientes potenciais ao Flow, que serão encaminhados para o sistema de CRM ou endereço de email que você configurou.

![Gerenciamento de lead - conectar](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

