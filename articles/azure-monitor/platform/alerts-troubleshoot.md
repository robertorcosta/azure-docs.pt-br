---
title: Solução de problemas Alertas e notificações do Azure Monitor
description: Problemas comuns com alertas do Azure Monitor e possíveis soluções.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132339"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemas de solução de problemas nos alertas do Monitor Do Azure 

Este artigo discute problemas comuns no Alerta do Azure Monitor.

Os alertas do Azure Monitor notificam-no proativamente quando condições importantes são encontradas em seus dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Para obter mais informações sobre alertas, consulte [Visão geral dos alertas no Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Ação ou notificação no meu alerta não funcionou como esperado

Se você pode ver um alerta de disparo no portal do Azure, mas tiver um problema com algumas de suas ações ou notificações, consulte as seguintes seções.

## <a name="did-not-receive-expected-email"></a>Não recebeu o e-mail esperado

Se você pode ver um alerta de disparo no portal Azure, mas não recebeu o e-mail que você configurou sobre ele, siga estas etapas: 

1. **O e-mail foi suprimido por uma regra de [ação?](alerts-action-rules.md)** 

    Verifique clicando no alerta de disparo no portal e veja a guia histórico para grupos de [ação](action-groups.md)suprimidos : 

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

1. **O tipo de ação é "Papel de gerente de recursos do Mail Azure"?**

    Essa ação só analisa as atribuições de função do Azure Resource Manager que estão no escopo da assinatura e do *usuário*do tipo .  Certifique-se de que você atribuiu a função no nível de assinatura, e não no nível de recurso ou nível de grupo de recursos.

1. **Seu servidor de e-mail e caixa de correio estão aceitando e-mails externos?**

    Verifique se os e-mails desses três endereços não estão bloqueados:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    É comum que listas internas de discussão ou listas de distribuição bloqueiem e-mails de endereços de e-mail externos. Você precisa listar os endereços de e-mail acima.  
    Para testar, adicione um endereço de e-mail de trabalho regular (não uma lista de discussão) ao grupo de ação e veja se os alertas chegam a esse e-mail. 

1. **O e-mail foi processado por regras de caixa de entrada ou um filtro de spam?** 

    Verifique se não há regras de caixa de entrada que excluam esses e-mails ou os movam para uma pasta lateral. Por exemplo, as regras da caixa de entrada podem pegar remetentes específicos ou palavras específicas no assunto.

    Além disso, verifique:
    
      - as configurações de spam do seu cliente de e-mail (como Outlook, Gmail)
      - os limites do remetente / configurações de spam / configurações de quarentena do seu servidor de e-mail (como Exchange, Office 365, G-suite)
      - as configurações do seu aparelho de segurança de e-mail, se houver (como Barracuda, Cisco). 

1. **Você acidentalmente cancelou a assinatura do grupo de ação?** 

    Os e-mails de alerta fornecem um link para cancelar a assinatura do grupo de ação. Para verificar se você acidentalmente cancelou a assinatura deste grupo de ação, também:

    1. Abra o grupo de ação no portal e verifique a coluna Status:

    ![coluna de status do grupo de ação](media/alerts-troubleshoot/action-group-status.png)

    2. Pesquise seu e-mail para obter a confirmação de cancelamento da inscrição:

    ![unsubscribed do grupo de ação de alerta](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Para se inscrever novamente – use o link no e-mail de confirmação de cancelamento de inscrição que você recebeu ou remova o endereço de e-mail do grupo de ação e, em seguida, adicione-o novamente. 
 
1. **Você foi avaliado como limitado devido a muitos e-mails indo para um único endereço de e-mail?** 

    O e-mail [é limitado](alerts-rate-limiting.md) a não mais de 100 e-mails a cada hora para cada endereço de e-mail. Se você passar por esse limite, notificações adicionais de e-mail serão retiradas.  Verifique se você recebeu uma mensagem indicando que seu endereço de e-mail foi temporariamente limitado: 
 
   ![Taxa de e-mail limitada](media/alerts-troubleshoot/email-paused.png)

   Se você quiser receber alto volume de notificações sem limitação de taxas, considere usar uma ação diferente, como webhook, app lógico, função Azure ou runbooks de automação, nenhum dos quais são limitados à taxa. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Não recebeu SMS esperado, chamada de voz ou notificação push

Se você pode ver um alerta de disparo no portal, mas não recebeu o SMS, chamada de voz ou notificação push que você configurou sobre ele, siga estas etapas: 

1. **A ação foi suprimida por uma regra de [ação?](alerts-action-rules.md)** 

    Verifique clicando no alerta de disparo no portal e veja a guia histórico para grupos de [ação](action-groups.md)suprimidos : 

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

   Se isso não foi intencional, você pode modificar, desativar ou excluir a regra de ação.
 
1. **SMS / voz: Seu número de telefone está correto?**

   Verifique a ação de SMS para erros de digitação no código do país ou número de telefone. 
 
1. **SMS / voz: você tem taxa limitada?** 

    SMS e chamadas de voz são limitados a não mais do que uma notificação a cada cinco minutos por número de telefone. Se você passar por esse limite, as notificações serão retiradas. 

      - Chamada de voz – verifique seu histórico de chamadas e veja se você teve uma chamada diferente do Azure nos cinco minutos anteriores. 
      - SMS - verifique o histórico de SMS para obter uma mensagem indicando que seu número de telefone foi limitado. 

    Se você quiser receber alto volume de notificações sem limitação de taxas, considere usar uma ação diferente, como webhook, app lógico, função Azure ou runbooks de automação, nenhum dos quais são limitados à taxa. 
 
1. **SMS: Você acidentalmente cancelou a assinatura do grupo de ação?**

    Abra seu histórico de SMS e verifique se você optou por não entregar SMS a partir deste grupo de ação específico (usando a resposta de action_group_short_name DISABLE) ou de todos os grupos de ação (usando a resposta STOP). Para assinar novamente, envie o comando SMS relevante (ENABLE action_group_short_name ou START) ou remova a ação SMS do grupo de ação e, em seguida, adicione-a novamente.  Para obter mais informações, consulte [o comportamento de alerta de SMS em grupos de ação](alerts-sms-behavior.md).

1. **Você bloqueou acidentalmente as notificações no seu telefone?**

   A maioria dos telefones celulares permite bloquear chamadas ou SMS de números de telefone específicos ou códigos curtos, ou bloquear notificações push de aplicativos específicos (como o aplicativo móvel Azure). Para verificar se você bloqueou acidentalmente as notificações em seu telefone, pesquise a documentação específica para o sistema operacional e modelo do telefone ou teste com um número de telefone e telefone diferente. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Esperava outro tipo de ação para desencadear, mas não 

Se você pode ver um alerta de disparo no portal, mas sua ação configurada não foi acionada, siga estas etapas: 

1. **A ação foi suprimida por uma regra de ação?** 

    Verifique clicando no alerta de disparo no portal e veja a guia histórico para grupos de [ação](action-groups.md)suprimidos : 

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)
 
    Se isso não foi intencional, você pode modificar, desativar ou excluir a regra de ação. 

1. **Um gancho não disparou?**

    1. **Os endereços IP de origem foram bloqueados?**
    
       Whitelist os [endereços IP](action-groups.md#action-specific-information) dos endereços de que o webhook é chamado.

    1. **Seu ponto final do webhook funciona corretamente?**

       Verifique se o ponto final do webhook configurado está correto e o ponto final está funcionando corretamente. Verifique seus logs de webhook ou seu código de instrumentos para que você possa investigar (por exemplo, registre a carga de entrada). 

    1. **Você está ligando para o Slack ou para o Microsoft Teams?**  
    Cada um desses pontos finais espera um formato JSON específico. Siga [estas instruções](action-groups-logic-app.md) para configurar uma ação lógica do aplicativo.

    1. **Seu webhook ficou sem resposta ou retornou erros?** 

        Nosso período de tempo para uma resposta de webhook é de 10 segundos. A chamada webhook será repetida novamente até duas vezes adicionais quando os seguintes códigos de status HTTP forem devolvidos: 408, 429, 503, 504 ou quando o ponto final HTTP não responder. A primeira nova tentativa ocorre após 10 segundos. A segunda e última tentativa acontece após 100 segundos. Se a segunda tentativa falhar, o ponto de extremidade não será chamado novamente por 30 minutos para nenhum grupo de ações.

## <a name="action-or-notification-happened-more-than-once"></a>Ação ou notificação aconteceu mais de uma vez 

Se você recebeu uma notificação para um alerta (como um e-mail ou um SMS) mais de uma vez, ou a ação do alerta (como webhook ou função Azure) foi acionada várias vezes, siga estas etapas: 

1. **É realmente o mesmo alerta?** 

    Em alguns casos, vários alertas semelhantes são disparados ao mesmo tempo. Então, pode parecer que o mesmo alerta desencadeou suas ações várias vezes. Por exemplo, uma regra de alerta de log de atividade pode ser configurada para disparar tanto quando um evento foi iniciado, quanto quando tiver terminado (bem sucedido ou falhado), não filtrando no campo de status do evento. 

    Para verificar se essas ações ou notificações vieram de diferentes alertas, examine os detalhes do alerta, como seu carimbo de data e hora ou o id de alerta ou seu id de correlação. Alternativamente, confira a lista de alertas de disparo no portal. Se esse for o caso, você precisará adaptar a lógica da regra de alerta ou configurar a fonte de alerta. 

1. **A ação se repete em vários grupos de ação?** 

    Quando um alerta é disparado, cada um de seus grupos de ação é processado de forma independente. Assim, se uma ação (como um endereço de e-mail) aparecer em vários grupos de ação acionados, ela será chamada uma vez por grupo de ação. 

    Para verificar quais grupos de ação foram acionados, verifique a guia histórico de alerta. Você veria que ambos os grupos de ação definidos na regra de alerta e grupos de ação adicionados ao alerta pelas regras de ação: 

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Ação ou notificação tem um conteúdo inesperado

Se você recebeu o alerta, mas acredita que alguns de seus campos estão faltando ou incorretos, siga estas etapas: 

1. **Você escolheu o formato correto para a ação?** 

    Cada tipo de ação (e-mail, webhook, etc.) tem dois formatos – o padrão, o formato legado e o [formato de esquema comum mais recente](alerts-common-schema.md). Quando você cria um grupo de ação, você especifica o formato que deseja por ação – diferentes ações nos grupos de ação podem ter formatos diferentes. 

    Por exemplo, para ação de webhook: 

    ![opção de esquema de ação webhook](media/alerts-troubleshoot/webhook.png)

    Verifique se o formato especificado no nível de ação é o que você espera. Por exemplo, você pode ter desenvolvido um código que responde a alertas (webhook, função, aplicativo lógico, etc.), esperando um formato, mas mais tarde na ação você ou outra pessoa especificou um formato diferente.  

    Além disso, verifique o formato de carga (JSON) para [alertas de registro de atividade,](activity-log-alerts-webhook.md)para [alertas](alerts-log-webhook.md) de pesquisa de log (tanto Insights de aplicativo quanto análise de log), para [alertas métricos,](alerts-metric-near-real-time.md#payload-schema)para o [esquema de alerta comum](alerts-common-schema-definitions.md)e para os [alertas métricos clássicos](alerts-webhooks.md)depreciados.

 
1. **Alertas de registro de atividades: As informações estão disponíveis no registro de atividades?** 

    [Os alertas de registro de atividades](activity-log-alerts.md) são alertas baseados em eventos escritos no Azure Activity Log, como eventos sobre criação, atualização ou exclusão de recursos do Azure, eventos de saúde e saúde de serviços ou descobertas do Azure Advisor e da Azure Policy. Se você recebeu um alerta com base no registro de atividades, mas alguns campos que você precisa estão faltando ou incorretos, primeiro verifique os eventos no próprio registro de atividades. Se o recurso Azure não tiver escrito os campos que você está procurando em seu evento de registro de atividades, esses campos não serão incluídos no alerta correspondente. 

## <a name="action-rule-is-not-working-as-expected"></a>A regra de ação não está funcionando como esperado 

Se você pode ver um alerta de disparo no portal, mas uma regra de ação relacionada não funcionou como esperado, siga estas etapas: 

1. **A regra de ação está ativada?** 

    Verifique a coluna de status da regra de ação para verificar se a função de ação relacionada está ativada. 

    ![gráfico](media/alerts-troubleshoot/action-rule-status.png) 

    Se ele não estiver habilitado, você pode habilitar a regra de ação selecionando-a e clicando em Ativar. 

1. **É um alerta de saúde do serviço?** 

    Os alertas de saúde dos serviços (serviço de monitoramento = "Saúde do Serviço") não são afetados pelas regras de ação. 

1. **A regra de ação agiu em seu alerta?** 

    Verifique se a regra de ação processou seu alerta clicando no alerta de disparo no portal e veja a guia histórico.

    Aqui está um exemplo de regra de ação que suprime todos os grupos de ação: 
 
     ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

    Aqui está um exemplo de uma regra de ação adicionando outro grupo de ação:

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **O escopo e o filtro da regra de ação correspondem ao alerta de disparo?** 

    Se você acha que a regra de ação deveria ter disparado, mas não disparou, ou que não deveria ter disparado, mas disparou, examine cuidadosamente o escopo da regra de ação e as condições de filtro versus as propriedades do alerta disparado. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Como encontrar a id de alerta de um alerta de disparo

Ao abrir um caso sobre um alerta específico de disparo (como – se você não recebeu sua notificação por e-mail), você precisará fornecer o id de alerta. 

Para localizá-lo, siga estas etapas:

1. No portal Azure, navegue até a lista de alertas de disparo e encontre esse alerta específico. Você pode usar os filtros para ajudá-lo a localizá-lo. 

1. Clique no alerta para abrir os detalhes do alerta. 

1. Role para baixo nos campos de alerta da primeira guia (a guia resumo) até localizá-la e copie-a. Esse campo também inclui um botão auxiliar "Copiar para prancheta" que você pode usar.  

    ![encontrar id alerta](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problemas na criação, atualização ou exclusão de regras de ação no portal Azure

Se você recebeu um erro ao tentar criar, atualizar ou excluir uma [regra de ação,](alerts-action-rules.md)siga estas etapas: 

1. **Você recebeu um erro de permissão?**  

    Você deve ter a [função de contribuinte de monitoramento embutida](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)ou as permissões específicas relacionadas às regras de ação e alertas.

1. **Você verificou os parâmetros da regra de ação?**  

    Verifique a [documentação](alerts-action-rules.md)da regra de ação ou a [regra de ação powerShell set-AzActionRule.](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 


## <a name="next-steps"></a>Próximas etapas
- Se estiver usando um alerta de log, consulte também [alertas de log de solução de problemas](alert-log-troubleshoot.md).
- Volte ao [portal Azure](https://portal.azure.com) para verificar se você resolveu seu problema com orientação acima 
