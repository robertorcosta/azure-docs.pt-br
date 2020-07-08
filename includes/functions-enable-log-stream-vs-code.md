---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "68669507"
---
Para ativar os logs de streaming para seu aplicativo de funções no Azure:

1. Selecione F1 para abrir a paleta de comandos e, em seguida, pesquise e execute o comando **Azure Functions: iniciar logs de streaming**.

1. Selecione seu aplicativo de funções no Azure e, em seguida, selecione **Sim** para habilitar o log de aplicativo para o aplicativo de funções.

1. Dispare suas funções no Azure. Observe que os dados de log são exibidos na janela saída no Visual Studio Code.

1. Quando terminar, lembre-se de executar o comando **Azure Functions: parar os logs de streaming** para desabilitar o registro em log para o aplicativo de funções.