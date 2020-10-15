---
title: Implantar o console de gerenciamento no Azure defender para IoT
description: Saiba mais sobre como implantar o console de gerenciamento no Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094233"
---
# <a name="deploy-the-management-console"></a>Implantar o console de gerenciamento
Este artigo descreve como implantar o console de gerenciamento local do Azure defender para IoT.

## <a name="the-on-premises-management-console"></a>O console de gerenciamento local

O console de gerenciamento local fornece uma exibição consolidada de todos os ativos de rede e fornece uma exibição em tempo real dos indicadores de risco e alertas principais de IoT e de conta em todos os seus recursos. Totalmente integrado aos seus fluxos de trabalho do SOC e execução de livros, ele permite uma priorização fácil de atividades de mitigação e correlação entre sites de ameaças.

- Holística – reduza a complexidade com uma única plataforma unificada para gerenciamento de ativos, risco e gestão de vulnerabilidades, bem como monitoramento de ameaças com resposta a incidentes.

- Agregação e correlação – exibem, agregam e analisam dados e alertas coletados de todos os sites.

- Controlar todos os sensores – configure e monitore todos os sensores de um único local.

   ![Exibição de gerenciamento de sites do Azure defender para IoT](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Implantar o dispositivo de console de gerenciamento local

Esse processo requer a aquisição de seu próprio hardware e a instalação do software. A solução é executada em dispositivos certificados. Consulte o [Guia de especificações de hardware do Azure defender para IOT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) como referência ao adquirir seu dispositivo certificado.

Consulte o [Guia de instalação do Azure defender para IOT](https://aka.ms/AzureDefenderforIoTInstallSensorISO) para obter detalhes sobre como baixar a imagem ISO e instalar o software do sensor.

**Para implantar o console de gerenciamento local:**

1. Navegue até Microsoft Azure defender para IoT.

2. Selecione **console de gerenciamento local**.

   ![Exibição do console de gerenciamento local do Azure defender para IoT](media/updates/image15.png)

3. Selecione a versão 3,1 no menu **selecionar versão** .

4. Selecione **baixar** e salve o arquivo.

5. Depois que o software é instalado, execute as tarefas de configuração de rede. Consulte o [Guia de configuração de rede do Azure defender para IOT](https://aka.ms/AzureDefenderForIoTNetworkSetup) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, vá para o guia de instruções para configuração de módulo.
> [!div class="nextstepaction"]
> [Guia de instruções de configuração de módulo](./how-to-agent-configuration.md)
