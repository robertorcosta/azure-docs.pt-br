---
title: Opções de implantação
description: Comece a compreender o fluxo de trabalho básico do defender para recursos e serviços de IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090056"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Introdução ao Azure defender para IoT

Este artigo descreve os processos de implantação e integração necessários para obter o Azure defender para IoT em execução. Também são necessárias etapas adicionais. É recomendável que você compreenda essas etapas e se familiarize com as informações em documentos que o acompanham.

Depois de concluir todas as etapas, os sensores do Azure defender para IoT monitorarão sua rede. Dependendo de como você configura sua solução, as detecções também podem ser enviadas para o console de gerenciamento local ou para o Hub IoT.

Conclua as etapas a seguir para colocar o Azure defender para IoT em funcionamento.

## <a name="1-set-up-azure"></a>1. configurar o Azure

- Configure uma conta do Azure. Para obter mais informações, consulte [criar uma conta do Azure](https://docs.microsoft.com/learn/modules/create-an-azure-account/).

- Firewall ou proxy: se você tiver um firewall ou dispositivo de rede intermediário semelhante configurado para permitir conexões específicas, verifique se *. azure-devices.net:443 está aberto no firewall ou proxy. Se não houver suporte para caracteres curinga ou se você quiser mais controle, o FQDN do Hub IoT específico deverá ser aberto em seu FW ou proxy. Para obter mais informações, consulte [referência-pontos de extremidade do Hub IOT](/azure/iot-hub/iot-hub-devguide-endpoints).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. implantar hardware, software e integrado ao sensor

- Comprar o hardware do sensor e instalar o software. Siga as etapas descritas aqui e para obter mais informações, consulte este artigo e o [Guia de hardware do defender for IOT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) e o [Guia de instalação](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

  - Depois de instalar o sensor, registre com segurança as credenciais de entrada do sensor. Você precisará das credenciais para carregar o arquivo de ativação no sensor.

  - Se você estiver trabalhando com sensores gerenciados localmente, registre com segurança o endereço IP do sensor ou o nome do sensor definido na instalação. Talvez você queira usá-lo ao criar um nome de sensor durante o registro do sensor no portal do defender para IoT. Você pode usá-los mais tarde para garantir o controle mais fácil e a nomenclatura consistente entre o nome de registro no portal do Azure defender para IoT e o endereço IP do sensor implantado exibido no console do sensor.

- Registre o sensor no portal do defender para IoT e baixe um arquivo de ativação do sensor.

- Carregue o arquivo de ativação em seu sensor.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. executar a configuração de rede para monitoramento e gerenciamento de sensor

- Conecte seu sensor à rede. Descrito no [Guia de configuração de rede](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. começar a descobrir sua rede

- Ajuste as configurações do sistema no console do sensor.

- Conecte os sensores a um console de gerenciamento local.

Para obter mais informações, consulte o [Guia do usuário do sensor do Azure defender para IOT](https://aka.ms/AzureDefenderforIoTUserGuide) e o [Guia do usuário do console de gerenciamento local do defender para IOT](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. popular o Azure Sentinel com informações de alerta

- Para enviar informações de alerta para o Azure Sentinel, configure o Azure Sentinel: [conecte seus dados do defender for IOT ao Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Próximas etapas

- Habilitar o [defender para IOT](quickstart-onboard-iot-hub.md)
- Configurar sua [solução](quickstart-configure-your-solution.md)
- [Criar Módulos de Segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implantar um agente de segurança](how-to-deploy-agent.md)
