---
title: Automação de fluxo de trabalho na central de segurança do Azure | Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 0eac72832d75c0c26addaef283015155c09b3935
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529362"
---
# <a name="workflow-automation"></a>Automação do fluxo de trabalho

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga. Ele também pode melhorar sua segurança, garantindo que as etapas do processo sejam feitas de forma rápida, consistente e de acordo com seus requisitos predefinidos.

Este artigo descreve o recurso de automação de fluxo de trabalho da central de segurança do Azure. Esse recurso pode disparar aplicativos lógicos em alertas de segurança e recomendações. Por exemplo, talvez você queira que a central de segurança envie por email um usuário específico quando ocorrer um alerta. Você também aprenderá a criar aplicativos lógicos usando [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Se você usou anteriormente a exibição de guias estratégicos (versão prévia) na barra lateral, encontrará os mesmos recursos junto com a funcionalidade expandida na nova página de automação de fluxo de trabalho.



## <a name="availability"></a>Disponibilidade

- Estado da versão: **Disponível para o público geral**
- Funções e permissões necessárias:
    - **Leitor** na assinatura que contém a configuração de exportação
    - **Função de administrador de segurança** no grupo de recursos (ou **proprietário**)
    - Também deve ter permissões de gravação para o recurso de destino
    - Além disso, para trabalhar com fluxos de trabalho de aplicativos lógicos do Azure, você deve ter as seguintes funções/permissões de aplicativos lógicos:
        - Permissões de [operador de aplicativo lógico](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) são necessárias ou acesso de leitura/gatilho do aplicativo lógico (essa função não pode criar ou editar aplicativos lógicos; *executar* apenas os existentes)
        - As permissões de [colaborador do aplicativo lógico](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) são necessárias para a criação e modificação do aplicativo lógico
        - Se você quiser usar conectores de aplicativos lógicos, talvez precise de credenciais adicionais para entrar em seus respectivos serviços (por exemplo, suas instâncias de Outlook/equipes/margem de atraso)
- Nuvens: 
    - ✔ Nuvens comerciais
    - ✔ Nuvem do governo dos EUA
    - Nuvem do governo ✘ na China, outras nuvens gov


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Criar um aplicativo lógico e definir quando ele deve ser executado automaticamente 

1. Na barra lateral da central de segurança, selecione **automação de fluxo de trabalho**.

    [![Lista de automaçãos de fluxo de trabalho](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Nessa página, você pode criar novas regras de automação, bem como habilitar, desabilitar ou excluir aquelas existentes.  
1. Para definir um novo fluxo de trabalho, clique em **Adicionar automação de fluxo de trabalho**. 

    Um painel é exibido com as opções para sua nova automação. Aqui você pode inserir:
    1. Um nome e uma descrição para a automação.
    1. Os gatilhos que iniciarão esse fluxo de trabalho automático. Por exemplo, talvez você queira que seu aplicativo lógico seja executado quando um alerta de segurança que contenha "SQL" for gerado.
    1. O aplicativo lógico que será executado quando suas condições de disparo forem atendidas. 

        [![Lista de automaçãos de fluxo de trabalho](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Na seção ações, clique em **criar um novo** para iniciar o processo de criação do aplicativo lógico.

    Você será levado para os aplicativos lógicos do Azure.

    [![Criando um novo aplicativo lógico](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Insira um nome, grupo de recursos e local e clique em **criar**.

1. Em seu novo aplicativo lógico, você pode escolher entre modelos predefinidos internos da categoria segurança. Ou você pode definir um fluxo de eventos personalizado para ocorrer quando esse processo é disparado.

    No designer do aplicativo lógico, há suporte para os seguintes gatilhos dos conectores da central de segurança:

    * **Quando uma recomendação da central de segurança do Azure é criada ou disparada**
    * **Quando um alerta da central de segurança do Azure é criado ou disparado** 
    
    > [!TIP]
    > Você pode personalizar o gatilho para que ele se relacione apenas a alertas com os níveis de severidade que lhe interessam.
    
    > [!NOTE]
    > Se você estiver usando o gatilho herdado "quando uma resposta a um alerta da central de segurança do Azure for disparada", seus aplicativos lógicos não serão iniciados pelo recurso de automação do fluxo de trabalho. Em vez disso, use qualquer um dos gatilhos mencionados acima. 

    [![Aplicativo lógico de exemplo](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir seu aplicativo lógico, retorne ao painel de definição de automação de fluxo de trabalho ("Adicionar automação de fluxo de trabalho"). Clique em **Atualizar** para garantir que seu novo aplicativo lógico esteja disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione seu aplicativo lógico e salve a automação. Observe que o menu suspenso do aplicativo lógico mostra apenas os aplicativos lógicos com suporte aos conectores da central de segurança mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Disparar um aplicativo lógico manualmente

Você também pode executar aplicativos lógicos manualmente ao exibir um alerta de segurança ou qualquer recomendação que ofereça [correções de correção rápida](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Para executar manualmente um aplicativo lógico, abra um alerta ou uma recomendação que dê suporte à correção de correção rápida e clique em **disparar aplicativo lógico**:

[![Disparar um aplicativo lógico manualmente](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Esquemas de tipos de dados

Para exibir os esquemas de eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância do aplicativo lógico, visite os [esquemas de tipos de dados de automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas). Isso pode ser útil em casos em que você não está usando conectores de aplicativos lógicos internos da central de segurança mencionados acima, mas, em vez disso, está usando o conector HTTP genérico do aplicativo lógico – você pode usar o esquema JSON de evento para analisá-lo manualmente como desejar.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar aplicativos lógicos, automatizar sua execução na central de segurança e executá-los manualmente. 

Para obter outros materiais relacionados, consulte: 

- [O módulo Microsoft Learn sobre como usar a automação de fluxo de trabalho para automatizar uma resposta de segurança](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
- [Sobre os Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Conectores dos Aplicativos Lógicos](https://docs.microsoft.com/connectors/)
- [Esquemas de tipos de dados de automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas)
