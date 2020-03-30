---
title: Diagnosticar e solucionar problemas em um ambiente de visualização - Azure Time Series Insights | Microsoft Docs
description: Saiba como diagnosticar e solucionar problemas em um ambiente de visualização de insights da série do tempo do Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152631"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnosticar e solucionar problemas em um ambiente de visualização

Este artigo resume vários problemas comuns que você pode encontrar ao trabalhar com o ambiente da Versão prévia do Azure Time Series Insights. O artigo também descreve possíveis causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problema: eu não consigo encontrar meu ambiente no explorador de visualização

Esse problema pode ocorrer se você não tem permissões para acessar o ambiente do Time Series Insights. Os usuários precisam de uma função de acesso de nível de leitura para exibir o ambiente do Time Series Insights. Para verificar os níveis de acesso atuais e conceder acesso adicional, acesse a seção **Políticas de Acesso** a Dados no recurso Time Series Insights no portal [Azure](https://portal.azure.com/).

  [![Verifique as políticas de acesso a dados.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: Nenhum dado é visto no explorador de visualização

Existem várias razões comuns pelas quais seus dados podem não aparecer no [explorador de visualização de visualizações da série do tempo do Azure](https://insights.timeseries.azure.com/preview).

- Pode ser que a origem do evento não esteja recebendo dados.

    Verifique se a origem do evento, que é um hub de eventos ou um hub IoT, está recebendo dados de suas marcas ou instâncias. Para verificar, acesse a página de visão geral do recurso no portal do Azure.

    [![Revise a visão geral das métricas do painel.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Os dados de origem do evento não estão no formato JSON.

    O Time Series Insights é compatível somente com os dados JSON. Para amostras JSON, leia [as formas JSON suportadas](./how-to-shape-query-json.md).

- A chave da origem do evento não tem uma permissão necessária.

  * Para o Hub IoT, você precisa fornecer a chave com a permissão de **conexão de serviço**.

    [![Verifique as permissões do hub de IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Tanto as políticas **iothubowner** quanto **o serviço** funcionam porque eles têm permissão **de conexão de serviço.**

  * Para o hub de eventos, você precisa fornecer a chave que tem a permissão de **Escuta**.
  
    [![Revise as permissões do hub de eventos.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Tanto as políticas **de leitura** quanto **de gerenciamento** funcionam porque têm permissão **de ouvir.**

- O grupo de consumidores fornecido não é exclusivo do Time Series Insights.

    Durante o registro de um hub IoT ou hub de eventos, você especifica o grupo de consumidores que é usado para ler os dados. Esse grupo de consumidores deve ser único por ambiente. Se o grupo de consumidores for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória. Forneça um grupo de consumidores exclusivo para o Time Series Insights para leitura.

- A propriedade de ID do Time Series especificada no momento do provisionamento está incorreta, ausente ou nula.

    Esse problema poderá ocorrer se a propriedade de ID do Time Series for configurada incorretamente no momento do provisionamento do ambiente. Para obter mais informações, leia [as melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md). Neste momento, você não pode atualizar um ambiente existente do Time Series Insights para usar uma ID do Time Series diferente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: Alguns dados mostram, mas alguns estão faltando

Pode ser que você esteja enviando dados sem a ID do Time Series.

- Esse problema pode ocorrer quando você envia eventos sem o campo de ID do Time Series no conteúdo. Para obter mais informações, leia [as formas JSON suportadas](./how-to-shape-query-json.md).
- Esse problema pode ocorrer porque o seu ambiente está sendo limitado.

    > [!NOTE]
    > No momento, o Time Series Insights é compatível com uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problema: Os dados estavam aparecendo, mas agora a ingestão parou

- Sua chave de origem do evento pode ter sido regenerada e seu ambiente de visualização precisa da nova chave de origem do evento.

Esse problema ocorre quando a chave fornecida ao criar sua fonte de evento não é mais válida. Você veria a telemetria em seu hub, mas nenhuma Ingress Recebeu Mensagens em Insights da Série Tempo. Se você não tiver certeza se a chave foi regenerada ou não, você pode pesquisar no registro de atividades do seu Event Hubs para "Criar ou Atualizar regras de autorização de namespace" ou pesquisar "Criar ou atualizar o Recurso IotHub" para o hub IoT. 

Para atualizar o ambiente de visualização de insights da série do tempo com a nova chave, abra o recurso do hub no portal Azure e copie a nova chave. Navegue até o recurso TSI e clique em Fontes de Eventos. 

   [![Atualização da chave.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selecione as fontes de eventos que têm a partir da qual a ingestão parou, cole na nova tecla e clique em Salvar.

   [![Atualização da chave.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: O nome de propriedade do timestamp da minha fonte de evento não funciona

Verifique se o nome e o valor estão em conformidade com as seguintes regras:

* O nome da propriedade Carimbo de data/hora diferencia maiúsculas de minúsculas.
* O valor de propriedade Timestamp que vem da sua fonte `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`de evento como uma seqüência JSON tem o formato . Um exemplo dessa cadeia de caracteres é `“2008-04-12T12:53Z”`.

A maneira mais fácil de assegurar que o nome da propriedade Carimbo de data/hora seja capturado e funcione corretamente é usar o gerenciador da Versão prévia do Time Series Insights. No gerenciador da Versão prévia do Time Series Insights, use o gráfico para selecionar um período de tempo após fornecer o nome da propriedade Carimbo de data/hora. Clique com o botão direito do mouse na seleção e escolha a opção **explorar eventos**. O primeiro cabeçalho de coluna é o nome da propriedade Carimbo de data/hora. Ele deve ter `($ts)` ao lado da palavra `Timestamp`, em vez de:

* `(abc)`, que indica que o Time Series Insights lê os valores de dados como cadeias de caracteres.
* O ícone **de calendário,** que indica que o Time Series Insights lê o valor dos dados como data de data.
* `#`, que indica que o Time Series Insights lê os valores de dados como um inteiro.

Se a propriedade Carimbo de data/hora não for especificada explicitamente, o tempo de enfileiramento de um hub IoT ou hub de eventos será usado como o Carimbo de data/hora padrão.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: eu não posso ver dados da minha loja quente no explorador

- Você pode ter provisionado sua loja quente recentemente, e os dados ainda estão fluindo dentro
- Você pode ter excluído sua loja quente, nesse caso você teria perdido dados.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: eu não posso visualizar ou editar meu Modelo de Série do Tempo

- Talvez você esteja acessando um ambiente S1 ou S2 do Time Series Insights.

   Os modelos de séries tempoé suportado apenas em ambientes pay-as-you-go. Para obter mais informações sobre como acessar seu ambiente S1 ou S2 a partir do explorador de visualização de insights da série do tempo, leia [Visualizar dados no explorador](./time-series-insights-update-explorer.md).

   [![Nenhum evento no ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Talvez você não tenha permissões para exibir e editar o modelo.

   Os usuários precisam de acesso de nível de colaborador para editar e exibir o modelo do Time Series. Para verificar os níveis de acesso atuais e conceder acesso adicional, acesse a seção **Políticas de Acesso a Dados** no recurso Time Series Insights no portal Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: Todas as minhas instâncias no explorador de visualização não têm um pai

Esse problema pode ocorrer o ambiente não tem uma hierarquia de modelo do Time Series definida. Para obter mais informações, leia [Work with Time Series Models](./time-series-insights-update-how-to-tsm.md).

  [![As instâncias não-parentais exibirão um aviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Leia [Trabalhar com modelos do Time Series](./time-series-insights-update-how-to-tsm.md).

- Saiba mais sobre [as formas JSON suportadas](./how-to-shape-query-json.md).

- Revise [o planejamento e os limites](./time-series-insights-update-plan.md) no Azure Time Series Insights Preview.
