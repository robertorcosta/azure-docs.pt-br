---
title: 'Tutorial: Como implantar o modelo de aplicativo do Azure IoT Central de detecção de objetos e movimentos e análise de vídeo'
description: Tutorial – este guia resume as etapas para implantar um aplicativo do Azure IoT Central usando o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: abeae674cef8f13a092b53bde6040ad853bd0cca
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820357"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Tutorial: Como implantar um aplicativo do IoT Central usando o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo

Para obter uma visão geral dos principais componentes do aplicativo de *detecção de objetos e movimentos e análise de vídeo*, confira a [arquitetura do aplicativo de detecção de objetos e movimentos e análise de vídeo](architecture-video-analytics.md).

O seguinte vídeo fornece uma explicação sobre como usar o _modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo_ para implantar uma solução do IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Implantar o aplicativo

Conclua as seguintes etapas para implantar um aplicativo do IoT Central usando o modelo de aplicativo de análise de vídeo:

1. Conclua o tutorial [Criar um aplicativo de análise de vídeo no Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou o [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para:
    - Criar uma conta de Serviços de Mídia do Azure.
    - Criar o aplicativo do IoT Central com o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
    - Configurar um dispositivo de gateway no aplicativo do IoT Central. O gateway permite que os dispositivos de câmera se conectem ao aplicativo.

1. Conclua o tutorial [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](tutorial-video-analytics-iot-edge-vm.md) ou o [Tutorial: Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) para:
    - Crie uma VM do Azure com o runtime do Azure IoT Edge instalado. Prepare a instalação do IoT Edge para hospedar o módulo de análise de vídeo.
    - Conecte o dispositivo do IoT Edge ao aplicativo do IoT Central.

1. Conclua o tutorial [Monitorar e gerenciar um aplicativo de análise de vídeo](tutorial-video-analytics-manage.md) para:
    - Adicionar câmeras de detecção de objetos e movimentos ao gateway no aplicativo do IoT Central.
    - Iniciar o processamento da câmera.
    - Instalar um player de mídia local para exibir o vídeo capturado no AMS.
    - Exibir o vídeo capturado que mostra objetos detectados.
    - Organizar.

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral das etapas para implantar e usar o modelo de aplicativo de análise de vídeo, consulte

> [!div class="nextstepaction"]
> [Criar um aplicativo de análise de vídeo no Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou

> [!div class="nextstepaction"]
> [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para começar.
