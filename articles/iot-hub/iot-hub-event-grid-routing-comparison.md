---
title: Comparar a Grade de Eventos, roteamento para Hub IoT | Microsoft Docs
description: O Hub IoT oferece seu próprio serviço de roteamento de mensagens, mas também integra com a Grade de Eventos para publicação de eventos. Compare os dois recursos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906795"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar roteamento de mensagens e Grade de Eventos para Hub IoT

O Azure IoT Hub oferece a capacidade de transmitir dados de seus dispositivos conectados e integrar esses dados em seus aplicativos de negócios. O Hub IoT oferece dois métodos para integrar eventos IoT em outros serviços do Azure ou aplicativos de negócios. Este artigo discute os dois recursos que fornecem essa capacidade para que você possa escolher qual é a melhor opção para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md)**: Este recurso ioT Hub permite que os usuários encaminhem mensagens dispositivo-nuvem para pontos finais de serviço, como contêineres de armazenamento Azure, hubs de eventos, filas de ônibus de serviço e tópicos de Barra de Serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de encaminhá-los aos pontos de extremidade. Além dos dados de telemetria do dispositivo, você também pode enviar [eventos de não-telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para acionar ações. 

**Integração do Hub IoT com a Grade de Eventos**: a Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. U Hub IoT e a Grade de Eventos trabalham em conjunto para [integrar os eventos do Hub IoT em serviços Azure e não-Azure](iot-hub-event-grid.md), quase em tempo real. O IoT Hub publica eventos de [dispositivos](iot-hub-event-grid.md#event-types) e eventos de telemetria.

## <a name="differences"></a>Diferenças

Embora o roteamento de mensagens e a Grade de Eventos habilitam a configuração de alerta, há algumas diferenças importantes entre os dois. Consulte a tabela a seguir para obter detalhes:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Mensagens e eventos do dispositivo** | Sim, o roteamento de mensagens pode ser usado para dados de telemetria, reportar alterações duplas do dispositivo, eventos do ciclo de vida do dispositivo e eventos de dupla mudança digital (parte da [pré-visualização pública ioT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)). | Sim, event grid pode ser usado para dados de telemetria, mas também pode relatar quando os dispositivos são criados, excluídos, conectados e desconectados do IoT Hub |
| **Ordering** | Sim, a ordenação de eventos é mantida.  | Não, a ordem dos eventos não é garantida. | 
| **Filtragem** | Filtragem avançada em propriedades do aplicativo de mensagens, propriedades do sistema de mensagens, corpo da mensagem, tags de gêmeos de dispositivo e propriedades de gêmeos de dispositivo. A filtragem não é aplicada a eventos de dupla mudança digital. Para exemplos, consulte [Sintaxe de Consulta de Roteamento de Mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem com base no tipo de evento, tipo de assunto e atributos em cada evento. Por exemplo, consulte [Entender eventos de filtragem em Assinaturas de Grade de Eventos](../event-grid/event-filtering.md). Ao assinar eventos de telemetria, você pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, corpo de mensagem e dispositivo gêmeo em seu IoT Hub, antes de publicar no Event Grid. [Veja como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Extremidade** | <ul><li>Hubs de Eventos</li> <li>Armazenamento do Blobs do Azure</li> <li>Fila do Barramento de Serviço</li> <li>Tópicos do Barramento de Serviço</li></ul><br>As SKUs do Hub IoT pagas (S1, S2 e S3) estão limitadas a 10 pontos de extremidades personalizados. 100 rotas podem ser criadas por Hub IoT. | <ul><li>Funções do Azure</li> <li>Automação do Azure</li> <li>Hubs de Eventos</li> <li>Aplicativos Lógicos</li> <li>Blob de Armazenamento</li> <li>Tópicos personalizados</li> <li>Armazenamento de Filas</li> <li>Microsoft Flow</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>500 pontos finais por IoT Hub são suportados. Para obter a lista mais atualizada de pontos de extremidades, consulte [Manipuladores de eventos da Grade de Eventos](../event-grid/overview.md#event-handlers). |
| **Custo** | Não há encargos separados para roteamento de mensagens. Somente o ingresso de telemetria no Hub IoT é cobrado. Por exemplo, se você tiver uma mensagem roteada para três pontos de extremidades diferentes, você será cobrado por apenas uma mensagem. | Não há nenhum custo do Hub IoT. A Event Grid oferece as primeiras 100.000 operações por mês gratuitamente e, em seguida, US$ 0,60 por milhão de operações depois. |

## <a name="similarities"></a>Semelhanças

O roteamento de mensagens do Hub IoT e a Grade de Eventos também têm semelhanças, algumas das quais detalhadas na tabela a seguir:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para nuvem | 256 KB, dispositivo para nuvem |
| **Confiabilidade** | Alta: entrega cada mensagem ao ponto de extremidade pelo menos uma vez por cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alta: entrega cada mensagem ao webhook pelo menos uma vez por cada assinatura. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alta: otimizado para suportar milhões de dispositivos conectados simultaneamente enviando bilhões de mensagens. | Alto: capaz de rotear 10.000.000 eventos por segundo por região. |
| **Latência** | Baixa: quase em tempo real. | Baixa: quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  
| **Segurança** | O HUB Iot fornece identidade por dispositivo e controle de acesso revogável. Para obter mais informações, consulte o [Controle de acesso do Hub IoT](iot-hub-devguide-security.md). | A Grade de Eventos fornece validação em três pontos: assinaturas de eventos, publicação de eventos e entrega de eventos do webhook. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). |

## <a name="how-to-choose"></a>Como escolher

O roteamento de mensagens do Hub IoT e a integração do Hub IoT com a Grade de Eventos realizam ações diferentes para obter resultados semelhantes. Ambos tomam informações da solução do Hub IoT e informam para que outros serviços possam reagir. Então, como você decide qual deles usar? Considere as seguintes perguntas para ajudar a orientar sua decisão: 

* **Que tipo de dados você está enviando para os pontos de extremidade?**

   Use o roteamento de mensagens do Hub IoT quando for necessário enviar dados telemétricos para outros serviços. O roteamento de mensagens também permite consultar o aplicativo de mensagens e as propriedades do sistema, o corpo da mensagem, as tags gêmeas do dispositivo e as propriedades gêmeas do dispositivo.

   A integração do Hub IoT com a Grade de Eventos funciona com os eventos que ocorrem no serviço do Hub IoT. Esses eventos do IoT Hub incluem dados de telemetria, dispositivo criado, excluído, conectado e desconectado. Ao assinar eventos de telemetria, você pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, corpo de mensagem e dispositivo gêmeo em seu IoT Hub, antes de publicar no Event Grid. [Veja como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quais pontos de extremidade precisam receber essas informações?**

   O roteamento de mensagens do IoT Hub suporta um número limitado de pontos finais exclusivos e tipos de ponto final, mas você pode criar conectores para redirecionar os dados e eventos para pontos finais adicionais. Para obter uma lista completa dos pontos de extremidade com suporte, consulte a tabela na seção anterior. 

   A integração do IoT Hub com o Event Grid suporta 500 pontos finais por IoT Hub e uma variedade maior de tipos de endpoint. Ele integra-se nativamente com as funções do Azure, aplicativos lógicos, filas de armazenamento e barramento de serviço, e também trabalha com webhooks para estender o envio de dados fora do ecossistema de serviços do Azure e em aplicativos de negócios de terceiros.

* **É importante os dados chegarem em ordem?**

   O roteamento de mensagens do Hub IoT mantém a ordem em que as mensagens são enviadas para que elas cheguem da mesma maneira.

   A Grade de Eventos não garante que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. Para aqueles casos em que a ordem absoluta das mensagens é significativa e/ou em que um consumidor precisa de um identificador exclusivo confiável para mensagens, recomendamos o uso do roteamento de mensagens. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o Roteamento de Mensagens do Hub de IoT](iot-hub-devguide-messages-d2c.md) e os pontos finais do [IoT Hub](iot-hub-devguide-endpoints.md).
* Saiba mais sobre a [Grade de Eventos do Azure](../event-grid/overview.md).
* Para aprender como criar Rotas de Mensagens, confira o tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas](../iot-hub/tutorial-routing.md).
* Experimente a integração da Event Grid [enviando notificações por e-mail sobre eventos do Azure IoT Hub usando aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).
