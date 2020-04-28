---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "66248839"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os [grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem exibições independentes no fluxo de eventos que permitem que aplicativos e serviços do Azure consumam dados de forma independente do mesmo ponto de extremidade do hub de eventos. Nesta seção, você adiciona um grupo de consumidores ao ponto de extremidade interno do Hub IoT que é usado posteriormente neste tutorial para efetuar pull de dados do ponto de extremidade.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **pontos de extremidade internos**, selecione **eventos** no painel direito e insira um nome em **grupos de consumidores**. Selecione **Salvar**.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
