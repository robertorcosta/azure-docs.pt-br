---
title: Fila de armazenamento como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as filas de armazenamento do Azure como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596265"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Fila de armazenamento como um manipulador de eventos para os eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos e o **Armazenamento de Filas do Azure** é um deles. 

Use o **Armazenamento de Filas** para receber eventos que precisam efetuar pull. Você pode usar o Armazenamento de Filas quando tem um processo de execução longa que demora muito para responder. Ao enviar eventos para o Armazenamento de Filas, o aplicativo pode receber e processar os eventos de acordo com a própria agenda.

## <a name="tutorials"></a>Tutoriais
Consulte o tutorial a seguir para ter um exemplo de uso do Armazenamento de filas como um manipulador de eventos. 

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: encaminhar eventos personalizados para o Armazenamento de Filas do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
