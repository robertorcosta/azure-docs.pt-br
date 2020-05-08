---
title: Log de tíquete de suporte para Azure Stack Edge, Gateway do Azure Data Box | Microsoft Docs
description: Saiba como registrar em log a solicitação de suporte para problemas relacionados ao seu Azure Stack Edge ou Gateway do Data Box ordens.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 3839fb325b1ed0c052f7a4e8955e9a9fda51fc5f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569648"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Abrir um tíquete de suporte para Azure Stack borda e Gateway do Azure Data Box

Este artigo se aplica a Azure Stack borda e Gateway do Azure Data Box ambos são gerenciados pelo serviço Azure Stack Edge/Gateway do Azure Data Box. Se você encontrar problemas com o serviço, poderá criar uma solicitação de serviço para o suporte técnico. Este artigo orienta você sobre:

* Como criar uma solicitação de suporte.
* Como gerenciar o ciclo de vida de uma solicitação de suporte de dentro do portal.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Execute as seguintes etapas para criar uma solicitação de suporte:

1. Vá para o Azure Stack borda ou Gateway do Data Box ordem. Navegue até a seção **suporte + solução de problemas** e selecione **nova solicitação de suporte**.
   
2. Em **nova solicitação de suporte**, na guia **noções básicas** , execute as seguintes etapas:
    
    1. Na lista suspensa **Tipo de problema**, selecione **Técnico**.
    2. Escolha sua **assinatura**.
    3. Em **Serviço**, verifique **Meus serviços**. Na lista suspensa, selecione **Azure Stack borda e gateway do data Box**.
    4. Selecione seu **recurso**. Isso corresponde ao nome do seu pedido.
    5. Forneça um breve **Resumo** do problema que você está enfrentando. 
    6. Selecione o **tipo de problema**.
    7. Com base no tipo de problema selecionado, escolha um **subtipo de problema**correspondente.
    8. Selecione **Avançar: soluções >>**.

        ![Noções básicas](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Na guia **detalhes** , execute as seguintes etapas:
    
    1. Forneça a data de início e a hora para o problema.
    2. Forneça uma **Descrição** para o problema.
    3. No **upload do arquivo**, selecione o ícone de pasta para procurar outros arquivos que você deseja carregar.
    4. Marque **Compartilhar informações de diagnóstico**.
    5. Com base em sua assinatura, um **plano de suporte** é preenchido automaticamente.
    6. Na lista suspensa, selecione a **severidade**.
    7. Especifique um **método de contato preferencial**.
    8. As **horas de resposta** são selecionadas automaticamente com base em seu plano de assinatura.
    9. Forneça o idioma de sua preferência para obter suporte.
    10. Nas **informações de contato**, forneça seu nome, email, telefone, contato opcional, país/região. O Suporte da Microsoft usará essas informações para entrar em contato com você para obter mais informações, diagnóstico e resolução. 
    11. Selecione **Avançar: revisar + criar >>**.

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Na guia **revisar + criar** , examine as informações relacionadas ao tíquete de suporte. Selecione **Criar**. 

    ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Depois de criar o tíquete de suporte, um engenheiro de suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## <a name="get-hardware-support"></a>Obter suporte de hardware

Essas informações se aplicam somente ao dispositivo Azure Stack. O processo para relatar problemas de hardware é o seguinte:

1. Abra um tíquete de suporte no portal do Azure para um problema de hardware. Em **tipo de problema**, selecione **Azure Stack hardware**. Escolha o **subtipo de problema** como **falha de hardware**. 

    ![Problema de hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Depois de criar o tíquete de suporte, um engenheiro de suporte entrará em contato com você assim que possível para prosseguir com sua solicitação. 

2. Se Suporte da Microsoft determinar que esse é um problema de hardware, uma das seguintes ações ocorrerá: 

    - Uma FRU (unidade de substituição de campo) para a parte de hardware com falha é enviada. Atualmente, a unidade de fonte de alimentação é a única FRU com suporte. 
    - Para qualquer outra falha de parte, a Microsoft faz uma substituição completa do sistema (FSR) ou uma troca de dispositivo.

3. Se um tíquete de suporte for gerado antes de 4:30 Hora local (de segunda a sexta), um técnico no local será expedido no próximo dia útil para o local para executar uma FRU ou uma substituição completa do dispositivo.

## <a name="manage-a-support-request"></a>Gerenciar uma solicitação de suporte

Depois de criar um tíquete de suporte, você pode gerenciar o ciclo de vida do tíquete de dentro do portal.

#### <a name="to-manage-your-support-requests"></a>Para gerenciar suas solicitações de suporte

1. Para acessar a página de ajuda e suporte navegue até **Procurar -> Ajuda + suporte**.

    ![Gerenciar solicitações de suporte](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)   

2. Uma lista tabular de **Solicitações recentes de suporte** é exibida em **Ajuda + suporte**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Selecione e clique em uma solicitação de suporte. Você pode exibir o status e os detalhes para esta solicitação. Clique em **+ Nova mensagem** se desejar acompanhar esta solicitação.

   
## <a name="next-steps"></a>Próximas etapas

Saiba como [solucionar problemas relacionados ao Azure Stack Edge](azure-stack-edge-troubleshoot.md).
Saiba como [solucionar problemas relacionados a gateway do data Box](data-box-gateway-troubleshoot.md).
