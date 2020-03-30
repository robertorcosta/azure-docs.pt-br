---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos IoT Central a partir do portal Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157918"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site do gerenciador de [aplicativos Azure IoT Central,](https://aka.ms/iotcentral) você pode usar o [portal Azure](https://portal.azure.com) para gerenciar seus aplicativos.

## <a name="create-iot-central-applications"></a>Criar aplicativos do IoT Central

Para criar um aplicativo, navegue até o [portal Azure](https://ms.portal.azure.com) e selecione **Criar um recurso**.

Em Pesquisar a barra **de marketplace,** *digite IoT Central*:

![Portal de gerenciamento: pesquisar](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o azulejo **do aplicativo Central ioT** nos resultados da pesquisa:

![Portal de gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **Criar**:

![Portal de gerenciamento: recurso do IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Este formulário é semelhante ao formulário que você preenche para criar aplicativos no site do [gerenciador de aplicativos Azure IoT Central.](https://aka.ms/iotcentral) Para obter mais informações, confira o guias de início rápido [Criar um aplicativo do IoT Central](quick-deploy-iot-central.md).

![Criar formulário Central de IoT](media/howto-manage-iot-central-from-portal/image6a.png)

**Localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) em que você deseja criar seu aplicativo. Normalmente, você deve escolher a localização fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. O Azure IoT Central está atualmente disponível nas regiões da **Austrália,** **Ásia-Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido**e **Japão.** Depois de escolher uma localização, você não poderá mover o aplicativo posteriormente para outra localização.

Depois de preencher todos os campos, selecione **Criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos do IoT Central existentes

Se você já tiver um aplicativo do Azure IoT Central, poderá excluí-lo ou movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure.

> [!NOTE]
> Você não pode ver aplicativos criados no plano de preços gratuitos no portal Azure porque eles não estão associados à sua assinatura.

Para começar, selecione **Todos os recursos** do portal. Selecione **Mostrar tipos ocultos** e comece a digitar o nome do seu aplicativo em **Filtro por nome** para encontrá-lo. Em seguida, selecione o aplicativo IoT Central que você gostaria de gerenciar.

Para navegar até o aplicativo, selecione a URL do **aplicativo central ioT**:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, selecione **a alteração** ao lado do grupo de recursos. Na página **Recursos do Move,** escolha o grupo de recursos que deseja mover este aplicativo para:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover o aplicativo para uma assinatura diferente, selecione **alterar** ao lado da assinatura. Na página **de recursos do Move,** escolha a assinatura que deseja mover este aplicativo para:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)