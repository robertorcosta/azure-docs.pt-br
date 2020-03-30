---
title: Configure gestão de lead no Marketo | Mercado Azure
description: Configure a gestão de líderes para os clientes do mercado Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281503"
---
# <a name="configure-lead-management-in-marketo"></a>Configurar o gerenciamento de cliente potencial no Marketo

Este artigo descreve como configurar seu sistema de CRM Marketo para processar leads de vendas a partir de sua oferta de marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configure seu sistema de CRM Marketo

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

7. Na guia *MarketplaceLeadBacked,* selecione **Embed Code**. 

    ![Código de inserção](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. O Código de Inserção do Marketo exibe um código semelhante ao exemplo a seguir.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copie os valores para os campos abaixo mostrados no formulário Código de incorporar. Você usará esses valores para configurar sua oferta para receber leads na próxima etapa. Use o próximo exemplo como guia para obter as IDs que você precisa no exemplo do Código de Inserção.

    - ID do servidor = **ys12**
    - ID munchkin = **123-PQR-789**
    - ID do Formulário = **1179**

    **Outra maneira de descobrir esses valores**

    - O ID do servidor é encontrado na URL da`serverID.marketo.com`sua instância marketo, por exemplo, ".
    - Obtenha o ID de mastigação da sua assinatura indo ao seu menu De administração>Munchkin no campo "Munchkin Account ID", ou a partir da primeira parte do seu subdomínio de host da API Marketo REST: `https://{Munchkin ID}.mktorest.com`.
    - O Form ID é o ID do formulário Embed Code que você criou na etapa 7 para rodar leads do nosso marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configure sua oferta para enviar leads para o Marketo

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga as etapas abaixo: 

1. Navegue até a página **de configuração oferta** para obter sua oferta.
1. Selecione **Conectar** na seção Gerenciamento de líderes. 

    ![Gerenciamento de chumbo - Conectar](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Na janela pop-up de detalhes da Conexão, **selecione Marketo** para o Destino Principal.

    ![Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Forneça o **ID**do servidor, **o ID da conta**de Munching e **o ID do Formulário.**

    >[!Note]
    >Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta. 

