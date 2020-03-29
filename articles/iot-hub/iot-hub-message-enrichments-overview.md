---
title: Visão geral dos enriquecimentos de mensagens do Azure IoT Hub
description: Este artigo mostra enriquecimentos de mensagens, que dão ao IoT Hub a capacidade de carimbar mensagens com informações adicionais antes que as mensagens sejam enviadas para o ponto final designado.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429114"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Enriquecimento de mensagens para mensagens ioT hub de dispositivo para nuvem

*Enriquecimento de mensagens* é a capacidade do IoT Hub de *carimbar* mensagens com informações adicionais antes que as mensagens sejam enviadas para o ponto final designado. Uma das razões para usar enriquecimentos de mensagens é incluir dados que podem ser usados para simplificar o processamento a jusante. Por exemplo, enriquecer mensagens de telemetria de dispositivos com uma tag dupla de dispositivo pode reduzir a carga nos clientes para fazer chamadas de API gêmeas para essas informações.

![Fluxo de enriquecimento de mensagens](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Um enriquecimento de mensagem tem três elementos-chave:

* Nome de enriquecimento ou chave

* Um valor

* Um ou mais [pontos finais](iot-hub-devguide-endpoints.md) para os quais o enriquecimento deve ser aplicado.

A **chave** é uma corda. Uma chave só pode conter caracteres alfanuméricos`-`ou`_`esses caracteres`.`especiais: hífen ( ), sublinhar ( ) e período ( ).

O **valor** pode ser qualquer um dos seguintes exemplos:

* Qualquer corda estática. Valores dinâmicos como condições, lógica, operações e funções não são permitidos. Por exemplo, se você desenvolver um aplicativo SaaS que é usado por vários clientes, você pode atribuir um identificador a cada cliente e disponibilizar esse identificador no aplicativo. Quando o aplicativo for executado, o IoT Hub carimbará as mensagens de telemetria do dispositivo com o identificador do cliente, possibilitando processar as mensagens de forma diferente para cada cliente.

* O nome do hub ioT enviando a mensagem. Este valor é *$iothubname*.

* Informações do dispositivo gêmeo, como seu caminho. Exemplos seriam *$twin.tags.field* e *$twin.tags.latitude*.

   > [!NOTE]
   > Neste momento, apenas $iothubname, $twin.tags, $twin.properties.desired e $twin.properties.reported são variáveis suportadas para enriquecimento de mensagens.

Os enriquecimentos de mensagens são adicionados à medida que as propriedades do aplicativo são enviadas para o ponto final escolhido.  

## <a name="applying-enrichments"></a>Aplicando enriquecimentos

As mensagens podem vir de qualquer fonte de dados suportada pelo [roteamento de mensagens do IoT Hub,](iot-hub-devguide-messages-d2c.md)incluindo os seguintes exemplos:

* telemetria do dispositivo, como temperatura ou pressão
* notificações de alteração dupla do dispositivo - alterações no dispositivo gêmeo
* eventos do ciclo de vida do dispositivo, como quando o dispositivo é criado ou excluído

Você pode adicionar enriquecimentos às mensagens que estão indo para o ponto final incorporado de um Hub IoT ou mensagens que estão sendo roteadas para pontos finais personalizados, como armazenamento Azure Blob, uma fila de Ônibus de Serviço ou um tópico de Ônibus de Serviço.

Você pode adicionar enriquecimentos às mensagens que estão sendo publicadas no Event Grid selecionando o ponto final como Event Grid. Criamos uma rota padrão no IoT Hub para telemetria de dispositivos, com base na sua assinatura event grid. Esta única rota pode lidar com todas as suas assinaturas do Event Grid. Você pode configurar enriquecimentos para o ponto final da grade de eventos depois de ter criado a assinatura da grade de eventos para a telemetria do dispositivo. Para obter mais informações, consulte [Iot Hub e Event Grid](iot-hub-event-grid.md).

Enriquecimentos são aplicados por ponto final. Se você especificar cinco enriquecimentos a serem carimbados para um ponto final específico, todas as mensagens que vão para esse ponto final são carimbadas com os mesmos cinco enriquecimentos.

Os enriquecimentos podem ser configurados usando os seguintes métodos:

| **Método** | **Comando** |
| ----- | -----| 
| Portal | [Portal Azure](https://portal.azure.com) | Veja o tutorial de [enriquecimento de mensagens](tutorial-message-enrichments.md) | 
| CLI do Azure   | [az iot hub mensagem-enriquecimento](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Adicionar enriquecimentos de mensagens não adiciona latência ao roteamento da mensagem.

Para experimentar enriquecimentos de mensagens, veja o [tutorial de enriquecimentos de mensagens](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitações

* Você pode adicionar até 10 enriquecimentos por IoT Hub para esses hubs no nível padrão ou básico. Para Hubs IoT no nível gratuito, você pode somar até 2 enriquecimentos.

* Em alguns casos, se você estiver aplicando um enriquecimento com um valor definido para uma tag ou propriedade no dispositivo gêmeo, o valor será carimbado como um valor de cadeia. Por exemplo, se um valor de enriquecimento for definido como $twin.tags.field, as mensagens serão carimbadas com a seqüência "$twin.tags.field" em vez do valor desse campo a partir do gêmeo. Isso acontece nos seguintes casos:

   * Seu Hub IoT está no nível básico. Os hubs básicos de IoT de nível não suportam gêmeos de dispositivo.

   * Seu IoT Hub está no nível padrão, mas o dispositivo que envia a mensagem não tem nenhum dispositivo gêmeo.

   * Seu IoT Hub está no nível padrão, mas o caminho duplo do dispositivo usado para o valor do enriquecimento não existe. Por exemplo, se o valor de enriquecimento estiver definido como $twin.tags.location e o dispositivo gêmeo não tiver uma propriedade de localização tags, a mensagem será carimbada com a seqüência $twin.tags.location. 

* Atualizações para um dispositivo gêmeo podem levar até cinco minutos para serem refletidas no valor de enriquecimento correspondente.

* O tamanho total da mensagem, incluindo os enriquecimentos, não pode exceder 256 KB. Se um tamanho de mensagem exceder 256 KB, o IoT Hub soltará a mensagem. Você pode usar [métricas do IoT Hub](iot-hub-metrics.md) para identificar e depurar erros quando as mensagens são retiradas. Por exemplo, você pode monitorar d2c.telemetria.egress.inválida.

* Os enriquecimentos de mensagens não se aplicam a eventos de mudança dupla digital (parte da [pré-visualização pública IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)).

## <a name="pricing"></a>Preços

Os enriquecimentos de mensagens estão disponíveis sem custo adicional. Atualmente, você é cobrado quando envia uma mensagem para um Hub IoT. Você só é cobrado uma vez por essa mensagem, mesmo que a mensagem vá para vários pontos finais.

## <a name="next-steps"></a>Próximas etapas

Confira esses artigos para obter mais informações sobre o roteamento de mensagens para um Hub ioT:

* [Tutorial de enriquecimento de mensagens](tutorial-message-enrichments.md)

* [Use o roteamento de mensagens do IoT Hub para enviar mensagens de dispositivo para nuvem para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)

* [Tutorial: Roteamento do IoT Hub](tutorial-routing.md)
