---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68669507"
---
Para ativar os logs de streaming do seu aplicativo de função no Azure:

1. Selecione F1 para abrir a paleta de comando e, em seguida, procure e execute o comando **Funções Azure: Inicie logs de streaming**.

1. Selecione seu aplicativo de função no Azure e, em seguida, **selecione Sim** para ativar o registro de aplicativos para o aplicativo de função.

1. Acione suas funções no Azure. Observe que os dados de log são exibidos na janela Saída no Visual Studio Code.

1. Quando terminar, lembre-se de executar o comando **Funções do Azure: Pare de fazer logs de streaming** para desativar o registro do aplicativo de função.