---
title: Gerenciar políticas de desligamento automático no Azure DevTest Labs | Microsoft Docs
description: Saiba como definir a política de desligamento automático para um laboratório para que as máquinas virtuais sejam desligadas automaticamente quando não estiverem em uso.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a30070470f9a75ec5c56d448cd09ca82dd0cbce7
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287549"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configurar o desligamento automático para máquinas virtuais de laboratório e computação no Azure DevTest Labs

Este artigo explica como definir as configurações de desligamento automático para VMs de laboratório no DevTest Labs e VMs de computação. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configurar o desligamento automático para VMs de laboratório (DevTest Labs)
O Azure DevTest Labs permite que você controle o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) para cada laboratório. Este artigo mostra como configurar a política de desligamento automático para uma conta de laboratório e definir as configurações de desligamento automático para um laboratório na conta do laboratório. Para exibir como definir cada política de laboratório, confira [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Definir a política de desligamento automático para um laboratório
Como proprietário de um laboratório, você pode configurar uma programação de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar custos com a execução de máquinas que não estão sendo usadas (ociosas). Você pode impor uma política de desligamento em todas as suas VMs de laboratório de forma centralizada, mas também salvar os usuários de seu laboratório com o esforço de configurar um cronograma para suas máquinas individuais. Esse recurso permite que você defina a política em sua programação de laboratório, desde a não oferta de controle a controle total, até seus usuários de laboratório. Como proprietário de um laboratório, você pode configurar essa política seguindo as etapas a seguir:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **Política de desligamento automático** na seção **Horários** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir fornecem mais detalhes sobre essas opções: A política de configuração se aplica apenas às novas VMs criadas no laboratório e não às VMs já existentes. 

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Definir configurações de desligamento automático
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs do laboratório são desligadas.

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Selecione **Configuração e políticas**.

    ![Painel de configurações de política](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. No painel **configuração e políticas** do laboratório, selecione **desligamento automático** em **agendas**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.
7. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.
8. Especifique **Sim** ou **não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) . 
9. Clique em **Salvar**.

    Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel Gerenciamento da VM e altere sua configuração de **desligamento** automático.
    
> [!NOTE]
> Se você atualizar a agenda de desligamento automático para seu laboratório ou para uma máquina virtual de laboratório específica dentro de 30 minutos da hora agendada atual, o tempo de desligamento atualizado será aplicado na agenda do dia seguinte. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define um agendamento e pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir ou desativar a programação do laboratório. Essa opção concede aos usuários de laboratório controle total sobre o cronograma de desligamento automático de suas VMs. Os usuários de laboratório não veem nenhuma alteração na página de programação de desligamento automático da VM.

![Opção de política de desligamento automático-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define um agendamento e não pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir a programação do laboratório. No entanto, eles não podem desativar a política de desligamento automático. Essa opção garante que todas as máquinas do seu laboratório estejam em um cronograma de desligamento automático. Os usuários de laboratório podem atualizar o cronograma de desligamento automático de suas VMs e configurar notificações de desligamento.

![Opção de política de desligamento automático-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o cronograma definido pelo administrador do laboratório
Se você definir seu laboratório para essa política, os usuários do laboratório não poderão substituir ou recusar a programação do laboratório. Essa opção oferece ao administrador do laboratório o controle completo da programação de cada máquina no laboratório. Os usuários de laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Notificações
Após o desligamento automático configurado pelo proprietário do laboratório, as notificações serão enviadas para os usuários do laboratório 30 minutos antes do desligamento automático ser disparado se qualquer uma de suas VMs for afetada. Essa opção dá aos usuários do laboratório a oportunidade de salvar seu trabalho antes do desligamento. A notificação também fornece links para cada VM para as seguintes ações:

- Ignorar o desligamento automático para este tempo
- Adiar o desligamento automático por uma hora ou 2 horas, para que eles possam continuar trabalhando na VM.

A notificação é enviada por meio do ponto de extremidade de gancho da Web configurado ou de um endereço de email especificado pelos proprietários do laboratório nas configurações de desligamento automático. WebHooks permitem que você crie ou configure integrações que assinam determinados eventos. Quando um desses eventos for disparado, o DevTest Labs enviará uma carga HTTP POST para a URL configurada do webhook. Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-bindings-http-webhook.md). 

Recomendamos que você use os ganchos da Web porque eles são amplamente suportados por vários aplicativos (por exemplo, margem de atraso, aplicativos lógicos do Azure e assim por diante) e permite que você implemente sua própria maneira de enviar notificações. Por exemplo, este artigo explica como obter notificações de desligamento automático de emails usando aplicativos lógicos do Azure. Primeiro, vamos percorrer rapidamente as etapas básicas para habilitar a notificação de desligamento automático em seu laboratório.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Criar um aplicativo lógico que recebe notificações por email
Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) fornecem muitos conectores prontos para uso que facilitam a integração de um serviço com outros clientes, como o Office 365 e o Twitter. No alto nível, as etapas para configurar um aplicativo lógico para notificação por email podem ser divididas em quatro fases: 

- Crie um aplicativo lógico. 
- Configure o modelo interno.
- Integre com seu cliente de email
- Obtenha a URL do webhook.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
Para começar, crie um aplicativo lógico em sua assinatura do Azure usando as seguintes etapas:

1. Selecione **+ criar um recurso** no menu à esquerda, selecione **integração**e selecione **aplicativo lógico**. 

    ![Novo menu de aplicativo lógico](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na página **aplicativo lógico – criar** , siga estas etapas: 
    1. Insira um **nome** para o aplicativo lógico.
    2. Selecione sua **assinatura**do Azure.
    3. Crie um novo **grupo de recursos** ou selecione um grupo de recursos existente. 
    4. Selecione um **local** para o aplicativo lógico. 

        ![Novo aplicativo lógico-configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **notificações**, selecione **ir para o recurso** na notificação. 

    ![Acessar recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **Designer de aplicativo lógico** na categoria **ferramentas de implantação** .

    ![Selecionar solicitação/resposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **solicitação-resposta http** , selecione **usar este modelo**. 

    ![Selecione usar esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o JSON a seguir na seção **esquema JSON do corpo da solicitação** : 

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
    
    ![Esquema JSON do corpo da solicitação](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ nova etapa** no designer e siga estas etapas:
    1. Pesquise pelo **Office 365 Outlook-enviar um email**. 
    2. Selecione **enviar um email** de **ações**. 
    
        ![Opção Enviar email](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **entrar** para entrar em sua conta de email. 
    4. Selecione **para** o campo e escolha proprietário.
    5. Selecione **assunto**e insira um assunto da notificação por email. Por exemplo: "desligamento do Machine vmName for Lab: labName."
    6. Selecione **corpo**e defina o conteúdo do corpo para notificação por email. Por exemplo: "vmName está agendado para desligar em 15 minutos. Ignore esse desligamento clicando em: URL. Atrasar o desligamento por uma hora: delayUrl60. Atraso no desligamento por 2 horas: delayUrl120. "

        ![Esquema JSON do corpo da solicitação](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecione **Salvar** na barra de ferramentas. Agora, você pode copiar a **URL http post**. Selecione o botão Copiar para copiar a URL para a área de transferência. 

    ![URL do webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configurar o desligamento automático para VMs de computação

1. Na página **máquina virtual** , selecione **desligamento automático** no menu à esquerda na seção **operações** . 
2. Na página **desligamento automático** , selecione **ativado** para habilitar essa política e **desativado** para desabilitá-la.
3. Se você habilitar essa política, especifique a **hora** (e o **fuso horário**) em que a VM deve ser desligada.
4. Especifique **Sim** ou **não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) . 
9. Clique em **Salvar**.

    ![Configurar o desligamento automático para uma VM de computação](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Exibir logs de atividade para atualizações de desligamento automático
Ao atualizar a configuração de desligamento automático, você verá a atividade registrada no log de atividades da VM. 

1. Na [portal do Azure](https://portal.azure.com), navegue até o Home Page da VM.
2. Selecione **log de atividades** no menu à esquerda. 
3. Remover **recurso: mycomputevm** dos filtros.
3. Confirme que você vê a operação **Adicionar ou modificar agendas** no log de atividades. Se você não o vir, aguarde algum tempo e atualize o log de atividades.

    ![Entrada do log de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Selecione a operação **Adicionar ou modificar agendas** para ver as seguintes informações na página **Resumo** :

    - Nome da operação (adicionar ou modificar agendas)
    - A data e a hora em que a configuração de desligamento automático foi atualizada.
    - O endereço de email do usuário que atualizou a configuração. 

        ![Resumo da entrada do log de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Alterne para a guia **histórico de alterações** na página **Adicionar ou modificar agendas** , você vê o histórico de alterações para a configuração. No exemplo a seguir, o tempo de desligamento foi alterado de 7 para 6 PM em 10 de abril de 2020 à EST 15:18:47. E, a configuração foi desabilitada em 15:25:09 EST. 

    ![Log de atividades – histórico de alterações](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Para ver mais detalhes sobre a operação, alterne para a guia **JSON** na página **Adicionar ou modificar agendas** .

## <a name="next-steps"></a>Próximas etapas
Para saber como definir todas as políticas, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).
