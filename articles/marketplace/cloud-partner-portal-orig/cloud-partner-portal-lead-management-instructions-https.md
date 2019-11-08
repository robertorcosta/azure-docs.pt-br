---
title: Ponto de extremidade HTTPS | Azure Marketplace
description: Configurar o gerenciamento de cliente potencial para um ponto de extremidade HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pabutler
ms.openlocfilehash: 817e431f5386b10345d414190e8bda0954ef2aca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825222"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de cliente potencial usando um ponto de extremidade HTTPS

É possível usar um ponto de extremidade HTTPS para tratar clientes potenciais do AppSource e do Microsoft Azure Marketplace. Esses clientes potenciais podem ser gravados em um sistema de CRM (Gerenciamento de relacionamento com o cliente) ou enviados como uma notificação por email. Este artigo descreve como configurar o gerenciamento de cliente potencial usando o serviço de automação do [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/).

## <a name="create-a-flow-using-microsoft-flow"></a>Criar um fluxo usando o Microsoft Flow

1. Abra a página da Web do [Flow](https://flow.microsoft.com/). Selecione **Inscrever-se** ou selecione**Inscreva-se gratuitamente** para criar uma conta gratuita do Flow.

2. Inscreva-se e selecione **Meus fluxos** na barra de menus.

    ![Meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Selecione **+ Criar em branco**.

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Selecione **Criar em branco**.

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. No campo **Pesquisar conectores e gatilhos**, digite "solicitar" para localizar o conector de Solicitação.
6. Em **Gatilhos**, selecione **Quando uma solicitação HTTP é recebida**. 

    ![Selecione o gatilho de solicitação HTTP recebido](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Use uma das etapas a seguir para configurar o **Esquema JSON do Corpo da Solicitação**:

   - Copie o [Esquema JSON](#json-schema) no final deste artigo na caixa de texto **Esquema JSON do Corpo da Solicitação**.
   - Selecione **Use o conteúdo de amostra para gerar o esquema**. Na caixa de texto **Digite ou cole uma carga de exemplo de conteúdo JSON**, cole o [Exemplo de JSON](#json-example). Selecione **Concluído** para criar o esquema.

   >[!Note]
   >Neste ponto do fluxo, você pode se conectar a um sistema de CRM ou configurar uma notificação por email.

### <a name="to-connect-to-a-crm-system"></a>Para se conectar a um sistema de CRM

1. Selecione **+ Nova Etapa**.
2. Escolha o sistema de CRM de sua preferência com a ação para criar um novo registro. A próxima captura de tela mostra **Dynamics 365 – Criar um novo registro** como um exemplo.

    ![Criar um novo registro](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Forneça o **Nome da Organização** que é a conexão de entrada de seu conector. Selecione **Clientes Potenciais** na lista suspensa **Nome da Entidade**.

    ![Selecionar clientes potenciais](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. O Flow mostra um formulário para fornecer informações de clientes potenciais. É possível mapear itens da solicitação de entrada, escolhendo adicionar conteúdo dinâmico. A captura de tela a seguir mostra **OfferTitle** como exemplo.

    ![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapeie os campos desejados e, em seguida, selecione **Salvar** para salvar o fluxo.

6. Uma URL de HTTP POST é criada na solicitação. Copie essa URL e use-a como o ponto de extremidade HTTPS.

    ![URL de HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Para configurar as notificações por email

1. Selecione **+ Nova Etapa**.
2. Em **Escolha uma ação**, selecione **Ações**.
3. Em **Ações**, selecione **Enviar um email**.

    ![Adicionar uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Em **Enviar um email**, configure os campos obrigatórios a seguir:

   - **Para** – insira pelo menos um endereço de email válido.
   - **Assunto** – o Flow oferece a opção de adicionar conteúdo dinâmico, como **LeadSource** na captura de tela a seguir.

     ![Adicionar uma ação de email usando conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corpo** – na lista de conteúdo dinâmico, adicione as informações desejadas no corpo do email. Por exemplo, LastName, FirstName e Empresa.

   Quando tiver terminado de configurar a notificação por email, ela se parecerá com o exemplo na captura de tela a seguir.

   ![Adicionar uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecione **Salvar** para concluir seu fluxo.
6. Uma URL de HTTP POST é criada na solicitação. Copie essa URL e use-a como o ponto de extremidade HTTPS.

    ![URL de HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Ao configurar as informações de gerenciamento de cliente potencial para a oferta, selecione **Ponto de Extremidade HTTPS** do **Destino do Cliente Potencial** e cole a URL de HTTP POST copiada na etapa anterior.  

![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando clientes potenciais forem gerados, a Microsoft enviará clientes potenciais ao Flow, que serão encaminhados para o sistema de CRM ou endereço de email que você configurou.

## <a name="json-schema-and-example"></a>Esquema JSON e exemplo

O exemplo de teste do JSON usa o esquema a seguir:

### <a name="json-schema"></a>JSON schema

``` json
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

Você pode copiar e editar o seguinte exemplo de JSON a ser usado como um teste em seu MS Flow.

### <a name="json-example"></a>Exemplo de JSON

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Próximas etapas

Se ainda não tiver feito isso, configure [clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) no Portal do Cloud Partner.
