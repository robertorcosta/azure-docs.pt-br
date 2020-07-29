---
title: Usar métricas para monitorar o IoT Hub do Azure | Microsoft Docs
description: Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral dos seus hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791836"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>Entender as métricas de Hub IoT

As métricas do Hub IoT fornecem informações sobre o estado dos recursos de IoT do Azure em sua assinatura do Azure. As métricas do Hub IoT ajudam a avaliar a integridade geral do serviço do Hub IoT e os dispositivos conectados a ele. Essas estatísticas voltadas para o usuário ajudam a ver o que está acontecendo com o Hub IoT e ajudam a executar a análise da causa raiz sobre problemas sem a necessidade de contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do Hub IoT no portal do Azure.

> [!NOTE]
> Você pode usar as métricas do Hub IoT para exibir informações sobre dispositivos IoT Plug and Play conectados ao seu hub IoT. Os dispositivos IoT Plug and Play fazem parte da [Visualização pública do iot plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Como exibir métricas de IoT Hub

1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um hub IoT na [enviar telemetria de um dispositivo ao IoT Hub](quickstart-send-telemetry-dotnet.md) guia.

2. Abra a folha do seu Hub IoT. A partir daí, clique em **métricas**.
   
    ![Captura de tela mostrando onde a opção de métricas está na página de recursos do IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. Folha de métricas, você pode exibir as métricas para o hub IoT e criar exibições personalizadas de suas métricas. 
   
    ![Captura de tela mostrando a página de métricas para o IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de hubs de eventos ou uma conta de armazenamento do Azure clicando em **configurações de diagnóstico**e, em seguida, **Adicionar configuração de diagnóstico**.

   ![Captura de tela mostrando onde o botão de configurações de diagnóstico é](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las

O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RoutingDeliveries | Roteamento de tentativas de entrega (visualização) | Contagem | Total |Essa é a métrica de entrega de roteamento. Use as dimensões para identificar o status de entrega para um ponto de extremidade específico ou para uma fonte de roteamento específica.| Identificação<br>Disso<br>Roteamento,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*Mais detalhes sobre dimensões [**aqui**](#dimensions)*. |
|RoutingDeliveryLatency| Latência de roteamento (versão prévia) | Milissegundos | Média |Essa é a métrica de latência de entrega de roteamento. Use as dimensões para identificar a latência de um ponto de extremidade específico ou de uma fonte de roteamento específica.| Identificação<br>Roteamento,<br>EndpointType<br>EndpointName<br>*Mais detalhes sobre dimensões [**aqui**](#dimensions)*.|
|RoutingDataSizeInBytesDelivered| Tamanho dos dados de entrega de roteamento em bytes (versão prévia)| Bytes | Total |O número total de bytes roteados pelo Hub IoT para o ponto de extremidade personalizado e o ponto de extremidade interno. Use as dimensões para identificar o tamanho dos dados roteados para um ponto de extremidade específico ou para uma fonte de roteamento específica.| Identificação<br>Roteamento,<br>EndpointType<br>EndpointName<br>*Mais detalhes sobre dimensões [**aqui**](#dimensions)*.|
|D2C. telemetria. entrada.<br>allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Nenhum|
|D2C. telemetria. entrada.<br>sucesso|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Nenhum|
|C2D. Commands. egresso.<br>concluído. êxito|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhum|
|C2D. Commands. egresso.<br>abandono. êxito|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhum|
|C2D. Commands. egresso.<br>rejeitar. êxito|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D expiradas (versão prévia)|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhum|
|devices.totalDevices|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|Devices. connectedDevices.<br>allProtocol|Dispositivos conectados (preteridos) |Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|D2C. telemetria. egresso.<br>sucesso|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Nenhum|
|D2C. telemetria. egresso.<br>passou|Roteamento: mensagens de telemetria removidas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Nenhum|
|D2C. telemetria. egresso.<br>orfão|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma consulta de roteamento, quando a rota de fallback está desabilitada.|Nenhum|
|D2C. telemetria. egresso.<br>inválido|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Uma mensagem é incompatível com um ponto de extremidade quando o Hub IOT tenta entregar a mensagem a um ponto de extremidade e falha com um erro não transitório. Mensagens inválidas não são repetidas. Esse valor não inclui novas tentativas.|Nenhum|
|D2C. telemetria. egresso.<br>fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Nenhum|
|pontos de D2C. end. de saída.<br>eventHubs|Roteamento: mensagens entregues ao Hub de Eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade personalizados do tipo Hub de eventos. Isso não inclui rotas de mensagens para ponto de extremidade interno (eventos).|Nenhum|
|D2C. pontos de extremidade. latência.<br>eventHubs|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em pontos de extremidade personalizados do tipo Hub de eventos. Isso não inclui rotas de mensagens para ponto de extremidade interno (eventos).|Nenhum|
|pontos de D2C. end. de saída.<br>serviceBusQueues|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Nenhum|
|D2C. pontos de extremidade. latência.<br>serviceBusQueues|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em um ponto de extremidade da fila do barramento de serviço.|Nenhum|
|pontos de D2C. end. de saída.<br>serviceBusTopics|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Nenhum|
|D2C. pontos de extremidade. latência.<br>serviceBusTopics|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagem para o Hub IoT e a entrada de mensagem em um ponto de extremidade de tópico do barramento de serviço.|Nenhum|
|pontos de D2C. end. de saída.<br>eventos internos|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos) e a rota de fallback.|Nenhum|
|D2C. pontos de extremidade. latência.<br>eventos internos|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem no ponto de extremidade interno (mensagens/eventos) e na rota de fallback.|Nenhum|
|pontos de D2C. end. de saída.<br>armazenamento|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Nenhum|
|D2C. pontos de extremidade. latência.<br>armazenamento|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em um ponto de extremidade de armazenamento.|Nenhum|
|pontos de D2C. end. de saída.<br>armazenamento. bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Nenhum|
|pontos de D2C. end. de saída.<br>Storage. BLOBs|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Nenhum|
|EventGridDeliveries|Entregas da Grade de Eventos (versão prévia)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|Identificação<br/>Disso<br/>EventType|
|EventGridLatency|Latência da Grade de Eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|Identificação<br/>EventType|
|d2c.twin.read.success|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.read.failure|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Nenhum|
|d2c.twin.read.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|O número de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.failure|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|O tamanho total de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhum|
|c2d.methods.success|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.failure|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Nenhum|
|c2d.methods.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A contagem de todas as solicitações de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Média|A contagem de todas as respostas de método diretas bem-sucedidas.|Nenhum|
|c2d.twin.read.success|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.failure|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.success|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhum|
|c2d.twin.update.failure|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|O tamanho total de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhum|
|twinQueries.success|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Nenhum|
|twinQueries.failure|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Média|O total do tamanho do resultado de todas as consultas de myup com êxito.|Nenhum|
|Jobs. createTwinUpdateJob.<br>sucesso|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Nenhum|
|Jobs. createTwinUpdateJob.<br>falha|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Nenhum|
|Jobs. createDirectMethodJob.<br>sucesso|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Nenhum|
|Jobs. createDirectMethodJob.<br>falha|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Nenhum|
|jobs.listJobs.success|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Nenhum|
|jobs.listJobs.failure|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Nenhum|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Nenhum|
|jobs.queryJobs.success|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Nenhum|
|jobs.queryJobs.failure|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Nenhum|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|jobs.failed|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhum|
|D2C. telemetria. entrada.<br>sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens utilizadas hoje. Este é um valor cumulativo que é reiniciado para zero em 00:00 UTC diariamente.|Nenhum|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|deviceDataUsageV2|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|totalDeviceCount|Total de dispositivos (versão prévia)|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (versão prévia)|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|configurações|Métricas de configuração|Contagem|Total|Número total de operações CRUD executadas para a configuração do dispositivo e implantação de IoT Edge em um conjunto de dispositivos de destino. Isso também inclui o número de operações que modificam o dispositivo FileMover ou o módulo n por causa dessas configurações.|Nenhum|

### <a name="dimensions"></a>Dimensões
As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de roteamento fornecem informações por ponto de extremidade. A tabela abaixo lista os valores possíveis para essas dimensões.

|Dimensão|Valores|
|---|---|
|ResourceID|o recurso do Hub IoT|
|Result|sucesso<br>falha|
|Roteamento|Mensagens do dispositivo<br>Eventos de alteração de entrelaçamento<br>Eventos de ciclo de vida do dispositivo|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>Builtin<br>blobStorage|
|FailureReasonCategory|inválido<br>passou<br>orfão<br>nulo|
|EndpointName|nome do ponto de extremidade|

## <a name="next-steps"></a>Próximas etapas

Depois da visão geral das métricas do Hub IoT, acesse este link para saber mais sobre o gerenciamento do Hub IoT do Azure:

* [Configuração dos logs de diagnóstico](iot-hub-monitor-resource-health.md)

* [Saiba como solucionar problemas de roteamento de mensagens](troubleshoot-message-routing.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
