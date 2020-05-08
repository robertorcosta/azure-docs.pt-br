---
title: Gerenciamento de leads no Salesforce-Microsoft Commercial Marketplace
description: Saiba como usar o Salesforce para configurar clientes potenciais para o Microsoft AppSource e o Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7d64b8914fa0b109dfc662a97a7f84d94e3491ec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789701"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurar o gerenciamento de clientes potenciais para o Salesforce

Este artigo descreve como configurar seu sistema Salesforce para processar leads de vendas de suas ofertas no Microsoft AppSource e no Azure Marketplace.

> [!NOTE]
> O Azure Marketplace não dá suporte a listas preenchidas previamente, como uma lista de valores para o campo **país** . Certifique-se de que não há nenhuma lista configurada antes de continuar. Como alternativa, você pode configurar um [ponto de extremidade https](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber clientes potenciais.

## <a name="set-up-your-salesforce-system"></a>Configurar seu sistema Salesforce

1. Entre no Salesforce.
1. Navegue até as configurações **da Web para o Lead** . 
    
    Se você estiver usando a experiência de iluminação do Salesforce
    1. Selecione **instalação** no Home Page do Salesforce.

       ![Configuração do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página **instalação** , vá para **ferramentas** > de**recursos configurações** > de recurso**marketing** > de**Web a cliente potencial**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Se você estiver usando a experiência clássica do Salesforce:

    1. Selecione **instalação** no Home Page do Salesforce.

       ![Configuração do Salesforce clássico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página **instalação** , selecione **criar** > **Personalizar** > **clientes potenciais** > **da Web para o Lead**.

        ![Web-to-Lead clássico do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   As etapas restantes são as mesmas para as duas experiências do Salesforce.

1. Na página **configuração da Web para o cliente potencial** , selecione o botão **criar formulário da Web para o Lead** .
1. Na **configuração da Web para o Lead**, selecione **criar um formulário da Web para o Lead**.

    ![Configuração da Web para Lead do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Em **criar um formulário da Web para o Lead**, verifique se a `Include reCAPTCHA in HTML` configuração está desmarcada e selecione **gerar**.

    ![Salesforce criar um painel de formulário da Web para o Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Você verá um texto HTML. Procure o texto "OID" e copie o **valor de "OID"** do texto HTML (somente o texto entre aspas) e salve-o. Você colará esse valor no campo **identificador da organização** no portal de publicação.

    ![Salesforce criar um formulário da Web para o Lead mostrando o valor HTML "OID"](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selecione **concluído**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurar sua oferta para enviar clientes potenciais ao Salesforce

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga estas etapas.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá para a guia **instalação da oferta** .

1. Na seção **Gerenciamento de leads** , selecione **conectar**. 

    ![Botão conectar da seção Gerenciamento de leads](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up **detalhes da conexão** , selecione **Salesforce** para o **destino do cliente potencial** e `oid` Cole o valor do formulário da Web para o Lead que você criou no campo **identificador da organização** .

    ![Janela pop-up detalhes da conexão caixa validar email de contato](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Em **email de contato**, insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode fornecer vários emails separando-os com um ponto e vírgula.

1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione **validar**. Se for bem-sucedido, você terá um líder de teste no destino do cliente potencial.

>[!NOTE]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.
