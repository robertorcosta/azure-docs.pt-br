---
title: Folha de dados da pesquisa visual do Azure Percept
description: Confira a folha de detalhes do Azure Percept Vision para obter especificações detalhadas do dispositivo
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661725"
---
# <a name="azure-percept-vision-datasheet"></a>Folha de dados da pesquisa visual do Azure Percept

As especificações listadas abaixo são para o dispositivo Azure Percept Vision, incluído no [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Especificação do produto           |Valor     |
|--------------------------------|---------------------|
|Setores-alvo               |Produção <br> Prédios inteligentes <br> Auto <br> Varejo |
|Cenários de Heroes                  |Análise do comprador <br> Disponibilidade na prateleira <br> Reduzir redução <br> Segurança/vigilância <br> Segurança do local de trabalho|
|Dimensões                      |42 mm x 42 mm x 40mm (assembly de SoM do Azure Percept Vision com hospedagem) <br> 42 mm x 42 mm x 6mm (chip de SoM de visão)|
|Plano de controle de gerenciamento        |ADU (atualização de dispositivo do Azure)          |
|Software e serviços com suporte |[Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Atualização de dispositivo do Azure |
|Aceleração de ia                 |Unidade de processamento de visão (VPU) Intel Movidius infinidade X (MA2085) com o ISP de câmera Intel integrado, 0,7 TOPS |
|Sensores e indicadores visuais   |Câmera Omni Vision 5670 <br> GSO GS8882AA Lens (resolução: 5MP às 30FPS, Distance: 50cm-Infinity, FoV: 120 graus, cor: amplo intervalo dinâmico, obturador de foco fixo)          |
|Suporte à câmera                  |Câmeras RGB, IR e profundidade <br> 2 câmeras podem ser executadas simultaneamente |
|Crypto-Controller de segurança      |ST-Micro STM32L462CE      |
|Componente de ID/versão       |EEPROM de 64 KB |
|Memória                          |LPDDR4 DE 2GB     |
|Energia                           |3,5 W     |
|Portas                           |1x USB 3,0 tipo C <br> 2x MIPI 4 Lane (até 1,5 Gbps por Lane)     |
|Interfaces de controle              |2x I2C <br> SPI 2x <br> 6 vezes PWM (GPIOs: 2x Clock, 2x quadro Sync, 2x não utilizado) <br> GPIO de reposição de 2x |
|Certificação                   |CE <br> NORMAS      |
|Temperatura operacional           |0 a 27 graus C (assembly de SoM do Azure Percept Vision com hospedagem) <br> -10 a 70 graus C (chip de SoM de visão) |
|Temperatura do toque               |<= 48 graus C |
|Umidade relativa               |8% a 90%    |