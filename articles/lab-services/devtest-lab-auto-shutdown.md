---
title: Gerenciar políticas de desligamento automático no Azure DevTest Labs | Microsoft Docs
description: Aprenda a definir a política de desligamento automático de um laboratório para que as máquinas virtuais sejam desligadas automaticamente quando não estiverem em uso.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270903"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configure o desligamento automático para máquinas virtuais de laboratório e computação no Azure DevTest Labs

Este artigo explica como configurar configurações de desligamento automático para VMs de laboratório em DevTest Labs e VMs de computação. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configure o desligamento automático para VMs de laboratório (DevTest Labs)
O Azure DevTest Labs permite que você controle o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) para cada laboratório. Este artigo mostra como configurar a política de desligamento automático para uma conta de laboratório e configurar configurações de desligamento automático para um laboratório na conta do laboratório. Para exibir como definir cada política de laboratório, confira [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Definir a política de desligamento automático para um laboratório
Como proprietário de um laboratório, você pode configurar uma programação de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar custos com a execução de máquinas que não estão sendo usadas (ociosas). Você pode impor uma política de desligamento em todas as suas VMs de laboratório de forma centralizada, mas também salvar os usuários de seu laboratório com o esforço de configurar um cronograma para suas máquinas individuais. Esse recurso permite que você defina a política em sua programação de laboratório, desde a não oferta de controle a controle total, até seus usuários de laboratório. Como proprietário de um laboratório, você pode configurar essa política seguindo as etapas a seguir:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **Política de desligamento automático** na seção **Horários** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir fornecem mais detalhes sobre essas opções: A política de configuração se aplica apenas às novas VMs criadas no laboratório e não às VMs já existentes. 

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Configure as configurações de desligamento automático
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique o tempo que as VMs deste laboratório desligaram.

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços**e selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Selecione **Configuração e políticas**.

    ![Painel de configurações de política](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. No painel **configuração e políticas** do laboratório, selecione **Auto-shutdown** em **Agendas**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.
7. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.
8. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [Notificações.](#notifications) 
9. Clique em **Salvar**.

    Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel de gerenciamento da VM e altere a configuração **de autoshutdown.**
    
> [!NOTE]
> Se você atualizar o cronograma de desligamento automático para o seu laboratório ou uma máquina virtual de laboratório específica dentro de 30 minutos do horário programado atual, o tempo de desligamento atualizado será aplicado para o cronograma do dia seguinte. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define um agendamento e pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir ou desativar a programação do laboratório. Essa opção concede aos usuários de laboratório controle total sobre o cronograma de desligamento automático de suas VMs. Os usuários de laboratório não veem nenhuma alteração na página de programação de desligamento automático da VM.

![Opção de política de desligamento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define um agendamento e não pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir a programação do laboratório. No entanto, eles não podem desativar a política de desligamento automático. Essa opção garante que todas as máquinas do seu laboratório estejam em um cronograma de desligamento automático. Os usuários de laboratório podem atualizar o cronograma de desligamento automático de suas VMs e configurar notificações de desligamento.

![Opção de política de desligamento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o cronograma definido pelo administrador do laboratório
Se você definir seu laboratório para essa política, os usuários do laboratório não poderão substituir ou recusar a programação do laboratório. Essa opção oferece ao administrador do laboratório o controle completo da programação de cada máquina no laboratório. Os usuários de laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Notificações
Uma vez configurado o desligamento automático pelo proprietário do laboratório, as notificações serão enviadas aos usuários do laboratório 30 minutos antes do desligamento automático acionado se alguma de suas VMs for afetada. Esta opção dá aos usuários de laboratório a chance de salvar seu trabalho antes do desligamento. A notificação também fornece links para cada VM para as seguintes ações:

- Pule o desligamento automático para este momento
- Soneca o desligamento automático por uma hora ou 2 horas, para que eles possam continuar trabalhando na VM.

A notificação é enviada através do ponto final do gancho da Web configurado ou de um endereço de e-mail especificado pelos proprietários de laboratório nas configurações de desligamento automático. Os webhooks permitem que você construa ou configure integrações que se inscrevam em determinados eventos. Quando um desses eventos é acionado, o DevTest Labs enviará uma carga HTTP POST para a URL configurada do webhook. Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Recomendamos que você use ganchos web porque eles são amplamente suportados por vários aplicativos (por exemplo, Slack, Azure Logic Apps, e assim por diante.) e permite que você implemente seu próprio caminho para enviar notificações. Como exemplo, este artigo orienta você sobre como obter notificação de desligamento automático de e-mails usando aplicativos azure logic. Primeiro, vamos rapidamente passar pelas etapas básicas para ativar a notificação de desligamento automático em seu laboratório.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Crie um aplicativo lógico que recebe notificações por e-mail
[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) fornece muitos conectores fora da caixa que facilitam a integração de um serviço com outros clientes, como o Office 365 e o twitter. No alto nível, as etapas para configurar um App Lógico para notificação por e-mail podem ser divididas em quatro fases: 

- Crie um aplicativo lógico. 
- Configure o modelo incorporado.
- Integre-se ao seu cliente de e-mail
- Obtenha a URL do Webhook.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
Para começar, crie um aplicativo lógico na sua assinatura do Azure usando as seguintes etapas:

1. Selecione **+ Crie um recurso** no menu esquerdo, selecione **Integração**e selecione **Logic App**. 

    ![Novo menu de aplicativo lógico](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. No **App Logic - Criar** página, siga estas etapas: 
    1. Digite um **nome** para o aplicativo lógico.
    2. Selecione sua **assinatura**do Azure.
    3. Crie um novo **grupo de recursos** ou selecione um grupo de recursos existente. 
    4. Selecione um **local** para o aplicativo lógico. 

        ![Novo aplicativo lógico - configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **Notificações,** **selecione Ir para recurso** na notificação. 

    ![Ir para o recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **o designer de aplicativos Logic** na categoria Ferramentas de **Implantação.**

    ![Selecione HTTP Solicitação/Resposta](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **HTTP Request-Response,** **selecione Use este modelo**. 

    ![Selecione Usar esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o Seguinte JSON na seção **Esquema JSON do Corpo de Solicitação:** 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Solicitar corpo JSON Schema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ Novo passo** no designer, e siga estes passos:
    1. Procure **o Office 365 Outlook - Envie um e-mail**. 
    2. Selecione **Enviar um e-mail** de **Ações**. 
    
        ![Enviar opção de e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **Entrar** para entrar em sua conta de e-mail. 
    4. Selecione **campo TO** e escolha o proprietário.
    5. Selecione **ASSUNTO**e insira um assunto da notificação por e-mail. Por exemplo: "Desligamento da máquina vmName for Lab: labName."
    6. Selecione **BODY**e defina o conteúdo do corpo para notificação por e-mail. Por exemplo: "vmName está programado para desligar em 15 minutos. Pule esse desligamento clicando em URL. Atraso de desligamento por uma hora: atrasoUrl60. Atraso de desligamento por 2 horas: atrasoUrl120."

        ![Solicitar corpo JSON Schema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecione **Salvar** na barra de ferramentas. Agora, você pode copiar a **URL HTTP POST**. Selecione o botão copiar para copiar a URL na área de transferência. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configure o desligamento automático para VMs de computação

1. Na página **da máquina Virtual,** selecione **Desligamento automático** no menu à esquerda na seção **Operações.** 
2. Na **página de desligamento automático,** selecione **Ativado** para ativar essa diretiva e desativa-a para desativá-la. **Off**
3. Se você habilitar esta diretiva, especifique a **hora** (e **fuso horário)** em que a VM deve ser desligada.
4. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [Notificações.](#notifications) 
9. Clique em **Salvar**.

    ![Configure o desligamento automático para uma VM de computação](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Exibir registros de atividades para atualizações de desligamento automático
Quando você atualiza a configuração de desligamento automático, você verá a atividade registrada no registro de atividades da VM. 

1. No [portal Azure,](https://portal.azure.com)navegue até a página inicial da sua VM.
2. Selecione **O registro de atividades** no menu esquerdo. 
3. Remover **recurso: mycomputevm** dos filtros.
3. Confirme se você vê a operação Adicionar ou modificar horários no registro de **atividades.** Se você não vê-lo, espere por algum momento e atualize o registro de atividades.

    ![Entrada de registro de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Selecione a operação **Adicionar ou modificar horários** para ver as seguintes informações na página **Resumo:**

    - Nome da operação (Adicionar ou modificar horários)
    - A data e a hora em que a configuração de desligamento automático foi atualizada.
    - O endereço de e-mail do usuário que atualizou a configuração. 

        ![Resumo da entrada do registro de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Alterne para a guia **Alterar histórico** na página Adicionar ou **modificar horários,** você verá o histórico de alterações para a configuração. No exemplo a seguir, o horário de desligamento foi alterado de 19:00 para 18:00 em 10 de abril de 2020 às 15:18:47 EST. E a configuração foi desativada às 15:25:09 EST. 

    ![Registro de atividades - alterar histórico](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Para ver mais detalhes sobre a operação, mude para a guia **JSON** na página **Adicionar ou modificar horários.**

## <a name="next-steps"></a>Próximas etapas
Para saber como definir todas as políticas, consulte [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).

