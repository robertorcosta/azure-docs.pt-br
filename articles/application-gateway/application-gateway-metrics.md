---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: a8882a810d18d06b33d6382bd8bd86ffe75b39d8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766797"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

O gateway de aplicativo publica pontos de dados, chamados métricas, para [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu gateway de aplicativo e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [Métricas do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

O gateway de aplicativo fornece várias métricas de tempo internas relacionadas à solicitação e à resposta, que são todas medidas em milissegundos. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no gateway de aplicativo, sempre filtre pela dimensão do *ouvinte* , comparando métricas de latência diferentes para obter uma inferência significativa.

- **Tempo de conexão de back-end**

  Tempo gasto estabelecendo uma conexão com o aplicativo back-end. 

  Isso inclui a latência de rede, bem como o tempo gasto pela pilha TCP do servidor de back-end para estabelecer novas conexões. No caso do SSL, ele também inclui o tempo gasto no handshake. 

- **Tempo de resposta do primeiro byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do primeiro byte do cabeçalho de resposta. 

  Isso aproxima a soma do *tempo de conexão de back-end*, o tempo gasto pela solicitação para alcançar o back-end do gateway de aplicativo, o tempo gasto pelo aplicativo de back-end para responder (o tempo que o servidor levou para gerar conteúdo, potencialmente buscar consultas de banco de dados) e o tempo gasto pelo primeiro byte da resposta para alcançar o gateway de aplicativo do back-end.

- **Tempo de resposta do último byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta. 

  Isso aproxima a soma do *tempo de resposta do primeiro back-end* e o tempo de transferência de dados (esse número pode variar muito dependendo do tamanho dos objetos solicitados e da latência da rede do servidor).

- **Tempo total do gateway de aplicativo**

  Tempo médio que leva para que uma solicitação seja recebida, processada e sua resposta seja enviada. 

  Esse é o intervalo a partir do momento em que o gateway de aplicativo recebe o primeiro byte da solicitação HTTP até a hora em que o último byte de resposta foi enviado ao cliente. Isso inclui o tempo de processamento usado pelo gateway de aplicativo, o *tempo de resposta do último byte de back-end*, o tempo gasto pelo gateway de aplicativo para enviar toda a resposta e o *RTT do cliente*.

- **RTT do cliente**

  Tempo médio de ida e volta entre clientes e o gateway de aplicativo.



Essas métricas podem ser usadas para determinar se a lentidão observada é devido à rede do cliente, ao desempenho do gateway de aplicativo, à rede de back-end e à saturação de pilha TCP do servidor de back-end, ao desempenho do aplicativo de backend ou ao tamanho de arquivo grande.

Por exemplo, se houver um pico na tendência de *tempo de resposta do primeiro byte de back-end* , mas a tendência de *tempo de conexão de back-end* for estável, poderá ser inferida que o gateway de aplicativo para latência de back-end e o tempo necessário para estabelecer a conexão é estável e o pico é causado devido a um aumento no tempo de resposta do aplicativo back-end. Por outro lado, se o tempo de resposta do pico no *primeiro byte de back-end* estiver associado a um pico correspondente no *tempo de conexão de back-end*, ele poderá ser deduzido que a rede entre o gateway de aplicativo e o servidor de back-end ou a pilha TCP do servidor de back-end está saturada. 

Se você notar um pico no *tempo de resposta do último byte de back-end* , mas o *tempo de resposta do primeiro byte de back-end* for estável, ele poderá ser deduzido de que o pico é devido a um arquivo maior sendo solicitado.

Da mesma forma, se o *tempo total do gateway de aplicativo* tiver um pico, mas o *tempo de resposta do último byte do back-end* for estável, ele poderá ser um sinal de afunilamento de desempenho no gateway de aplicativo ou um afunilamento na rede entre o cliente e o gateway de aplicativo. Além disso, se o *RTT do cliente* também tiver um pico correspondente, isso indica que a degradação é devido à rede entre o cliente e o gateway de aplicativo.

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo gateway de aplicativo dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pelo gateway de aplicativo para os clientes

- **Protocolo TLS do cliente**

   Contagem de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas. As unidades de capacidade medem o custo com base no consumo que é cobrado além do custo fixo. Há três determinantes para unidade de computação de unidade de capacidade, conexões persistentes e taxa de transferência. Cada unidade de capacidade é composta de no máximo: 1 unidade de computação ou 2500 conexões persistentes ou taxa de transferência de 2,22 Mbps.

- **Unidades de computação atuais**

   Contagem de capacidade de processador consumida. Fatores que afetam a unidade de computação são conexões TLS/s, computações de regravação de URL e processamento de regra WAF. 

- **Conexões atuais**

   Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

   Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.


- **Status da resposta**

   Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

   Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

   Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Status da resposta de back-end**

  Contagem de códigos de status de resposta HTTP retornados pelos back-ends. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts não íntegros em um pool de back-end específico.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Utilização da CPU**

  Exibe a utilização das CPUs alocadas para o gateway de aplicativo.  Em condições normais, o uso da CPU não deve exceder regularmente 90%, pois isso pode causar latência nos sites hospedados atrás do gateway de aplicativo e interromper a experiência do cliente. Você pode controlar indiretamente ou melhorar a utilização da CPU modificando a configuração do gateway de aplicativo aumentando a contagem de instâncias ou movendo para um tamanho de SKU maior ou fazendo ambos.

- **Conexões atuais**

  Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

  Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Status da resposta**

  Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

  Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

  Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts não íntegros em um pool de back-end específico.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue até um gateway de aplicativo, em **monitoramento** selecione **métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

Na imagem a seguir, você pode ver um exemplo com três métricas exibidas para os últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [Métricas com suporte no Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta poderá chamar um webhook ou enviar um email para um administrador se a vazão de dados do gateway de aplicativo estiver acima, abaixo ou no limite durante um período especificado.

O seguinte exemplo orientará você pela criação de uma regra de alerta que envia um email para um administrador após um limite de vazão de dados ter sido violado:

1. Selecione **adicionar alerta de métrica** para abrir a página **Adicionar regra** . Você também pode acessar essa página na página métricas.

   ![Botão “Adicionar alerta de métrica”][6]

2. Na página **Adicionar regra** , preencha as seções nome, condição e notificar e selecione **OK**.

   * No seletor **Condição**, selecione um dos quatro valores: **Maior que**, **Maior ou igual a**, **Menor que** ou **Menor ou igual a**.

   * No seletor **Período**, selecione um período de cinco minutos a seis horas.

   * Se você selecionar **Proprietários, colaboradores e leitores de email**, o email poderá ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, você poderá fornecer uma lista separada por vírgula de usuários na caixa **Emails de administrador adicionais**.

   ![Página Adicionar regra][7]

Se o limite for violado, um email semelhante ao mostrado na seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida após a criação de um alerta de métrica. Ela fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para entender mais sobre webhooks e como eles podem ser usados com alertas, consulte [Configurar um webhook em um alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Próximos passos

* Visualize o contador e os logs de eventos com os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* Postagem no blog [Visualize your Azure Activity Log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar o log de atividades do Azure com o Power BI).
* Postagem no blog [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Exibir e analisar os Logs de Atividades do Azure no Power BI e muito mais).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
