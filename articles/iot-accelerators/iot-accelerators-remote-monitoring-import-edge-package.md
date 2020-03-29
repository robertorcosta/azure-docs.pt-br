---
title: Pacote de Borda de importação de solução de Monitoramento Remoto - Azure|Microsoft Docs
description: Este artigo descreve como importar um pacote do IoT Edge para o acelerador de solução de monitoramento remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442829"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importe um pacote IoT Edge para o acelerador de solução de monitoramento remoto

Um manifesto de implantação define os módulos para implantar em um dispositivo IoT Edge. Este artigo presume que um desenvolvedor em sua organização já criou o manifesto de implantação. Para saber como um desenvolvedor cria um manifesto, consulte um dos seguintes artigos de instruções do IoT Edge:

- [Implante os módulos do Azure IoT Edge no portal do Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implante os módulos do Azure IoT Edge com a CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Implantar módulos do Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Um desenvolvedor cria e testa um manifesto de implantação em um ambiente de desenvolvimento. Quando estiver pronto, você poderá importar o manifesto para o acelerador de solução de monitoramento remoto.

## <a name="export-a-manifest"></a>Exportar um manifesto

Use o portal do Azure para exportar o manifesto de implantação do seu ambiente de desenvolvimento:

1. No portal do Azure, navegue até o hub IoT que você está usando para desenvolver e testar seus dispositivos IoT Edge. Clique em **do IoT Edge** e, em seguida **implantações do IoT Edge**: ![do IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Clique na implantação que tem a configuração de implantação que você deseja usar. A página **Detalhes da Implantação** é exibida: ![Detalhes da implantação do IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Clique **em Baixar manifesto IoT Edge**: ![Baixe manifesto de implantação](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Salve o arquivo JSON como um arquivo local chamado **deploymentmanifest.json**.

Agora você tem um arquivo que contém o manifesto de implantação. Na próxima seção, você importa esse manifesto como um pacote para a solução de monitoramento remoto.

## <a name="import-a-package"></a>Importar um pacote

Siga as etapas abaixo para importar um manifesto de implantação do Edge como um pacote para sua solução:

1. Navegue até a página **Pacotes** na interface da Web da Web do monitoramento remoto: ![Página Pacotes](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Clique em **+ New Package**, escolha **Edge Manifest** como o tipo de pacote e clique em **Browse** para selecionar o arquivo **deploymentmanifest.json** que você salvou em a seção anterior: ![Selecione manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Clique em **carregue** para adicionar o pacote à sua solução de monitoramento remoto: ![pacote carregados](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Agora você carregou um manifesto de implantação do IoT Edge como um pacote. Na página **Implantações**, você pode implantar esse pacote em seus dispositivos IoT Edge conectados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar módulos em um dispositivo IoT Edge a partir da solução de monitoramento remoto, a próxima etapa é aprender mais sobre [IoT Edge](../iot-edge/about-iot-edge.md).
