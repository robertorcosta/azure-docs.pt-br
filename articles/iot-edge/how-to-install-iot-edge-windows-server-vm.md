---
title: Executar Azure IoT Edge em Máquinas Virtuais do Windows Server | Microsoft Docs
description: Instruções de configuração do Azure IoT Edge em Máquinas Virtuais do Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045888"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar a zure IoT Edge em máquinas virtuais do Windows Server

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre como funciona o runtime do IoT Edge e quais componentes estão incluídos, veja [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o tempo de execução do Azure IoT Edge em uma máquina virtual do Windows Server 2019 usando a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Siga as instruções em [Instalar o tempo de execução do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para uso com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace

1. Navegue até a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **GET IT NOW**
3. No **plano de software,** encontre "Núcleo de servidor de data center do Windows Server 2019 com contêineres" e, em seguida, **selecione Continuar** na próxima caixa de diálogo.
    * Você também pode usar essas instruções para outras versões do Windows Server com Containers
4. Quando estiver no portal do Azure, selecione **Criar** e siga o assistente para implantar a VM.
    * Se é a primeira vez que experimenta uma VM, é mais fácil usar uma senha e ativar RDP e SSH no menu de porta de entrada pública.
    * Se você tiver uma carga de trabalho com muitos recursos, atualize o tamanho da máquina virtual, adicionando mais CPUs e/ou memória.
5. Depois que a máquina virtual for implantada, configure-a para conectar ao Hub IoT:
    1. Copie a seqüência de conexão do dispositivo do seu dispositivo IoT Edge criado no seu Hub IoT. Veja o procedimento [Recuperar a seqüência de conexões no portal Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecionar o recurso de máquina virtual recém-criada no portal do Azure e abrir a opção **executar comando**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie este script na janela de comando com a seqüência de conexão do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o tempo de execução do IoT Edge e defina sua seqüência de conexões selecionando **Executar Executar**
    1. Depois de um minuto ou dois, você deve ver uma mensagem de que o tempo de execução do Edge foi instalado e provisionado com sucesso.

## <a name="deploy-from-the-azure-portal"></a>Implantar a partir do portal Azure

1. A partir do portal Azure, procure por "Windows Server" e selecione **o Windows Server 2019 Datacenter** para iniciar o fluxo de trabalho de criação de VM.
2. A **partir selecione um plano de software** escolha "Núcleo de servidor de data center do Windows Server 2019 com contêineres", em seguida, selecione **Criar**
3. Complete o passo 5 nas instruções "Implantar a partir do Mercado Azure" acima.

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
   1. Execute este comando com o ID copiado:

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

   * Este comando solicitará uma senha, mas você `--admin-password` pode adicionar a opção de defini-la mais facilmente em um script
   * A imagem do Windows Server Core tem suporte à linha de comando apenas com `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` desktop remoto, então se você quiser a experiência completa da área de trabalho, especifique como a imagem

1. Defina a seqüência de conexões do dispositivo (Você pode seguir a [seqüência recuperar a seqüência de conexão com o procedimento CLI do Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se você não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação de tempo de execução do Edge corretamente, confira a página [de solução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Saiba mais sobre o uso de máquinas virtuais do Windows na [documentação do Windows Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se você quiser ssh nesta VM após a configuração, siga a instalação do guia [OpenSSH for Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) usando desktop remoto ou powershell remoto.
