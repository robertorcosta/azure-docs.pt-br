---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84732457"
---
1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione **Azure Functions: implantar para aplicativo de funções**.

1. Se você não estiver conectado, será solicitado a **Entrar no Azure**. Após você entrar pelo navegador, retorne ao Visual Studio Code. Se você tiver várias assinaturas, **selecione uma assinatura** que contenha o aplicativo de funções.

1. Selecione seu aplicativo de funções existente no Azure. Quando você for avisado sobre a substituição de todos os arquivos no aplicativo de funções, selecione **Implantar** para reconhecer o aviso e continuar.

O projeto é recriado, reempacotado e carregado no Azure. O projeto existente é substituído pelo novo pacote e o aplicativo de funções é reiniciado.