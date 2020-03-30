---
title: Diagnosticar, solucionar problemas e resolver problemas - Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como diagnosticar, solucionar problemas e resolver problemas comuns em seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152563"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no ambiente do Time Series Insights

Este artigo descreve alguns problemas que você pode encontrar em seu ambiente do Azure Time Series Insights. O artigo fornece possíveis causas e soluções para resolução.

## <a name="video"></a>Vídeo

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Conheça os desafios e atenuações comuns da time series insights do cliente.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: nenhum dado é mostrado

Há vários motivos comuns pelos quais você não pode ver seus dados no [gerenciador do Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: os dados de origem do evento não estão no formato JSON

O Azure Time Series Insights dá suporte somente a dados JSON. Para amostras JSON, leia [as formas JSON suportadas](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: a chave de origem do evento está faltando uma permissão necessária

* Para um hub IoT no Hub IoT do Azure, você precisa fornecer a chave com as permissões de **conexão de serviço**. Selecione as políticas **de iothubowner** ou **service,** já que ambas têm permissões **de conexão** de serviço.

   [![Permissões de conexão de serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Para um hub de eventos nos Hubs de Eventos do Azure, você precisa fornecer a chave que tem a permissão de **escuta**. Qualquer umas das políticas de **ler** ou **gerenciar** funcionará porque ambas têm permissões para **escutar**.

   [![Permissão de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Causa C: o grupo de consumidores fornecido não é exclusivo do Time Series Insights

Quando você registra um hub IoT ou um hub de eventos, é importante definir o grupo de consumidores que deseja usar para ler os dados. Esse grupo de consumidores *não pode ser compartilhado*. Se o grupo de consumidores for compartilhado, o hub IoT ou o hub de eventos subjacente desconectará de forma automática e aleatória um dos leitores. Forneça um grupo de consumidores exclusivo para o Time Series Insights para leitura.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: o ambiente acaba de ser provisionado

Os dados aparecerão no explorador Time Series Insights dentro de alguns minutos após o ambiente e seus dados serem criados pela primeira vez.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: alguns dados são mostrados, mas os dados estão faltando

Quando dados são exibidos apenas parcialmente e parecem estar apresentando retardo, você deve considerar várias possibilidades.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa A: seu ambiente está sendo estrangulado

[O estrangulamento](time-series-insights-environment-mitigate-latency.md) é um problema comum quando os ambientes são provisionados após a criação de uma fonte de evento que tenha dados. Os Hubs de Eventos do Azure e o Hub IoT do Azure armazenam dados por até sete dias. O Time Series Insights sempre começa com o evento mais antigo na origem do evento (primeiro a entrar, primeiro a sair ou *PEPS*).

Por exemplo, se você tiver 5 milhões de eventos em uma origem do evento quando se conecta a um ambiente do Time Series Insights S1, de unidade única, o Time Series Insights lerá aproximadamente 1 milhão de eventos por dia. Pode parecer que o Time Series Insights está apresentando cinco dias de latência. No entanto, o que está acontecendo é que o ambiente está sendo limitado.

Se houver eventos antigos na origem do evento, você poderá lidar com a limitação de duas maneiras:

- Alterar os limites de retenção da origem do evento para ajudar a eliminar eventos antigos que você não quer exibir no Time Series Insights.
- Provisionar um tamanho de ambiente maior (número de unidades) para aumentar a taxa de transferência de eventos antigos. Usando o exemplo anterior, se você aumentou o mesmo ambiente S1 para cinco unidades por um dia, o ambiente deverá atualizar durante o dia. Se a produção de evento de estado contínuo for de um milhão ou menos de eventos por dia, será possível reduzir a capacidade do evento para uma unidade após ele ser atualizado.

A limitação é imposta com base na capacidade e no tipo de SKU do ambiente. Todas as origens do evento no ambiente compartilham essa capacidade. Se a fonte de evento para o seu hub de IoT ou hub de eventos empurrar os dados para além dos limites impostos, você experimentará estrangulamento e um atraso.

A figura a seguir mostra um ambiente do Time Series Insights com um SKU S1 e uma capacidade 3. Ele pode ingressar 3 milhões de eventos por dia.

[![Capacidade atual do SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Como exemplo, suponha que um ambiente ingere mensagens de um hub de eventos. A taxa de entrada diária é de cerca de 67.000 mensagens. Essa taxa é equivalente a aproximadamente 46 mensagens por minuto. 

* Se cada mensagem do hub de eventos for nivelada para um único evento do Time Series Insights, a limitação não ocorrerá. 
* Se cada mensagem do hub de eventos for nivelada para 100 eventos do Time Series Insights, 4.600 eventos deverão ser ingeridos a cada minuto. 

Um ambiente de SKU S1 que tem uma capacidade de 3 pode ingressar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2.100 eventos por minuto). 

Para uma compreensão de alto nível de como a lógica de achatamento funciona, leia [formas JSON suportadas](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resolução recomendadas para limitação excessiva

Para corrigir o retardo, aumente a capacidade do SKU do ambiente. Para obter mais informações, leia [Dimensionar o ambiente de Insights da série do tempo](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: ingestão inicial de dados históricos retarda a ingestão

Se você se conectar a uma origem do evento existente, provavelmente o hub IoT ou o hub de eventos já conterá dados. O ambiente inicia o pull dos dados desde o início do período de retenção de mensagens da origem do evento. Este processamento padrão não pode ser substituído. Você pode acionar a limitação. A limitação pode levar algum tempo para ficar atualizada conforme ela consome os dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Soluções recomendadas de ingestão inicial grande

Para corrigir o retardo:

1. aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Após você aumentar a capacidade, o processo de entrada começará a se atualizar muito mais rapidamente. Você é cobrado pelo aumento da capacidade. Para visualizar a rapidez da atualização, você pode exibir o do gráfico de disponibilidade no [gerenciador do Time Series Insights](https://insights.timeseries.azure.com).

2. Depois que o retardo for atualizado, diminua a capacidade do SKU para a taxa de entrada normal.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: os dados estavam aparecendo anteriormente, mas não estão mais mostrando

TSI não está mais ingerindo dados, mas os eventos ainda estão fluindo para o Iot Hub ou Event Hub

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Causa A: sua chave de acesso ao hub foi regenerada e seu ambiente precisa ser atualizado

Esse problema ocorre quando a chave fornecida ao criar sua fonte de evento não é mais válida. Você veria a telemetria em seu hub, mas nenhuma Ingress Recebeu Mensagens em Insights da Série Tempo. Se você não tiver certeza se a chave foi regenerada ou não, você pode pesquisar no registro de atividades do seu Event Hubs para "Criar ou Atualizar regras de autorização de namespace" ou pesquisar "Criar ou atualizar o Recurso IotHub" para o hub IoT.

Para atualizar seu ambiente de Insights da série do tempo com a nova chave, abra seu recurso de hub no portal Azure e copie a nova chave. Navegue até o recurso TSI e clique em Fontes de Eventos. 

   [![Atualização da chave.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecione as fontes de eventos que têm a partir da qual a ingestão parou, cole na nova tecla e clique em Salvar.

   [![Atualização da chave.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: a configuração do nome do nome do carimbo de tempo da minha fonte de evento não funciona

Verifique se o nome e o valor da propriedade de nome do carimbo de data/hora estão em conformidade com as seguintes regras:

* O nome da propriedade do carimbo de data/hora diferencia maiúsculas de minúsculas.
* O valor da propriedade do carimbo de data/hora é obtido da origem do evento, como uma cadeia de caracteres JSON, deve ter o formato _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_. Um exemplo é **2008-04-12T12:53Z**.

A maneira mais fácil de assegurar que o nome da propriedade Carimbo de data/hora seja capturado e funcione corretamente é usar o gerenciador do Time Series Insights. No gerenciador do Time Series Insights, usando o gráfico, selecione um período de tempo após fornecer o nome da propriedade de carimbo de data/hora. Clique com o botão direito do mouse na seleção e escolha a opção **Explorar eventos**.

O cabeçalho da primeira coluna deve ser o nome da propriedade de carimbo de data/hora. Ao lado da palavra **Carimbo de tempo**( **$ts)** será exibida.

Os seguintes valores não serão exibidos:

- *(abc)*: Indica que o Time Series Insights está lendo os valores de dados como strings.
- *Ícone do calendário*: Indica que o Time Series Insights está lendo o valor dos dados como *data.*
- *#*: Indica que o Time Series Insights está lendo os valores de dados como um inteiro.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [como mitigar a latência no Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Aprenda [a dimensionar seu ambiente de Insights de séries tempo .](time-series-insights-how-to-scale-your-environment.md)