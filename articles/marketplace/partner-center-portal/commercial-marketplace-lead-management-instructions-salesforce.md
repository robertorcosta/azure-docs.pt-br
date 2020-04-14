---
title: Configure gerenciamento de lead para Salesforce | Mercado Azure
description: Configure o gerenciamento de líderes no Salesforce para clientes do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262960"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurar o gerenciamento de clientes potenciais para o Salesforce

Este artigo descreve como configurar seu sistema Salesforce para processar leads de vendas a partir de sua oferta de mercado comercial.

> [!Note]
> O Marketplace não suporta listas pré-preenchidas, como uma lista de valores para o campo **País.** Certifique-se de que não há listas configuradas antes de continuar. Alternativamente, você pode configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="set-up-your-salesforce-system"></a>Configure seu sistema Salesforce

1. Entre no Salesforce.
2. Se você estiver usando a experiência de iluminação da Salesforce.
    1. Selecione **Configuração** na página inicial do Salesforce.
    ![Configuração do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A partir da página Configuração, navegue pela navegação à esquerda até **Plataforma Tools->Feature Settings->Marketing->Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Se você estiver usando a experiência Salesforce Classic:
    1. Selecione **Configuração** na página inicial do Salesforce.
    ![Configuração clássica do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A partir da página Configuração, navegue pela navegação à esquerda para **criar >Personalizar->Leads->Web-to-Lead**.

        ![Salesforce clássico web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

O resto das instruções são as mesmas, independentemente da experiência da Salesforce que você está usando.

4. Na **página Configuração Web-to-Lead,** selecione o botão **Criar formulário web-para-líder.**
5. Em **Instalação do Web-to-Lead**, selecione **Criar Formulário Web-to-Lead**.
    ![Salesforce - Configuração Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. No **Formulário Criar uma Forma Web-to-Lead, certifique-se**de que `the Include reCAPTCHA in HTML` a configuração não está marcada e selecione **Gerar**. 
    ![Salesforce - Crie um formulário Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Você será apresentado com algum texto HTML. Procure o texto "oid" e copie o **valor oid** do texto HTML (apenas o texto entre aspas) e salve-o. Você colará esse valor no campo **Identificador** de Organização no portal de publicação.
    ![Salesforce - Crie um formulário Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Concluído **selecionado**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configure sua oferta para enviar leads para a Salesforce

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página **de configuração oferta** para obter sua oferta.
1. Selecione **Conectar** na seção Gerenciamento de líderes.
    ![Gerenciamento de chumbo - Conectar](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up de detalhes da Conexão, selecione `oid` **Salesforce** para o Destino **líder** e cole no formulário web-to-lead que você criou seguindo etapas anteriores no campo **identificador da Organização.**

1. **E-mail de contato** - Forneça e-mails para pessoas da sua empresa que devem receber notificações por e-mail quando um novo lead é recebido. Você pode fornecer vários e-mails separando-os com ponto e vírgula.

1. Selecione **Ok**.

Para ter certeza de que você se conectou com sucesso a um destino de chumbo, clique no botão validar. Se for bem sucedido, você terá uma pista de teste no destino principal.

>[!Note]
>Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta.

![Detalhes de conexão - Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Detalhes de conexão - Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
