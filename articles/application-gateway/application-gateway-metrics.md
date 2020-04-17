---
title: Métricas do Monitor do Azure para gateway de aplicativos
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativos
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457868"
---
# <a name="metrics-for-application-gateway"></a>Métricas para gateway de aplicativo

O Application Gateway publica pontos de dados, chamados métricas, no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do gateway de aplicativos e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de séries tempois que descrevem algum aspecto do gateway do aplicativo em um determinado momento. Se houver solicitações fluindo através do Gateway de aplicativo, ele mede e envia suas métricas em intervalos de 60 segundos. Se não houver solicitações fluindo através do Gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [as métricas do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas suportadas pelo Application Gateway V2 SKU

### <a name="timing-metrics"></a>Métricas de tempo

O Application Gateway fornece várias métricas de tempo incorporadas relacionadas à solicitação e resposta, todas medidas em milissegundos. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no Gateway de aplicativo, então sempre filtre pela dimensão *Ouvinte,* comparando diferentes métricas de latência para obter uma inferência significativa.

- **Tempo de conexão backend**

  Tempo gasto estabelecendo uma conexão com o aplicativo backend. 

  Isso inclui a latência da rede, bem como o tempo demorado pela pilha TCP do servidor backend para estabelecer novas conexões. No caso do TLS, ele também inclui o tempo gasto em aperto de mão. 

- **Tempo de resposta do backend primeiro byte**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor backend e o recebimento do primeiro byte do cabeçalho de resposta. 

  Isso aproxima a soma do tempo de conexão do *Backend,* o tempo levado pela solicitação para chegar ao backend do Application Gateway, o tempo levado pelo aplicativo backend para responder (o tempo que o servidor levou para gerar conteúdo, potencialmente buscar consultas de banco de dados) e o tempo demorado pelo primeiro byte da resposta para chegar ao Gateway de aplicativo a partir do backend.

- **Tempo de resposta do backend passado byte**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor backend e o recebimento do último byte do corpo de resposta. 

  Isso aproxima a soma do tempo de resposta do *backend primeiro byte* e do tempo de transferência de dados (esse número pode variar muito dependendo do tamanho dos objetos solicitados e da latência da rede do servidor).

- **Tempo total do gateway do aplicativo**

  Tempo médio que leva para que uma solicitação seja recebida, processada e sua resposta seja enviada. 

  Este é o intervalo do momento em que o Application Gateway recebe o primeiro byte da solicitação HTTP até o momento em que o último byte de resposta foi enviado ao cliente. Isso inclui o tempo de processamento tomado pelo Application Gateway, o tempo de resposta do *backend last byte,* o tempo tomado pelo Application Gateway para enviar toda a resposta e o *Client RTT*.

- **Cliente RTT**

  Tempo médio de ida e volta entre clientes e Gateway de aplicativo.



Essas métricas podem ser usadas para determinar se a desaceleração observada é devido à rede do cliente, ao desempenho do Application Gateway, à rede backend e à saturação da pilha tCP do servidor backend, ao desempenho do aplicativo back-end ou ao grande tamanho do arquivo.

Por exemplo, se houver um pico na tendência de tempo de *resposta do Backend first byte,* mas a tendência de tempo de *conexão do Backend* é estável, então pode-se inferir que o gateway de aplicativo para latência backend e o tempo necessário para estabelecer a conexão é estável, e o pico é causado devido a um aumento no tempo de resposta do aplicativo backend. Por outro lado, se o pico no *tempo de resposta do backend primeiro byte* estiver associado a um pico correspondente no tempo de *conexão do Backend,* então pode ser deduzido que a rede entre o Application Gateway e o servidor backend ou a pilha tCP do servidor backend esteja saturada. 

Se você notar um pico no tempo de *resposta do backend lastbyte,* mas o *tempo de resposta do backend primeiro byte* é estável, então pode ser deduzido que o pico é por causa de um arquivo maior sendo solicitado.

Da mesma forma, se o tempo total do *gateway do aplicativo* tiver um pico, mas o tempo de resposta do *backend de último byte* for estável, então ele pode ser um sinal de gargalo de desempenho no Gateway de aplicativo ou um gargalo na rede entre o cliente e o Gateway de aplicativo. Além disso, se o *CLIENTE RTT* também tiver um pico correspondente, então ele indica que a degradação é devido à rede entre cliente e Gateway de aplicativo.

### <a name="application-gateway-metrics"></a>Métricas do Gateway de aplicativos

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo Gateway de aplicativo dos clientes

- **Bytes enviado**

   Contagem de bytes enviados pelo Gateway de aplicativo para os clientes

- **Protocolo TLS do cliente**

   Contagem de solicitações TLS e não-TLS iniciadas pelo cliente que estabeleceu conexão com o Gateway de aplicativo. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão Protocolo TLS.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas para carregar equilibram o tráfego. Existem três determinantes para unidade de capacidade - unidade de computação, conexões persistentes e throughput. Cada unidade de capacidade é composta de no máximo: 1 unidade de computação, ou 2500 conexões persistentes, ou throughput de 2,22 Mbps.

- **Unidades de computação atuais**

   Contagem da capacidade do processador consumida. Os fatores que afetam a unidade de computação são conexões TLS/seg, computação de regravação de URL e processamento de regras WAF. 

- **Conexões atuais**

   O número total de conexões simultâneas ativas dos clientes para o Gateway de aplicativos
   
- **Unidades estimadas de Capacidade Faturada**

  Com o v2 SKU, o modelo de preços é impulsionado pelo consumo. As unidades de capacidade medem o custo baseado no consumo que é cobrado além do custo fixo. *As unidades estimadas de Capacidade Faturada* indicam o número de unidades de capacidade utilizando-se o faturamento. Isso é calculado como o maior valor entre *as unidades de capacidade atual* (unidades de capacidade necessárias para carregar o equilíbrio do tráfego) e unidades de capacidade de cobrança *fixa* (unidades de capacidade mínima mantidas provisionadas).

- **Solicitações com falha**

  Número de solicitações que o Application Gateway serviu com códigos de erro do servidor 5xx. Isso inclui os códigos 5xx gerados a partir do Gateway de aplicativo, bem como os códigos 5xx gerados a partir do backend. A contagem de solicitações pode ser filtrada ainda mais para mostrar a contagem por cada/combinação específica de configuração de pool-http de backend.
   
- **Unidades de capacidade de faturamento fixas**

  O número mínimo de unidades de capacidade mantidas provisionadas de acordo com a configuração *de unidades de escala mínima* (uma instância se traduz em 10 unidades de capacidade) na configuração do Application Gateway.
   
 - **Novas conexões por segundo**

   O número médio de novas conexões TCP por segundo estabelecidas dos clientes para o Gateway de aplicativo e do Gateway de aplicativo para os membros back-end.


- **Status da Resposta**

   Status de resposta HTTP retornado pelo Application Gateway. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

   Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

   Contagem de solicitações bem-sucedidas que o Application Gateway atendeu. A contagem de solicitações pode ser filtrada ainda mais para mostrar a contagem por cada/combinação específica de configuração de pool-http de backend.

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Status de resposta backend**

  Contagem de códigos de status de resposta HTTP retornados pelos backends. Isso não inclui nenhum código de resposta gerado pelo Gateway do aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de hosts íntegros**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar em uma base de pool por backend para mostrar o número de hosts saudáveis em uma piscina de backend específica.

- **Contagem de hosts não íntegros**

  O número de backends que são determinados insalubres pela sonda de saúde. Você pode filtrar em uma base de pool por backend para mostrar o número de hosts insalubres em um pool de backend específico.
  
- **Solicitações por minuto por Host Saudável**

  O número médio de solicitações recebidas por cada membro saudável em uma piscina de backend em um minuto. Você deve especificar o pool de backend usando a dimensão *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas suportadas pelo Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Métricas do Gateway de aplicativos

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Utilização da CPU**

  Exibe a utilização das CPUs alocadas no Gateway de aplicativo.  Em condições normais, o uso da CPU não deve exceder regularmente 90%, pois isso pode causar latência nos sites hospedados atrás do Gateway de aplicativo e interromper a experiência do cliente. Você pode controlar ou melhorar indiretamente a utilização da CPU modificando a configuração do Gateway de aplicativo aumentando a contagem de ocorrências ou movendo-se para um tamanho SKU maior ou fazendo ambos.

- **Conexões atuais**

  Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

  Número de solicitações que o Application Gateway serviu com códigos de erro do servidor 5xx. Isso inclui os códigos 5xx gerados a partir do Gateway de aplicativo, bem como os códigos 5xx gerados a partir do backend. A contagem de solicitações pode ser filtrada ainda mais para mostrar a contagem por cada/combinação específica de configuração de pool-http de backend.

- **Status da Resposta**

  Status de resposta HTTP retornado pelo Application Gateway. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

  Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

  Contagem de solicitações bem-sucedidas que o Application Gateway atendeu. A contagem de solicitações pode ser filtrada ainda mais para mostrar a contagem por cada/combinação específica de configuração de pool-http de backend.

- **Contagem de solicitações bloqueadas do Firewall de Aplicativos da Web**
- **Distribuição de solicitações bloqueadas do Firewall de Aplicativos da Web**
- **Distribuição total de regras do Firewall de Aplicativos da Web**

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Contagem de hosts íntegros**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar em uma base de pool por backend para mostrar o número de hosts saudáveis em uma piscina de backend específica.

- **Contagem de hosts não íntegros**

  O número de backends que são determinados insalubres pela sonda de saúde. Você pode filtrar em uma base de pool por backend para mostrar o número de hosts insalubres em um pool de backend específico.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue até um gateway de aplicativo, em **Monitorando** selecionar **Métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

Na imagem a seguir, você pode ver um exemplo com três métricas exibidas para os últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [Métricas com suporte no Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta em métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta poderá chamar um webhook ou enviar um email para um administrador se a vazão de dados do gateway de aplicativo estiver acima, abaixo ou no limite durante um período especificado.

O seguinte exemplo orientará você pela criação de uma regra de alerta que envia um email para um administrador após um limite de vazão de dados ter sido violado:

1. selecione **Adicionar alerta métrico** para abrir a página Adicionar **regra.** Você também pode acessar esta página a partir da página métricas.

   ![Botão “Adicionar alerta de métrica”][6]

2. Na **página Adicionar regra,** preencha o nome, a condição e notifique as seções e selecione **OK**.

   * No seletor **Condição**, selecione um dos quatro valores: **Maior que**, **Maior ou igual a**, **Menor que** ou **Menor ou igual a**.

   * No seletor **Período**, selecione um período de cinco minutos a seis horas.

   * Se você selecionar **Proprietários, colaboradores e leitores de email**, o email poderá ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, você poderá fornecer uma lista separada por vírgula de usuários na caixa **Emails de administrador adicionais**.

   ![Adicionar página de regra][7]

Se o limite for violado, um email semelhante ao mostrado na seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida após a criação de um alerta de métrica. Ela fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para entender mais sobre webhooks e como eles podem ser usados com alertas, consulte [Configurar um webhook em um alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Próximas etapas

* Visualize o contador e os logs de eventos com os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize seu registro de atividades do Azure com a](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) publicação do blog Power BI.
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
