---
title: Pacote de importação da solução de monitoramento remoto-Azure | Microsoft Docs
description: Este artigo descreve como importar um pacote de gerenciamento de dispositivo automático para o acelerador de solução de Monitoramento Remoto
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 266e31ae9865c8fb427e06e89cd755e7ff38b27f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073862"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importar um pacote de gerenciamento de dispositivo automático para o acelerador de solução de Monitoramento Remoto

Uma configuração automática de gerenciamento de dispositivo define as alterações de configuração a serem implantadas em um grupo de dispositivos. Este artigo presume que um desenvolvedor em sua organização já criou uma configuração de gerenciamento de dispositivo automático. Para saber como um desenvolvedor cria uma configuração, consulte um dos seguintes artigos de instruções do Hub IoT:

- [Configurar e monitorar dispositivos IoT em escala usando o portal do Azure](../iot-hub/iot-hub-automatic-device-management.md)
- [Configurar e monitorar dispositivos IoT em escala usando a CLI do Azure](../iot-hub/iot-hub-automatic-device-management-cli.md)

Um desenvolvedor cria e testa uma configuração de gerenciamento de dispositivo automático em um ambiente de desenvolvimento. Quando estiver pronto, você poderá importar a configuração para o acelerador de solução de Monitoramento Remoto.

## <a name="export-a-configuration"></a>Exportar uma configuração

Use o portal do Azure para exportar a configuração de gerenciamento de dispositivo automático do seu ambiente de desenvolvimento:

1. No portal do Azure, navegue até o hub IoT que você está usando para desenvolver e testar os dispositivos IoT. Clique em **Configuração do dispositivo IoT**:

    [![Configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Clique na configuração que você quer usar. A página **Detalhes da Configuração do Dispositivo** é exibida:

    [![Detalhes de configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Clique em **Baixar arquivo de configuração**:

    [![Baixar arquivo de configuração](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Salve o arquivo JSON como um arquivo local chamado **configuration.json**.

Agora você tem um arquivo contendo a configuração de gerenciamento de dispositivo automático. Na próxima seção, você importará essa configuração como um pacote para a solução de Monitoramento Remoto.

## <a name="import-a-package"></a>Importar um pacote

Siga as etapas abaixo para importar uma configuração de gerenciamento de dispositivo automático como um pacote para sua solução:

1. Navegue até a página **Pacotes** na interface da Web da Web do monitoramento remoto: ![Página Pacotes](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Clique em **+ Novo Pacote**, escolha **Configuração** como o tipo de pacote e clique em **Procurar** para selecionar o arquivo **configuration.json** que você salvou no seção anterior:

    ![Selecionar configuração](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Clique em **Carregar** para adicionar o pacote à sua solução de Monitoramento Remoto:

    ![Pacote carregado](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Você carregou uma configuração de gerenciamento de dispositivo automático como um pacote. Na página **Implantações**, você poderá implantar esse pacote nos dispositivos conectados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um pacote de configuração e importá-lo a partir da solução de monitoramento remoto, a próxima etapa será aprender como [Gerenciar dispositivos conectados ao Monitoramento Remoto em massa](iot-accelerators-remote-monitoring-bulk-configuration-update.md).