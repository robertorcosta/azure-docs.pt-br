---
title: Definir alertas no Aplicativo Azure insights
description: Seja notificado sobre os tempos de resposta lentos, as exceções e outras alterações de desempenho ou de uso em seu aplicativo Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666710"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights

[Aplicativo Azure insights][start] pode alertá-lo sobre alterações nas métricas de desempenho ou de uso em seu aplicativo Web. 

O Application Insights monitora seu aplicativo em tempo real em uma [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e entender os padrões de uso.

Há vários tipos de alertas:

* Os [**alertas de métrica**](../../azure-monitor/platform/alerts-metric-overview.md) informam quando uma métrica cruza um valor de limite para algum período, como tempos de resposta, contagens de exceção, uso da CPU ou exibições de página.
* Os [**alertas de log**](../../azure-monitor/platform/alerts-unified-log.md) são usados para descrever alertas em que o sinal de alerta se baseia em uma consulta Kusto personalizada.
* Os [**testes da Web**][availability] informam quando seu site está indisponível na Internet ou está respondendo lentamente. [Saiba mais][availability].
* Os [**diagnósticos proativos**](../../azure-monitor/app/proactive-diagnostics.md) são configurados automaticamente para notificar você sobre padrões de desempenho incomuns.

## <a name="set-a-metric-alert"></a>Definir um alerta de Métrica

Abra a guia regras de alerta e, em seguida, use o botão Adicionar.

![Na guia regras de alerta, escolha Adicionar alerta. Defina seu aplicativo como o recurso a ser medido, forneça um nome para o alerta e escolha uma métrica.](./media/alerts/01-set-metric.png)

* Defina o recurso antes de outras propriedades. **Escolha o recurso "(componentes)"** se desejar definir alertas em métricas de desempenho ou de uso.
* O nome dado ao alerta deve ser exclusivo dentro do grupo de recursos (não apenas no seu aplicativo).
* Observe as unidades quando você for solicitado para inserir o valor de limite.
* Se você marcar a caixa “Proprietários de email...”, os alertas serão enviados por email para qualquer pessoa que tenha acesso a esse grupo de recursos. Para expandir esse grupo de pessoas, adicione-as à [assinatura ou grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md) (não o recurso).
* Se você especificar “Emails adicionais”, os alertas serão enviados aos indivíduos ou grupos (sem levar em conta se a caixa “proprietários de email...” foi marcada ou não). 
* Defina um [endereço de webhook](../../azure-monitor/platform/alerts-webhooks.md) se tiver configurado um aplicativo Web que responda aos alertas. Ele é chamado quando o alerta é ativado e quando ele está resolvido. (Mas observe que, no momento, os parâmetros de consulta não são passados como propriedades de webhook)
* Você pode desabilitar ou habilitar o alerta: consulte os botões na parte superior.

*Não vejo o botão Adicionar Alerta.*

* Você está usando uma conta organizacional? Você poderá definir alertas se tiver acesso de proprietário ou colaborador a esse recurso de aplicativo. Dê uma olhada na guia controle de acesso. [saiba mais sobre o controle de acesso][roles].

> [!NOTE]
> Na folha de alertas, você vê que já existe uma configuração de alerta: [Proactive Diagnostics](../../azure-monitor/app/proactive-failure-diagnostics.md). O alerta automático monitora uma métrica específica, taxa de falha de solicitação. A menos que você opte por desabilitar o alerta proativo, não precisa definir seu próprio alerta na taxa de falha de solicitação.
> 
> 

## <a name="see-your-alerts"></a>Ver seus alertas
Você recebe um email quando o estado de um alerta é mudado de inativo para ativo. 

O estado atual de cada alerta é mostrado na guia regras de alerta.

Há um resumo da atividade recente no menu suspenso de alertas:

![Lista suspensa Alertas](./media/alerts/010-alert-drop.png)

O histórico das alterações de estado está no Log de Atividades:

![Na guia Visão geral, clique em configurações, logs de auditoria](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como os alertas funcionam
* Um alerta tem três estados: “Nunca ativado”, “Ativado” e “Resolvido”. Ativado significa que a condição especificada era true, quando ela foi avaliada pela última vez.
* Uma notificação é gerada quando um alerta muda de estado. (Se a condição do alerta já era true quando o alerta foi criado, talvez você não receba uma notificação até que a condição mude para false.)
* Cada notificação gerará um email caso tenha marcado a caixa de emails ou fornecido endereços de email. Também é possível examinar a lista suspensa Notificações.
* Um alerta é avaliado toda vez que uma métrica chega, mas não o contrário.
* A avaliação agrega a métrica ao longo do período anterior e a compara com o limite para determinar o novo estado.
* O período que você escolhe especifica o intervalo durante o qual as métricas são agregadas. Ele não afeta a frequência com que o alerta é avaliado: isto depende da frequência da chegada das métricas.
* Se nenhum dado chegar para uma determinada métrica por algum tempo, a lacuna tem efeitos diferentes sobre a avaliação do alerta e sobre os gráficos no Metrics Explorer. No Metrics Explorer, se nenhum dado for visto por mais tempo do que o intervalo de amostragem do gráfico, o gráfico mostrará um valor 0. Mas um alerta com base na mesma métrica não é avaliado novamente e o estado do alerta permanece inalterado. 
  
    Quando os dados chegam, o gráfico volta para um valor diferente de zero. O alerta é avaliado com base nos dados disponíveis para o período especificado. Se o novo ponto de dados for o único disponível no período, a agregação se baseará apenas nisso.
* Um alerta pode piscar frequentemente entre os estados de alerta e íntegro, mesmo que você defina um longo período. Isso pode acontecer se o valor da métrica estiver em torno do limite. Não há nenhuma histerese no limite: a transição para o alerta acontece com o mesmo valor que a transição para o estado íntegro.

## <a name="what-are-good-alerts-to-set"></a>Quais são alguns alertas que é recomendável definir?
Depende de seu aplicativo. Para começar, é melhor não definir um número excessivo de métricas. Passe algum tempo examinando seus gráficos de métricas enquanto seu aplicativo está em execução para ter uma noção de como ele se comporta normalmente. Esta prática ajuda a encontrar maneiras de melhorar o desempenho. Em seguida, configure alertas para informá-lo quando as métricas estiverem fora da zona normal. 

Alguns alertas populares são:

* As [métricas do navegador][client], especialmente **tempos de carregamento de página**do navegador, são boas para aplicativos Web. Se a página tem muitos scripts, você deve procurar **exceções de navegador**. Para obter essas métricas e alertas, você precisa configurar o [monitoramento de página da Web][client].
* **Tempo de resposta do servidor** para o lado do servidor de aplicativos Web. Além de configurar alertas, confira essa métrica para ver se ela varia de forma desproporcional com altas taxas de solicitação: a variação pode indicar que seu aplicativo está ficando sem recursos. 
* **Exceções de servidor** - para vê-las, você precisa fazer algumas [configurações adicionais](../../azure-monitor/app/asp-net-exceptions.md).

Não se esqueça de que [diagnósticos de taxa de falha proativos](../../azure-monitor/app/proactive-failure-diagnostics.md) monitoram automaticamente a taxa em que seu aplicativo responde às solicitações com códigos de falha.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Como definir um alerta de exceção usando a pesquisa de logs personalizada

Nesta seção, vamos examinar como definir um alerta de exceção baseada em consulta. Para este exemplo, digamos que desejamos um alerta quando a taxa de falha for maior que 10% nas últimas 24 horas.

1. Vá para o recurso do Application Insight no portal do Azure.
2. À esquerda, em configurar, clique em **alerta**.

    ![À esquerda, em configurar clique em alerta](./media/alerts/1appinsightalert.png)

3. Na parte superior da guia alerta, selecione **nova regra de alerta**.

     ![Na parte superior da guia alerta, clique em nova regra de alerta](./media/alerts/2createalert.png)

4. O recurso deve ser selecionado automaticamente. Para definir uma condição, clique em **Adicionar condição**.

    ![Clique em Adicionar condição](./media/alerts/3addcondition.png)

5. Na guia Configurar lógica de sinal, selecione **pesquisa de logs personalizada**

    ![Clique em pesquisa de logs personalizada](./media/alerts/4customlogsearch.png)

6. Na guia pesquisa de logs personalizada, insira sua consulta na caixa "consulta de pesquisa". Para este exemplo, usaremos a consulta Kusto abaixo.
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

    ![Consulta de tipo na caixa de consulta de pesquisa](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Você também pode aplicar essas etapas a outros tipos de alertas baseados em consulta. Você pode saber mais sobre a linguagem de consulta do Kusto deste [documento de introdução do Kusto](https://docs.microsoft.com/azure/kusto/concepts/) ou desta página de dicas de [SQL para Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Em "lógica de alerta", escolha se ele é baseado no número de resultados ou medição de métrica. Em seguida, escolha a condição (maior que, igual a, menor que) e um limite. Enquanto você estiver alterando esses valores, você poderá observar as alterações da sentença de visualização da condição. Neste exemplo, estamos usando "igual a".

    ![Em lógica de alerta, escolha entre as opções fornecidas para com base na condição e, em seguida, digite um limite](./media/alerts/6alertlogic.png)

8. Em "avaliado com base em", defina o período e a frequência. O ponto aqui deve corresponder ao valor que colocamos para o período na consulta acima. Em seguida, clique em **concluído**.

    ![Defina período e frequência na parte inferior e clique em concluído](./media/alerts/7evaluate.png)

9. Agora vemos a condição que criamos com o custo mensal estimado. Abaixo de ["grupos de ação"](../platform/action-groups.md) , você pode criar um novo grupo ou selecionar um existente. Se desejar, você poderá personalizar as ações.

    ![Clique nos botões selecionar ou criar em grupo de ações](./media/alerts/8actiongroup.png)

10. Finalmente, adicione os detalhes do alerta (nome da regra de alerta, descrição, severidade). Quando terminar, clique em **criar regra de alerta** na parte inferior.

    ![Em detalhe do alerta, digite o nome da regra de alerta, escreva uma descrição e escolha uma severidade](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Como cancelar a assinatura de notificações de email de alerta clássico

Esta seção se aplica a **alertas de disponibilidade clássicos**, **alertas de métrica de Application insights clássica**e a **alertas de anomalias de falha clássica**.

Você receberá notificações por email para esses alertas clássicos se uma das seguintes opções for aplicável:

* Seu endereço de email está listado no campo destinatários do email de notificação nas configurações da regra de alerta.

* A opção de enviar notificações por email a usuários que mantêm determinadas funções para a assinatura é ativada e você mantém uma função respectiva para essa assinatura específica do Azure.

![Captura de tela de notificação de alerta](./media/alerts/alert-notification.png)

Para controlar melhor sua segurança e privacidade, geralmente é recomendável especificar explicitamente os destinatários da notificação para seus alertas clássicos no campo **destinatários do email de notificação** . A opção de notificar todos os usuários que contêm determinadas funções é fornecida para compatibilidade com versões anteriores.

Para cancelar a assinatura de notificações por email geradas por uma determinada regra de alerta, remova seu endereço de email do campo **destinatários do email de notificação** .

Se seu endereço de email não estiver listado explicitamente, é recomendável desabilitar a opção para notificar todos os membros de determinadas funções automaticamente e, em vez disso, listar todos os emails do usuário que precisam receber notificações para essa regra de alerta no email de notificação campo de destinatários.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para entender mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, consulte o [artigo Visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos.

* Para alertas sobre métrica do Application Insights (incluindo métrica de disponibilidade), a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações.

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real caso precise notificar usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários que tenham qualquer uma das funções de proprietário/colaborador/leitor (não combinadas como uma única opção).

## <a name="automation"></a>Automação
* [Usar o PowerShell para automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Usar webhooks para automatizar a resposta a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Consulte também
* [Testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnóstico proativo](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

