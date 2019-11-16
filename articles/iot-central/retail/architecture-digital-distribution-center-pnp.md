---
title: Arquitetura do Centro de Distribuição Digital para IoT Central | Microsoft Docs
description: Uma arquitetura do modelo de aplicativo do Centro de Distribuição Digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 478ebde1de5624796ebf1dde5cf89f4e1f9d9104
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890733"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arquitetura do modelo de aplicativo do centro de distribuição digital para IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Os parceiros e clientes podem aproveitar o modelo de aplicativo e as diretrizes a seguir para desenvolver soluções de **centro de distribuição digital** de ponta a ponta.

> [!div class="mx-imgBorder"]
> ![centro de distribuição digital](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Conjunto de sensores de IoT que enviam dados de telemetria para um dispositivo de gateway
2. Dispositivos de gateway que enviam telemetria e informações agregadas para o IoT Central
3. Os dados são roteados para o serviço do Azure desejado para fins de manipulação
4. Os serviços do Azure, como ASA ou Azure Functions, podem ser aproveitados para reformatar os fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são guardados no armazenamento frequente para ações quase em tempo real ou armazenamento frio, para aprimoramentos de insights adicionais com base na análise de ML ou em lote. 
6. Os Aplicativos Lógicos podem ser usados para alimentar vários fluxos de trabalho de negócios em aplicativos de negócios do usuário final

## <a name="details"></a>Detalhes
A seção a seguir descreve cada parte da arquitetura conceitual

## <a name="video-cameras"></a>Câmeras de vídeo 
As câmeras de vídeo são os principais sensores nesse ecossistema de escala empresarial conectado digitalmente. Avanços no aprendizado de máquina e inteligência artificial que permitem que o vídeo seja transformado em dados estruturados e processe-os na borda antes de enviar para a nuvem. Podemos usar câmeras IP para capturar imagens, compactá-las na câmera e, em seguida, enviar os dados compactados na computação de borda para o pipeline de análise de vídeo ou usar câmeras de visão GigE para capturar imagens no sensor e enviá-las diretamente para o Azure IoT Edge, que são compactadas antes do processamento no pipeline de análise de vídeo. 

## <a name="azure-iot-edge-gateway"></a>Gateway do Azure IoT Edge
As "câmeras como sensores" e as cargas de trabalho de borda são gerenciadas localmente pelo Azure IoT Edge e o fluxo da câmera é processado pelo pipeline de análise. O pipeline de processamento de análise de vídeo no Azure IoT Edge traz inúmeros benefícios, incluindo o tempo de resposta reduzido, o consumo de baixa largura de banda, resultando em baixa latência para processamento rápido de dados. Somente os metadados, insights ou as ações mais essenciais são enviadas para a nuvem para ação ou investigação posterior. 

## <a name="device-management-with-iot-central"></a>Gerenciamento de dispositivos com IoT Central 
O Azure IoT Central é uma plataforma de desenvolvimento de solução que simplifica a conectividade, a configuração e o gerenciamento do dispositivo IoT e do gateway do Azure IoT Edge. A plataforma reduz significativamente a carga e os custos de gerenciamento, operações e desenvolvimentos relacionados de dispositivos IoT. Os clientes e parceiros podem criar soluções empresariais de ponta a ponta para obter um loop de comentário digital nos centros de distribuição.

## <a name="business-insights--actions-via-data-egress"></a>Insight de negócios & ações por meio da saída de dados 
A plataforma IoT Central fornece opções avançadas de extensibilidade por meio de CDE (Exportação de Dados Contínua) e APIs. Os insights de negócios baseados no processamento de dados de telemetria ou na telemetria bruta são exportadas normalmente para um aplicativo de linha de negócios preferencial. Isso pode ser obtido por meio de webhook, barramento de serviço, hub de eventos ou armazenamento de blob para criar, treinar e implantar modelos de aprendizado de máquina e enriquecer ainda mais os insights.

## <a name="next-steps"></a>Próximas etapas
* Aprenda a implantar um [modelo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Saiba mais sobre [modelos comerciais do IoT Central](./overview-iot-central-retail-pnp.md)
* Para saber mais sobre o IoT Central, confira [Visão geral do IoT Central](../preview/overview-iot-central.md)
