---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do portal do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c86df7c50e59309f921c60738870407e74a23219
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555211"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar o [portal do Azure](https://portal.azure.com) para gerenciar seus aplicativos.

## <a name="create-iot-central-applications"></a>Criar aplicativos do IoT Central

Para criar um aplicativo, navegue até a [portal do Azure](https://ms.portal.azure.com) e selecione **criar um recurso** no painel principal à esquerda.

![Portal de gerenciamento: menu de navegação](media/howto-manage-iot-central-from-portal/image0.png)

Na barra de pesquisa, digite **IoT Central**.

![Portal de gerenciamento: pesquisar](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione a **IOT central** item de linha de aplicativo nos resultados da pesquisa.

![Portal de gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **Criar**.

![Portal de gerenciamento: recurso do IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Esse formulário é semelhante ao formulário que você preenche para criar aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Para obter mais informações, confira o guias de início rápido [Criar um aplicativo do IoT Central](quick-deploy-iot-central.md).

Você pode criar IoT Central aplicativo com recursos geralmente disponíveis selecionando **exemplo contoso**, **aplicativo personalizado** e **exemplo devkits** como modelos de aplicativos, todos os outros modelos de aplicativos usam recursos de visualização pública.

![Criar formulário de IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Local** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher o local que está fisicamente mais próximo de seus dispositivos para obter o desempenho ideal. No momento, o Azure IoT Central está disponível no **Estados Unidos**, na **austrália**, no **Pacífico Asiático**ou na **Europa**.  Depois de escolher um local, você não poderá mover seu aplicativo para um local diferente mais tarde.

> [!NOTE]
> Os modelos de aplicativo de visualização atualmente só estão disponíveis nos locais da **Europa** e **Estados Unidos** .

![Portal de gerenciamento: criar recurso do IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Depois de preencher todos os campos, selecione **criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos do IoT Central existentes

Se você já tiver um aplicativo do Azure IoT Central, poderá excluí-lo ou movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure.

> [!NOTE]
> Você não pode ver aplicativos de avaliação no portal do Azure porque eles não estão associados com sua assinatura.

Para começar, selecione **todos os recursos** no painel principal à esquerda. Use a caixa de pesquisa para digitar o nome do aplicativo para localizá-lo na lista de recursos. Em seguida, selecione o aplicativo IoT Central que você gostaria de gerenciar.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image2a.png)

Para navegar até o aplicativo, selecione a URL do aplicativo IoT Central.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, selecione **alterar** ao lado do grupo de recursos. Na página **Mover recursos**, escolha o grupo de recursos para o qual você deseja migrar esse aplicativo.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover o aplicativo para uma assinatura diferente, selecione o link **alterar** ao lado da assinatura. Escolha a assinatura para a qual você deseja migrar esse aplicativo na caixa de diálogo exibida.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)