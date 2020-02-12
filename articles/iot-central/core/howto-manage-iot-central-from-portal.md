---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do portal do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 27517c375265b552d2e1dec4d8c167d1bc86549d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137682"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar o [portal do Azure](https://portal.azure.com) para gerenciar seus aplicativos.

## <a name="create-iot-central-applications"></a>Criar aplicativos do IoT Central

Para criar um aplicativo, navegue até a [portal do Azure](https://ms.portal.azure.com) e selecione **criar um recurso**.

Em **Pesquisar na barra do Marketplace** , digite *IOT central*:

![Portal de gerenciamento: pesquisar](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o bloco **IOT central aplicativo** nos resultados da pesquisa:

![Portal de gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **criar**:

![Portal de gerenciamento: recurso do IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Esse formulário é semelhante ao formulário que você preenche para criar aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Para obter mais informações, confira o guias de início rápido [Criar um aplicativo do IoT Central](quick-deploy-iot-central.md).

![Criar formulário de IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Local** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher a localização fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. No momento, o Azure IoT Central está disponível nos **Estados Unidos**, na **Austrália**, no **Pacífico Asiático** e na **Europa**.  Depois de escolher uma localização, você não poderá mover o aplicativo posteriormente para outra localização.


Depois de preencher todos os campos, selecione **criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos do IoT Central existentes

Se você já tiver um aplicativo do Azure IoT Central, poderá excluí-lo ou movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure.

> [!NOTE]
> Você não pode ver os aplicativos criados no plano de preços gratuito no portal do Azure porque eles não estão associados à sua assinatura.

Para começar, selecione **todos os recursos** no Portal. Selecione **Mostrar tipos ocultos** e comece a digitar o nome do seu aplicativo em **Filtrar por nome** para localizá-lo. Em seguida, selecione o aplicativo IoT Central que você gostaria de gerenciar.

Para navegar até o aplicativo, selecione a **URL do aplicativo IOT central**:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, selecione **alterar** ao lado do grupo de recursos. Na página **mover recursos** , escolha o grupo de recursos para o qual você deseja mover este aplicativo:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover o aplicativo para uma assinatura diferente, selecione **alterar** ao lado da assinatura. Na página **mover recursos** , escolha a assinatura para a qual você gostaria de mover este aplicativo:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)