---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76963641"
---
Para maximizar o desempenho, use uma conta de armazenamento separada para cada aplicativo de funções. Isso é particularmente importante quando você tem Durable Functions ou funções disparadas pelo hub de eventos, que geram um alto volume de transações de armazenamento. Quando a lógica do aplicativo interage com o armazenamento do Azure, seja diretamente (usando o SDK de armazenamento) ou por meio de uma das associações de armazenamento, você deve usar uma conta de armazenamento dedicada. Por exemplo, se você tiver uma função disparada por Hub de eventos gravando alguns dados no armazenamento de BLOBs, use duas contas de armazenamento &mdash; uma para o aplicativo de funções e outra para os blobs que estão sendo armazenados pela função.