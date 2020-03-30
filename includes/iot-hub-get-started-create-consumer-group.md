---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248839"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

[Grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem visualizações independentes sobre o fluxo de eventos que permitem que aplicativos e serviços do Azure consumam independentemente dados do mesmo ponto final do Event Hub. Nesta seção, você adiciona um grupo de consumidores ao ponto final incorporado do seu hub de IoT que é usado mais tarde neste tutorial para extrair dados do ponto final.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **Pontos finais incorporados,** selecione **Eventos** no painel direito e digite um nome em grupos **de consumo**. Selecione **Salvar**.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
