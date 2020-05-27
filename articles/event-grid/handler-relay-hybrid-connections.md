---
title: Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as conexões híbridas da Retransmissão do Azure como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596105"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e a **Retransmissão do Azure** é um deles. 

Use as **Conexões Híbridas de Retransmissão** do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não têm um ponto de extremidade publicamente acessível.

## <a name="tutorials"></a>Tutoriais
Consulte o tutorial a seguir para ter um exemplo de uso da conexão híbrida da Retransmissão do Azure como um manipulador de eventos. 

|Title  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de escuta. |

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 