---
title: 'Início Rápido: Criar um módulo gêmeo de segurança para a Central de Segurança do Azure para IoT'
description: Neste início rápido, saiba como criar um módulo gêmeo para ser usado na Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904159"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Início Rápido: Criar um módulo gêmeo azureiotsecurity

Este Início Rápido explica como criar módulos gêmeos individuais _azureiotsecurity_ para novos dispositivos ou criar módulos gêmeos em lote para todos os dispositivos em um Hub IoT.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Noções básicas sobre módulos gêmeos azureiotsecurity 

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo. 

A Central de Segurança do Azure para IoT oferece integração completa com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie seu status de segurança de dispositivo e faça uso de funcionalidades de controle de dispositivo existente.
A integração da Central de Segurança do Azure para IoT é obtida usando o mecanismo gêmeo do Hub IoT.  

Veja [módulos gêmeos do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral dos módulos gêmeos no Hub IoT do Azure. 
 
A Central de Segurança do Azure para IoT usa o mecanismo de módulo gêmeo e mantém um módulo gêmeo de segurança chamado _azureiotsecurity_ para cada um dos seus dispositivos.

O módulo gêmeo de segurança contém todas as informações relevantes para a segurança de dispositivo para cada um dos seus dispositivos. 
 
Para aproveitar totalmente os recursos da Central de Segurança do Azure para IoT, você precisará criar, configurar e usar esses módulos gêmeos de segurança para cada dispositivo no serviço.  

## <a name="create-azureiotsecurity-module-twin"></a>Criar módulo gêmeo azureiotsecurity 

Os módulos gêmeos _azureiotsecurity_ podem ser criados de duas maneiras:
1. [Script em lotes do módulo](https://aka.ms/iot-security-github-create-module) – cria automaticamente o módulo gêmeo para novos dispositivos ou dispositivos sem um módulo gêmeo usando a configuração padrão.
2. Editando manualmente cada módulo gêmeo individualmente com configurações específicas para cada dispositivo.

>[!NOTE] 
> Usar o método de lote não substituirá os módulos gêmeos azureiotsecurity existentes. Usar o método de lote cria APENAS módulos gêmeos para dispositivos que ainda não têm um módulo gêmeo de segurança. 

Confira a [configuração do agente](how-to-agent-configuration.md) para saber como modificar ou alterar a configuração de um módulo gêmeo existente. 

Para criar manualmente um módulo gêmeo _azureiotsecurity_ para um dispositivo, use as seguintes instruções: 

1. No Hub IoT, localize e selecione o dispositivo para o qual deseja criar um módulo gêmeo de segurança.
1. Clique no seu dispositivo e, em seguida, em **Adicionar identidade do módulo**.
1. No campo **Nome da Identidade do Módulo**, insira **azureiotsecurity**.

1. Clique em **Save** (Salvar). 

## <a name="verify-creation-of-a-module-twin"></a>Verificar a criação de um módulo gêmeo

Para verificar a existência de um módulo gêmeo de segurança para um dispositivo específico:

1. No seu Hub IoT do Azure, selecione **Dispositivos IoT** no menu **Explorers**.    
1. Insira a ID do dispositivo ou selecione uma opção no **Campo de dispositivo de consulta** e clique em **Dispositivos de consulta**. 
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou clique nele duas vezes para abrir a página de detalhes do Dispositivo. 
1. Selecione o menu **Identidades do módulo** e confirme a existência do módulo **ascforiotsecurity** na lista de identidades de módulo associadas ao dispositivo. 
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-3.png)


Para aprender mais sobre como personalizar propriedades de módulos gêmeos da Central de Segurança do Azure para IoT, consulte [Configuração do agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Próximas etapas

Avance ao próximo artigo para aprender como criar alertas personalizados...

> [!div class="nextstepaction"]
> [Configurar alertas personalizados](quickstart-create-custom-alerts.md)
