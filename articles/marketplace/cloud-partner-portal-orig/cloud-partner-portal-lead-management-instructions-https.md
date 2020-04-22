---
title: Configure o gerenciamento de lead usando um ponto final HTTPS | Mercado Azure
description: Aprenda a usar um ponto final HTTP para lidar com os leads do Microsoft AppSource e do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770151"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de cliente potencial usando um ponto de extremidade HTTPS

Você pode usar um ponto final HTTPS para lidar com os leads Microsoft AppSource e Azure Marketplace. Esses leads podem ser gravados em um sistema de CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente) ou enviados como uma notificação por e-mail. Este artigo descreve como usar o serviço de automação [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) para configurar o gerenciamento de chumbo.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Crie um fluxo usando o Microsoft Power Automate

1. Abra a página do [Power Automate.](https://flow.microsoft.com/) Selecione **Inscrever-se** ou selecione**Inscreva-se gratuitamente** para criar uma conta gratuita do Flow.

1. Inscreva-se e selecione **Meus fluxos** na barra de menus.
    > [!div class="mx-imgBorder"]
    > ![Meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Em **+ Novo**, selecione + Instantâneo — em **branco**.
    > [!div class="mx-imgBorder"]
    > ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie seu fluxo e, em seguida, **Escolha como ativar esse fluxo,** **selecione Quando uma solicitação HTTP for recebida**.

    > [!div class="mx-imgBorder"]
    > ![Selecione o gatilho de solicitação HTTP recebido](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique na etapa de fluxo para expandi-lo.

    > [!div class="mx-imgBorder"]
    > ![Expanda a etapa de fluxo](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Use um dos seguintes métodos para configurar o **Esquema JSON do Corpo de Solicitação**:

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

Quando os leads são gerados, a Microsoft envia leads para o fluxo power automate, que são roteados para o sistema de CRM ou endereço de e-mail que você configurou.

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
    "Description": {
      "id": "/properties/Description",
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

Você pode copiar e editar o seguinte exemplo JSON para usar como um teste no seu fluxo.

### <a name="json-example"></a>Exemplo de JSON

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

## <a name="next-steps"></a>Próximas etapas

Se ainda não tiver feito isso, configure [clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) no Portal do Cloud Partner.
