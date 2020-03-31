---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963641"
---
Para maximizar o desempenho, use uma conta de armazenamento separada para cada aplicativo de função. Isso é particularmente importante quando você tem funções duráveis ou funções desencadeadas pelo Event Hub, que geram um alto volume de transações de armazenamento. Quando a lógica do aplicativo interage com o Azure Storage, diretamente (usando o SDK de armazenamento) ou através de uma das vinculações de armazenamento, você deve usar uma conta de armazenamento dedicada. Por exemplo, se você tiver uma função desencadeada pelo Event Hub escrevendo&mdash;alguns dados para blob armazenamento, use duas contas de armazenamento uma para o aplicativo de função e outra para as bolhas que estão sendo armazenadas pela função.