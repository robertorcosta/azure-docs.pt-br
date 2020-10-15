---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876595"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Criar o dispositivo de gateway do Azure IoT Edge

O aplicativo de detecção de objetos e movimentos e análise de vídeo inclui um modelo de dispositivo **Detector de Objetos de Borda de LVA** e um modelo de dispositivo **Detector de Movimentos de Borda de LVA**. Nesta seção, você criará um modelo de dispositivo de gateway usando o manifesto de implantação e adicionará o dispositivo de gateway ao seu aplicativo do IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Criar um modelo de dispositivo para o Gateway de Borda de LVA

Para importar o manifesto de implantação e criar o modelo de dispositivo **Gateway de Borda de LVA**:

1. No aplicativo do IoT Central, procure **Modelos de Dispositivo** e selecione **+ Novo**.

1. Na página **Selecionar tipo de modelo**, selecione o bloco **Azure IoT Edge**. Em seguida, selecione **Avançar: Personalizar**.

1. Na página **Carregar um manifesto de implantação do Azure IoT Edge**, insira *Gateway de Borda de LVA* como o nome do modelo e marque a opção **Dispositivo de gateway com dispositivos downstream**.

    Não procure o manifesto de implantação ainda. Se você fizer isso, o assistente de implantação vai esperar uma interface para cada módulo, mas você só precisará expor a interface do **LvaEdgeGatewayModule**. Você carregará o manifesto em uma etapa posterior.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Não carregue o manifesto de implantação":::

    Selecione **Avançar: Análise**.

1. Na página **Examinar**, selecione **Criar**.

### <a name="import-the-device-capability-model"></a>Importar o modelo de funcionalidade do dispositivo

O modelo de dispositivo precisa incluir um modelo de funcionalidade do dispositivo. Na página **Gateway de Borda de LVA**, selecione o bloco **Importar modelo de funcionalidade**. Procure a pasta *lva-configuration* criada anteriormente e selecione o arquivo *LvaEdgeGatewayDcm.json*.

O modelo de dispositivo **Gateway de Borda de LVA** agora inclui o **Módulo do Gateway de Borda de LVA** juntamente com três interfaces: **Informações do dispositivo**, **Configurações do Gateway de Borda de LVA** e **Interface do Gateway de Borda de LVA**.
