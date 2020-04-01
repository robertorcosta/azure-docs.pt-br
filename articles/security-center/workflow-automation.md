---
title: Automação de fluxo de trabalho no Azure Security Center | Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho no Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397907"
---
# <a name="workflow-automation"></a>Automação do fluxo de trabalho

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir notificar as partes interessadas relevantes, iniciar um processo de gerenciamento de alterações e aplicar etapas específicas de remediação. Especialistas em segurança recomendam que você automatize o máximo de etapas desses procedimentos. A automação reduz a sobrecarga. Ele também pode melhorar sua segurança, garantindo que as etapas do processo sejam feitas de forma rápida, consistente e de acordo com seus requisitos predefinidos.

Este artigo descreve o recurso de automação do fluxo de trabalho do Azure Security Center. Esse recurso pode acionar os Logic Apps em alertas e recomendações de segurança. Por exemplo, você pode querer que o Security Center envie um e-mail para um usuário específico quando um alerta ocorrer. Você também aprenderá como criar aplicativos lógicos usando [aplicativos de lógica do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Se você usou anteriormente a exibição Playbooks (Preview) na barra lateral, encontrará os mesmos recursos juntamente com a funcionalidade expandida na nova página de automação do fluxo de trabalho.


## <a name="requirements"></a>Requisitos

* Para trabalhar com fluxos de trabalho do Azure Logic Apps, você deve ter as seguintes funções/permissões de aplicativos lógicos:

    * [As](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) permissões do Logic App Operator são necessárias ou o logic app ler/acionar o acesso (essa função não pode criar ou editar aplicativos lógicos; apenas *executar* os existentes)

    * [Permissões de contribuinte de aplicativos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) lógicos são necessárias para criação e modificação do Logic App

* Se você quiser usar conectores logic app, você pode precisar de credenciais adicionais para fazer login em seus respectivos serviços (por exemplo, suas instâncias Outlook/Teams/Slack)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Crie um app lógico e defina quando ele deve ser executado automaticamente 

1. Na barra lateral do Security Center, selecione **automação de fluxo de trabalho**.

    [![Lista de automações de fluxo de trabalho](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    A partir desta página, você pode criar novas regras de automação, bem como ativar, desativar ou excluir as existentes.  
1. Para definir um novo fluxo de trabalho, clique **em Adicionar automação de fluxo de trabalho**. 

    Um painel aparece com as opções para sua nova automação. Aqui você pode entrar:
    1. Um nome e descrição para a automação.
    1. Os gatilhos que iniciarão este fluxo de trabalho automático. Por exemplo, você pode querer que seu App Logic seja executado quando um alerta de segurança que contenha "SQL" for gerado.
    1. O App Lógico que será executado quando suas condições de gatilho forem atendidas. 

        [![Lista de automações de fluxo de trabalho](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Na seção Ações, clique em **Criar um novo** para iniciar o processo de criação do App Lógico.

    Você será levado para a Azure Logic Apps.

    [![Criando um novo aplicativo lógico](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Digite um nome, grupo de recursos e localização e clique **em Criar**.

1. No seu novo App Logic, você pode escolher entre modelos integrados e predefinidos da categoria de segurança. Ou você pode definir um fluxo personalizado de eventos para ocorrer quando esse processo é acionado.

    No designer logic app, os seguintes gatilhos dos conectores do Security Center são suportados:

    * **Quando uma recomendação do Azure Security Center é criada ou acionada**
    * **Quando um alerta do Azure Security Center é criado ou acionado** 
    
    > [!TIP]
    > Você pode personalizar o gatilho para que ele se relacione apenas com alertas com os níveis de gravidade que lhe interessam.
    
    > [!NOTE]
    > Se você estiver usando o gatilho legado "Quando uma resposta a um alerta do Azure Security Center for acionada", seus Aplicativos lógicos não serão lançados pelo recurso Workflow Automation. Em vez disso, use qualquer um dos gatilhos mencionados acima. 

    [![Aplicativo de lógica de amostra](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir seu App Lógico, retorne ao painel de definição de automação do fluxo de trabalho ("Adicionar automação de fluxo de trabalho"). Clique **em Atualizar** para garantir que seu novo Aplicativo Lógico esteja disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione seu App Lógico e salve a automação. Observe que a parada do App Logic mostra apenas aplicativos lógicos com conectores do Security Center de suporte mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Acione manualmente um aplicativo lógico

Você também pode executar aplicativos lógicos manualmente ao visualizar um alerta de segurança ou qualquer recomendação que ofereça [correção rápida](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Para executar manualmente um aplicativo lógico, abra um alerta ou uma recomendação que suporte a correção rápida e clique **em Trigger Logic App**:

[![Acione manualmente um aplicativo lógico](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Tipos de dados schemas

Para visualizar os esquemas de eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância do App Lógico, visite os [tipos de dados de automação do Workflow](https://aka.ms/ASCAutomationSchemas). Isso pode ser útil nos casos em que você não está usando os conectores de aplicativo lógico incorporados do Security Center mencionados acima, mas, em vez disso, está usando o conector HTTP genérico do Logic App - você pode usar o esquema JSON do evento para analisá-lo manualmente como achar melhor.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre criar aplicativos lógicos, automatizar sua execução no Security Center e executá-los manualmente. 

Para outros materiais relacionados, consulte: 

- [O módulo Microsoft Learn sobre como usar a automação do fluxo de trabalho para automatizar uma resposta de segurança](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
- [Sobre os Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Conectores de Aplicativos Lógicos](https://docs.microsoft.com/connectors/)
- [Tipos de dados de automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas)
