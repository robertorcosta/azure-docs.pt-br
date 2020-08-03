---
title: Executar Azure IoT Edge em máquinas virtuais do Windows Server | Microsoft Docs
description: Azure IoT Edge instruções de instalação em máquinas virtuais do Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494053"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar Azure IoT Edge em máquinas virtuais do Windows Server

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem.

Para saber mais sobre como funciona o runtime do IoT Edge e quais componentes estão incluídos, veja [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o Azure IoT Edge Runtime em uma máquina virtual do Windows Server 2019 usando a oferta do Azure Marketplace do [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) . Siga as instruções em [instalar o Azure IOT Edge tempo de execução](how-to-install-iot-edge-windows.md) no Windows para uso com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace

1. Navegue até a oferta do [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **obter agora**
3. No **plano de software**, localize "núcleo do Windows Server 2019 datacenter com contêineres" e selecione **continuar** na próxima caixa de diálogo.
    * Você também pode usar estas instruções para outras versões do Windows Server com contêineres
4. Quando estiver no portal do Azure, selecione **Criar** e siga o assistente para implantar a VM.
    * Se for sua primeira vez experimentando uma VM, será mais fácil usar uma senha e habilitar o RDP e o SSH no menu porta de entrada pública.
    * Se você tiver uma carga de trabalho com muitos recursos, atualize o tamanho da máquina virtual, adicionando mais CPUs e/ou memória.
5. Depois que a máquina virtual for implantada, configure-a para conectar ao Hub IoT:
    1. Copie a cadeia de conexão do dispositivo de seu dispositivo IoT Edge criado no Hub IoT. Consulte o procedimento [recuperar a cadeia de conexão no portal do Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecionar o recurso de máquina virtual recém-criada no portal do Azure e abrir a opção **executar comando**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie esse script na janela de comando com a cadeia de conexão do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o IoT Edge Runtime e defina sua cadeia de conexão selecionando **executar**
    1. Após um ou dois minutos, você deverá ver uma mensagem informando que o tempo de execução do Edge foi instalado e provisionado com êxito.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

1. No portal do Azure, pesquise "Windows Server" e selecione **Windows server 2019 datacenter** para iniciar o fluxo de trabalho de criação de VM.
2. Em **selecionar um plano de software** , escolha "núcleo do servidor do Windows Server 2019 datacenter com contêineres" e, em seguida, selecione **criar**
3. Conclua a etapa 5 nas instruções "implantar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure

1. Se você estiver usando a CLI do Azure na área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```

1. Se você tiver várias assinaturas, selecione a assinatura que gostaria de usar:
   1. Liste suas assinaturas:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriptionID da assinatura que você quer usar
   1. Execute este comando com a ID que você copiou:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Esse comando solicitará uma senha, mas você pode adicionar a opção `--admin-password` para defini-la com mais facilidade em um script
   * A imagem do Windows Server Core tem suporte de linha de comando somente com a área de trabalho remota, portanto, se você quiser a experiência de área de trabalho completa, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` como a imagem

1. Defina a cadeia de conexão do dispositivo (você pode seguir o procedimento [recuperar a cadeia de conexão com CLI do Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Saiba mais sobre como usar máquinas virtuais do Windows na [documentação do máquinas virtuais do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se você quiser realizar o SSH nessa VM após a instalação, siga o guia de [instalação do OpenSSH para Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) usando a área de trabalho remota ou o PowerShell remoto.
