---
title: Definir alertas no Azure Application Insights
description: Seja notificado sobre os tempos de resposta lentos, as exceções e outras alterações de desempenho ou de uso em seu aplicativo Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295091"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights

O [Azure Application Insights][start] pode alertá-lo sobre as alterações nas métricas de desempenho ou de uso do aplicativo Web. 

O Application Insights monitora o aplicativo ativo em um [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e compreender os padrões de uso.

Existem vários tipos de alertas:

* [**Os alertas métricos**](../../azure-monitor/platform/alerts-metric-overview.md) dizem quando uma métrica cruza um valor de limite por algum período - como tempos de resposta, contagem de exceções, uso da CPU ou visualizações de página.
* [**O Log Alerts**](../../azure-monitor/platform/alerts-unified-log.md) é usado para descrever alertas onde o sinal de alerta é baseado em uma consulta Kusto personalizada.
* [**Testes na**][availability] Web dizem quando seu site não está disponível na internet ou respondendo lentamente. [Saiba mais][availability].
* Os [**diagnósticos proativos**](../../azure-monitor/app/proactive-diagnostics.md) são configurados automaticamente para notificar você sobre padrões de desempenho incomuns.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Como definir um alerta de exceção usando pesquisa de log personalizado

Nesta seção, vamos passar por como definir um alerta de exceção baseado em consulta. Para este exemplo, digamos que queremos um alerta quando a taxa de falha for superior a 10% nas últimas 24 horas.

1. Vá para o recurso Application Insight no portal Azure.
2. À esquerda, em configure clique em **Alerta**.

    ![À esquerda, em configurar alerta de clique](./media/alerts/1appinsightalert.png)

3. Na parte superior da guia de alerta selecione **Nova regra de alerta**.

     ![Na parte superior da guia de alerta clique em nova regra de alerta](./media/alerts/2createalert.png)

4. Seu recurso deve ser selecionado automaticamente. Para definir uma condição, clique **em Adicionar condição**.

    ![Clique em adicionar condição](./media/alerts/3addcondition.png)

5. Na guia configurar lógica de sinal selecione **Pesquisa de log personalizado**

    ![Clique em pesquisa de log personalizado](./media/alerts/4customlogsearch.png)

6. Na guia de pesquisa de log personalizado, digite sua consulta na caixa "Pesquisa consulta". Para este exemplo, usaremos a consulta kusto abaixo.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Tipo consulta na caixa de consulta de pesquisa](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Você também pode aplicar essas etapas a outros tipos de alertas baseados em consultas. Você pode aprender mais sobre a linguagem de consulta kusto a partir deste [Kusto começando doc](https://docs.microsoft.com/azure/kusto/concepts/) ou este [SQL para Kusto cheat sheet](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Em "Lógica de alerta", escolha se é baseado no número de resultados ou medição métrica. Em seguida, escolha a condição (maior do que, igual a, menos de) e um limiar. Enquanto você estiver alterando esses valores, você pode notar a condição de visualizar alterações de sentença. Neste exemplo estamos usando "igual a".

    ![Em alerta, escolha entre as opções fornecidas com base e condição, em seguida, digite um limite](./media/alerts/6alertlogic.png)

8. Em "Avaliado com base", defina o período e a frequência. O período aqui deve corresponder ao valor que colocamos por período na consulta acima. Em seguida, clique **feito**.

    ![Definir período e freqüência na parte inferior e, em seguida, clique feito](./media/alerts/7evaluate.png)

9. Agora vemos a condição que criamos com o custo mensal estimado. Abaixo, em ["Grupos de ação",](../platform/action-groups.md) você pode criar um novo grupo ou selecionar um já existente. Se você quiser, você pode personalizar as ações.

    ![clique na seleção ou criar botões em grupo de ação](./media/alerts/8actiongroup.png)

10. Por fim, adicione seus detalhes de alerta (nome da regra de alerta, descrição, gravidade). Quando terminar, clique em **Criar regra de alerta** na parte inferior.

    ![Em detalhes de alerta digite seu nome de regra de alerta, escreva uma descrição e escolha uma gravidade](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Como cancelar a inscrição a partir de notificações clássicas de alerta

Esta seção se aplica a **alertas clássicos**de disponibilidade, **alertas métricos clássicos do Application Insights**e a **alertas clássicos de anomalias de falha.**

Você está recebendo notificações de e-mail para esses alertas clássicos se algum dos seguintes se aplica:

* Seu endereço de e-mail está listado no campo 'Destinatários de e-mails' nas configurações de regra de alerta.

* A opção de enviar notificações de e-mail aos usuários que ocupam determinadas funções para a assinatura é ativada, e você mantém uma respectiva função para essa assinatura específica do Azure.

![Captura de tela de notificação de alerta](./media/alerts/alert-notification.png)

Para controlar melhor sua segurança e privacidade, geralmente recomendamos que você especifique explicitamente os destinatários de notificação para seus alertas clássicos no campo **Destinatários de e-mail de Notificação.** A opção de notificar todos os usuários que detêm determinadas funções é fornecida para compatibilidade retrógrada.

Para cancelar a inscrição das notificações de e-mail geradas por uma determinada regra de alerta, remova seu endereço de e-mail do campo **Destinatários de e-mail de Notificação.**

Se o seu endereço de e-mail não estiver listado explicitamente, recomendamos que você desative a opção de notificar todos os membros de determinadas funções automaticamente e, em vez disso, liste todos os e-mails do usuário que precisam receber notificações para essa regra de alerta no e-mail de notificação campo destinatários.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para entender mais sobre a diferença entre [os alertas clássicos](../platform/alerts-classic.overview.md) e a experiência dos novos alertas, consulte o artigo de visão geral dos [alertas.](../platform/alerts-overview.md) Para controlar a notificação de alerta na experiência dos novos alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos.

* Para alertas sobre métrica do Application Insights (incluindo métrica de disponibilidade), a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações.

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real caso precise notificar usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários que tenham qualquer uma das funções de proprietário/colaborador/leitor (não combinadas como uma única opção).

## <a name="automation"></a>Automação
* [Usar o PowerShell para automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Usar webhooks para automatizar a resposta a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Confira também
* [Testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnóstico proativo](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

