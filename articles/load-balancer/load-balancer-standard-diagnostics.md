---
title: Diagnóstico com métricas, alertas e saúde de recursos - Azure Standard Load Balancer
description: Use as métricas, alertas e informações de saúde disponíveis para diagnosticar o Balanceador de carga padrão do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 9003d35ce2eea18aa912a866802b026bb923aa08
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272688"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnóstico do Standard Load Balancer com métricas, alertas e integridade de recursos

O Azure Standard Load Balancer expõe os seguintes recursos de diagnóstico:

* **Métricas e alertas multidimensionais:** Fornece recursos de diagnóstico multidimensionais através do [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para configurações padrão do balanceador de carga. Você pode monitorar, gerenciar e solucionar problemas dos recursos padrão do balanceador de carga.

* **Saúde dos recursos**: A página Load Balancer no portal Azure e a página Resource Health (em Monitor) expõem a seção Saúde de Recursos para Balanceador de Carga Padrão. 

Este artigo fornece um tour rápido dessas funcionalidades e oferece maneiras de usá-las para o Load Balancer Standard. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece métricas multidimensionais através do Azure Metrics no portal Azure, e ajuda você a obter insights de diagnóstico em tempo real sobre seus recursos de balanceador de carga. 

As várias configurações do Load Balancer Standard oferecem as seguintes métricas:

| Métrica | Tipo de recurso | Descrição | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade de caminho de dados | Balanceador de carga público e interno | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do balanceador de carga e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados que seus clientes usam também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.| Média |
| Status da sonda de saúde | Balanceador de carga público e interno | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância no pool do balanceador de carga. É possível ver como o Load Balancer exibe a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade. |  Média |
| Pacotes SYN (sincronizar) | Balanceador de carga público e interno | O Load Balancer Standard não encerra conexões TCP nem interage com fluxos de pacote TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.| Média |
| Conexões SNAT | Balanceador de carga público |O Load Balancer Standard relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT (conversão de endereços de rede) de origem são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa. Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.| Média |
| Portas SNAT alocadas | Balanceador de carga público | O Balancer de carga padrão informa o número de portas SNAT alocadas por instância de backend | Média. |
| Portas SNAT usadas | Balanceador de carga público | O Standard Load Balancer informa o número de portas SNAT utilizadas por instância de backend. | Média | 
| Contadores de bytes |  Balanceador de carga público e interno | O Load Balancer Standard informa os dados processados por front-end. Você pode notar que os bytes não são distribuídos igualmente entre as instâncias de back-end. Isso é esperado, pois o algoritmo load balancer do Azure é baseado em fluxos | Média |
| Contadores de pacotes |  Balanceador de carga público e interno | O Load Balancer Standard relata os pacotes processados por front-end.| Média |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Exibir suas métricas do balanceador de carga no portal do Azure

O portal Azure expõe as métricas do balanceador de carga através da página Métricas, que está disponível tanto na página de recursos do balanceador de carga para um determinado recurso quanto na página do Monitor do Azure. 

Para exibir as métricas de seus recursos do Load Balancer Standard:
1. Vá para a página Métricas e faça qualquer um dos seguintes:
   * Na página de recursos do balanceador de carga, selecione o tipo de métrica na lista suspensa.
   * Na página do Azure Monitor, selecione o recurso do balanceador de carga.
2. Defina o tipo de agregação métrica apropriado.
3. Ou configure a filtragem e o agrupamento necessários.
4. Opcionalmente, configure o intervalo de tempo e a agregação. Por tempo padrão é exibido em UTC.

  >[!NOTE] 
  >A agregação de tempo é importante ao interpretar determinadas métricas à medida que os dados são amostrados uma vez por minuto. Se a agregação de tempo for definida como cinco minutos e o tipo de agregação métrica Soma for usada para métricas como alocação de SNAT, seu gráfico exibirá cinco vezes o total alocado de portas SNAT. 

![Métricas para balanceador de carga padrão](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Figura: Métrica de disponibilidade do caminho de dados para o Balanceador de carga padrão*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar as métricas multidimensionais programaticamente por meio de APIs

Para obter diretrizes sobre API para recuperar valores e definições de métricas multidimensionais, consulte o [passo a passo da API REST de Monitoramento do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Essas métricas podem ser escritas em uma conta de armazenamento apenas através da opção 'Todas as Métricas'. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Configure alertas para métricas multidimensionais ###

O Azure Standard Load Balancer suporta alertas facilmente configuráveis para métricas multidimensionais. Configure limiares personalizados para métricas específicas para disparar alertas com diferentes níveis de gravidade para capacitar uma experiência de monitoramento de recursos sem toque.

Para configurar alertas:
1. Vá para a sublâmina de alerta para o balanceador de carga
1. Criar nova regra de alerta
    1.  Configurar condição de alerta
    1.  (Opcional) Adicionar grupo de ação para reparo automatizado
    1.  Atribuir gravidade de alerta, nome e descrição que permite reação intuitiva

  >[!NOTE]
  >A janela de configuração da condição de alerta mostrará séries tempormétricas para o histórico do sinal. Há uma opção para filtrar esta série de tempo por dimensões como O IP do Backend. Isso filtrará o gráfico da série temporal, mas **não** o alerta em si. Não é possível configurar alertas para endereços IP backend específicos.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Cenários comuns de diagnósticos e modos de exibição recomendados

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>O caminho de dados está disponível para o meu Load Balancer Frontend?
<details><summary>Expanda</summary>

A métrica de disponibilidade do caminho de dados descreve a saúde do caminho de dados dentro da região para o host de computação onde suas VMs estão localizadas. A métrica é uma reflexão da integridade da infraestrutura do Azure. É possível usar a métrica para:
- Monitorar a disponibilidade externa do seu serviço
- Obter detalhes e entender se a plataforma na qual o serviço é implantado está íntegra ou se a instância do aplicativo ou o SO convidado está íntegro.
- Isolar se um evento está relacionado ao seu serviço ou o plano de dados subjacente. Não confunda essa métrica com o status do teste de saúde ("Disponibilidade de instância de backend").

Para obter a disponibilidade do caminho de dados para os recursos do Balancer de carga padrão:
1. Verifique se o recurso do balanceador de carga correto está selecionado. 
2. Na lista de parada **métrica,** selecione **Disponibilidade de caminho**de dados . 
3. Na lista suspensa **Agregação**, selecione **Méd**. 
4. Além disso, adicione um filtro no endereço IP do Frontend ou na porta Frontend como a dimensão com o endereço IP front-end ou porta front-end necessário e, em seguida, agrupe-os pela dimensão selecionada.

![Investigação do VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Detalhes de sondagem do Balancer de carga Frontend*

A métrica é gerada por uma medida de ativa e dentro da faixa. Um serviço de investigação dentro da região origina o tráfego da medida. O serviço é ativado assim que você cria uma implantação com um front-end público, e ele continua até que você remova o front-end. 

Um pacote que corresponde ao front-end e a uma regra de sua implantação é gerado periodicamente. Ele atravessa a região da origem para o host no qual uma VM no pool de back-end está localizada. A infraestrutura do balanceador de carga executa as mesmas operações de balanceamento de carga e de translação, assim como faz para todos os outros tráfegos. Essa investigação é feita dentro da banda no seu ponto de extremidade com balanceamento de carga. Depois que a investigação chega no host de computação no qual uma VM íntegra no pool de back-end está localizada, o host de computação gera uma resposta para o serviço de investigação. A VM não vê esse tráfego.

A disponibilidade do datapath falha pelas seguintes razões:
- Sua implantação não tem nenhuma VM íntegra restante no pool de back-end. 
- Ocorreu uma interrupção de infraestrutura.

Para fins diagnósticos, você pode usar a [métrica de disponibilidade do caminho de dados juntamente com o status do teste de saúde](#vipavailabilityandhealthprobes).

Use **Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>As instâncias de backend do meu Balanceador de carga estão respondendo aos testes?
<details>
  <summary>Expanda</summary>
A métrica de status de investigação de integridade descreve a integridade da implantação do aplicativo, conforme configurado por você ao configurar a investigação de integridade do balanceador de carga. O balanceador de carga usa o status da investigação de integridade para determinar para onde enviar novos fluxos. As investigações de integridade se originam de um endereço de infraestrutura do Azure e são visíveis no sistema operacional convidado da VM.

Para obter o status do teste de saúde para os recursos do Balanceador de carga padrão:
1. Selecione a métrica **status da sonda** de saúde com o tipo de agregação **Avg.** 
2. Aplique um filtro no endereço IP ou porta do Frontend necessário (ou ambos).

As investigações de integridade falham pelos seguintes motivos:
- Ao configurar uma investigação de integridade em uma porta que não está escutando ou não está respondendo ou que está usando o protocolo incorreto. Se seu serviço está usando regras de DSR (retorno de servidor direto ou IP flutuante), verifique ele está escutando no endereço IP da configuração IP do NIC e não apenas no loopback configurado com o endereço IP de front-end.
- Sua investigação não é permitida pelo Grupo de Segurança de Rede, o firewall do SO convidado da VM ou os filtros da camada do aplicativo.

Use **Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como verificar as minhas estatísticas de conexão de saída? 
<details>
  <summary>Expanda</summary>
A métrica de conexões SNAT descreve o volume de conexões bem-sucedidas e com falha para [fluxos de saída](https://aka.ms/lboutbound).

Um volume de conexões com falha maior que zero indica o esgotamento da porta SNAT. É necessário investigar mais para determinar o que pode estar causando essas falhas. O esgotamento de porta SNAT manifesta-se como uma falha para estabelecer um [fluxo de saída](https://aka.ms/lboutbound). Examine o artigo sobre conexões de saída para entender os cenários e mecanismos no trabalho e para saber como minimizar e criar para evitar o esgotamento da porta SNAT. 

Para obter estatísticas de conexão SNAT:
1. Selecione o tipo de métrica **Conexões SNAT** e **Soma** como agregação. 
2. Agrupe por **Estado de Conexão** para contagens de conexão SNAT bem-sucedidas e com falha representadas por linhas diferentes. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Contagem de conexões SNAT do Balanceador de carga*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Como faço para verificar o uso e a alocação da minha porta SNAT?
<details>
  <summary>Expanda</summary>
A métrica de uso do SNAT indica quantos fluxos exclusivos são estabelecidos entre uma fonte de internet e um conjunto de escala de vm ou máquina virtual backend que está por trás de um balanceador de carga e não tem um endereço IP público. Comparando isso com a métrica de alocação snat, você pode determinar se seu serviço está experimentando ou em risco de exaustão do SNAT e consequente falha de fluxo de saída. 

Se suas métricas indicarem risco de falha no [fluxo de saída,](https://aka.ms/lboutbound) consulte o artigo e tome medidas para mitigar isso para garantir a saúde do serviço.

Para exibir o uso e a alocação da porta SNAT:
1. Defina a agregação de tempo do gráfico para 1 minuto para garantir que os dados desejados sejam exibidos.
1. Selecione **o uso do SNAT** e/ou **alocação de SNAT** como o tipo métrico e **a média** como a agregação
    * Por padrão, este é o número médio de portas SNAT alocadas ou usadas por cada VMs ou VMSSes backend, correspondentes a todos os IPs públicos frontend mapeados para o Balancer de Carga, agregados sobre TCP e UDP.
    * Para visualizar as portas SNAT totais usadas ou alocadas para o balanceador de carga, use aagregação métrica **Soma**
1. Filtrar para um tipo de **protocolo**específico, um conjunto de **IPs backend**e/ou **IPs Frontend**.
1. Para monitorar a saúde por backend ou instância frontend, aplique a divisão. 
    * A divisão de notas só permite que uma única métrica seja exibida de cada vez. 
1. Por exemplo, monitorar o uso de SNAT para fluxos TCP por máquina, agregar por **Média,** dividido por **IPs Backend** e filtrar por **Tipo de Protocolo**. 

![Alocação e uso do SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figura: Alocação e uso médioda da porta TCP SNAT para um conjunto de VMs backend*

![Uso de SNAT por instância backend](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figura: Uso da porta TCP SNAT por instância backend*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como verificar as tentativas de conexão de entrada/saída para meu serviço?
<details>
  <summary>Expanda</summary>
Uma métrica de pacotes SYN descreve o volume de pacotes TCP SYN, que chegaram ou foram enviados (para [fluxos de saída](https://aka.ms/lboutbound)) associados a um front-end específico. É possível usar essa métrica para entender as tentativas de conexão TCP com seu serviço.

Use **Total** como a agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN do balanceador de carga*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como verificar o consumo de largura de banda da rede? 
<details>
  <summary>Expanda</summary>
A métrica de contadores de pacote e de bytes descreve o volume de bytes e de pacotes enviados ou recebidos pelo seu serviço por front-end.

Use **Total** como a agregação para a maioria dos cenários.

Para obter estatísticas de contagem de bytes ou de pacotes:
1. Selecione o tipo de métrica **Contagem de Bytes** e/ou **Contagem de Pacotes** com **Méd** como a agregação. 
2. Execute um destes procedimentos:
   * Aplique um filtro em um IP de front-end específico, em uma porta de front-end, em um IP de back-end ou em uma porta de back-end.
   * Obtenha estatísticas gerais para seu recurso de balanceador de carga sem nenhuma filtragem.

![Contagem de Bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de bytes do balanceador de carga*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Como posso diagnosticar a implantação do meu balanceador de carga?
<details>
  <summary>Expanda</summary>
Usando uma combinação das métricas de disponibilidade de caminho de dados e status da sonda de saúde em um único gráfico, você pode identificar onde procurar o problema e resolver o problema. É possível obter a garantia de que o Azure está funcionando corretamente e usar esse conhecimento para determinar conclusivamente que a configuração ou o aplicativo é a causa raiz.

Você pode usar métricas de investigação de integridade para entender como o Azure exibe a integridade de sua implantação de acordo com a configuração que você forneceu. Olhar para investigações de integridade é sempre uma excelente primeira etapa no monitoramento ou determinação de uma causa.

Você pode dar um passo adiante e usar a métrica de disponibilidade do Data Path para obter informações sobre como o Azure vê a saúde do plano de dados subjacente responsável por sua implantação específica. Ao combinar as duas métricas, é possível isolar onde a falha pode estar, conforme ilustrado nesse exemplo:

![Combinando métricas de disponibilidade do caminho de dados e status da sonda de saúde](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: Combinando as métricas de disponibilidade do caminho de dados e status da sonda de saúde*

O gráfico exibe as seguintes informações:
- A infra-estrutura que hospeda suas VMs não estava disponível e em 0% no início do gráfico. Mais tarde, a infraestrutura era saudável e as VMs eram acessíveis, e mais de uma VM foi colocada na parte traseira. Essas informações são indicadas pelo rastreamento azul para disponibilidade do caminho de dados, que foi posteriormente em 100%. 
- O estado da sonda de saúde, indicado pelo traço roxo, está em 0% no início do gráfico. A área circundada em verde destaca onde o status da sonda de saúde se tornou saudável, e nesse ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes resolvam problemas da implantação sozinhos sem a necessidade de adivinhar ou perguntar ao suporte se outros problemas estão ocorrendo. O serviço não estava disponível porque as investigações de integridade estavam falhando devido a um erro de configuração ou a um aplicativo com falha.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Status de integridade de recurso

O status de integridade para os recursos do Load Balancer Standard é exposto por meio do **Recursos de integridade** existente em **Monitor > Integridade do Serviço**.

Para exibir a integridade dos seus recursos do Load Balancer Standard:
1. Selecione **Monitor** > **Service Health**.

   ![Página do Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: o link da Integridade do Serviço no Azure Monitor*

2. Selecione **Resource Health** e certifique-se de que a **ID da assinatura** e o **Tipo de recurso = Load Balancer** estão selecionados.

   ![Status de integridade de recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: selecione o recurso para o modo de exibição de integridade*

3. Na lista, selecione o recurso do Load Balancer para exibir o status da integridade histórico.

    ![Status de integridade do Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: modo de exibição de integridade de recurso do Load Balancer*
 
Os vários status da integridade do recurso e suas descrições estão listadas na seguinte tabela: 

| Status de integridade de recurso | Descrição |
| --- | --- |
| Disponível | Seu recurso de balanceador de carga padrão é saudável e disponível. |
| Indisponível | Seu recurso de balanceador de carga padrão não é saudável. Diagnosticar a saúde selecionando > **métricas**do **Monitor Azure**.<br>( O status*indisponível* também pode significar que o recurso não está conectado ao balanceador de carga padrão.) |
| Unknown (desconhecido) | O estado de saúde dos recursos para o seu recurso de balanceador de carga padrão ainda não foi atualizado.<br>(*Status desconhecido* também pode significar que o recurso não está conectado ao seu balanceador de carga padrão.)  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- Saiba mais sobre a [Conectividade de saída do balanceador de carga](https://aka.ms/lboutbound).
- Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Saiba mais sobre o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) e [como recuperar as métricas por meio da API REST](/rest/api/monitor/metrics/list).
