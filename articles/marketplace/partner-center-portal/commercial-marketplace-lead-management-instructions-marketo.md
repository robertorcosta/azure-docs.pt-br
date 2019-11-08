---
title: Configurar gerenciamento de leads no Marketo | Azure Marketplace
description: Configure o gerenciamento de leads para clientes do Marketo para o Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812129"
---
# <a name="configure-lead-management-in-marketo"></a>Configurar o gerenciamento de cliente potencial no Marketo

Este artigo descreve como configurar seu sistema CRM do Marketo para processar os leads de vendas da oferta do Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurar seu sistema de CRM do Marketo

1. Entre no Marketo.
2. Selecione **Design Studio**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Selecione **Novo Formulário**.
    ![Novo formulário do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Preencha os campos obrigatórios no Novo formulário e selecione **Criar**.
    ![Criar novo formulário do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Em Detalhes do Campo, selecione **Concluir**.
    ![Formulário de conclusão do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Aprovar e fechar.

7. Na guia *MarketplaceLeadBacked* , selecione **código de inserção**. 

    ![Código de inserção](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. O Código de Inserção do Marketo exibe um código semelhante ao exemplo a seguir.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copie os valores para os campos abaixo mostrados no formulário de código de inserção. Você usará esses valores para configurar sua oferta para receber leads na próxima etapa. Use o próximo exemplo como guia para obter as IDs que você precisa no exemplo do Código de Inserção.

    - ID do servidor = **ys12**
    - ID de Munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    **Outra maneira de descobrir esses valores**

    - A ID do servidor é encontrada na URL da sua instância do Marketo, por exemplo, "`serverID.marketo.com`".
    - Obtenha a ID do munching da sua assinatura acessando o menu do administrador > Munchkin no campo "ID da conta do Munchkin" ou na primeira parte do subdomínio do host da API REST do Marketo: `https://{Munchkin ID}.mktorest.com`.
    - ID de formulário é a ID do formulário de código de inserção criado na etapa 7 para rotear clientes potenciais de nosso Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurar sua oferta para enviar clientes potenciais ao Marketo

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo: 

1. Navegue até a página de **instalação da oferta** para sua oferta.
1. Selecione **conectar** na seção Gerenciamento de leads. 

    ![Gerenciamento de Lead-conectar](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Na janela pop-up detalhes da conexão, selecione **marketo** para o destino do cliente potencial.

    ![Escolher um destino de cliente potencial](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Forneça a **ID do servidor**, a **ID da conta do MUNCHING**e a **ID do formulário**.

    >[!Note]
    >Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta. 

