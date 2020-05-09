---
title: Gerenciamento de leads no Marketo – Marketplace comercial da Microsoft
description: Saiba como usar um sistema de CRM do Marketo para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 1f3a097629f8c5f4a3f0ecefa5ee50f2d3e62162
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789871"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Use o Marketo para gerenciar clientes potenciais do Marketplace comercial

Este artigo descreve como configurar seu sistema CRM do Marketo para processar os leads de vendas de suas ofertas no Microsoft AppSource e no Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurar seu sistema de CRM do Marketo

1. Entre no Marketo.

1. Selecione **Design Studio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selecione **Novo Formulário**.

    ![Novo formulário do marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Preencha os campos obrigatórios na caixa de diálogo **novo formulário** e, em seguida, selecione **criar**.

    ![Criar novo formulário do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na página **detalhes do campo** , selecione **concluir**.

    ![Formulário de conclusão do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Aprove e feche.

1. Na guia **MarketplaceLeadBackend** , selecione **código de inserção**. 

    ![Código de inserção do marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. O Código de Inserção do Marketo exibe um código semelhante ao exemplo a seguir.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copie os valores para os campos a seguir mostrados no formulário de código de inserção. Você usará esses valores para configurar sua oferta para receber leads na próxima etapa. Use o exemplo a seguir como um guia para obter as IDs necessárias do exemplo de código de inserção do Marketo.

    - ID do servidor = **ys12**
    - ID de Munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    Outra maneira de descobrir esses valores:

    - A ID do servidor é encontrada na URL da sua instância do Marketo, por `serverID.marketo.com`exemplo,.
    - Obtenha a ID do Munchkin da sua assinatura acessando o menu do **administrador** > **Munchkin** no campo **ID da conta do Munchkin** ou da primeira parte do subdomínio do host da API REST `https://{Munchkin ID}.mktorest.com`do marketo:.
    - ID de formulário é a ID do formulário de código de inserção criado na etapa 7 para rotear clientes potenciais do Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurar sua oferta para enviar clientes potenciais ao Marketo

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga estas etapas. 

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá para a guia **instalação da oferta** .

1. Na seção **Gerenciamento de leads** , selecione **conectar**. 

    ![Botão conectar da seção Gerenciamento de leads](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Na janela pop-up **detalhes da conexão** , selecione **marketo** para o **destino do cliente potencial**.

    ![Escolher um destino de cliente potencial](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Forneça a **ID do servidor**, a **ID da conta do MUNCHKIN**e a **ID do formulário**.

    > [!NOTE]
    > Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta. 

1. Em **email de contato**, insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode fornecer vários endereços de email separando-os com um ponto e vírgula.

1. Selecione **OK**.

   Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione **validar**. Se for bem-sucedido, você terá um líder de teste no destino do cliente potencial.

   ![Janela pop-up detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
