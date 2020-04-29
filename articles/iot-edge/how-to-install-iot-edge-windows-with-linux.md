---
title: Instalar o Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Azure IoT Edge instruções de instalação para contêineres do Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80133169"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usar IoT Edge no Windows para executar contêineres do Linux

Testar módulos de IoT Edge para dispositivos Linux usando um computador Windows.

Em um cenário de produção, os dispositivos Windows só devem executar contêineres do Windows. No entanto, um cenário de desenvolvimento comum é usar um computador Windows para criar módulos IoT Edge para dispositivos Linux. O tempo de execução do IoT Edge para Windows permite que você execute contêineres do Linux para fins de **teste e desenvolvimento** .

Este artigo lista as etapas para instalar o Azure IoT Edge tempo de execução usando contêineres do Linux em seu sistema Windows x64 (AMD/Intel). Para saber mais sobre o instalador do IoT Edge Runtime, incluindo detalhes sobre todos os parâmetros de instalação, consulte [instalar o Azure IOT Edge Runtime no Windows](how-to-install-iot-edge-windows.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação.

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

Azure IoT Edge com contêineres do Linux podem ser executados em qualquer versão do Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Se você quiser instalar o IoT Edge em uma máquina virtual, habilite a virtualização aninhada e aloque pelo menos 2 GB de memória. A maneira como você habilita a virtualização aninhada é diferente dependendo do hipervisor que seu uso. Para Hyper-V, as máquinas virtuais de geração 2 têm virtualização aninhada habilitada por padrão. Para o VMWare, há uma alternância para habilitar o recurso em sua máquina virtual.

### <a name="prepare-the-container-engine"></a>Preparar o mecanismo de contêiner

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). A maior diferença de configuração entre a execução de contêineres do Windows e do Linux em um computador com Windows é que a instalação do IoT Edge inclui um tempo de execução de contêiner do Windows, mas você precisa fornecer seu próprio tempo de execução para contêineres do Linux antes de instalar o IoT Edge.

Para configurar um computador Windows para desenvolver e testar contêineres para dispositivos Linux, você pode usar o [Docker desktop](https://www.docker.com/docker-windows) como seu mecanismo de contêiner. Você precisa instalar o Docker e configurá-lo para [usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IOT Edge.  

Se o dispositivo do IoT Edge for um computador Windows, verifique se atende aos [requisitos do sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) para Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de runtime, o agente do IoT Edge, que permite implantações remotas de outros módulos.

Quando você instala o runtime do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo de IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão de dispositivo fornecida pelo Hub IoT. Ou, você também pode usar o Serviço de Provisionamento de Dispositivos para provisionar dispositivos automaticamente, o que é útil quando há muitos dispositivos para configurar.

Você pode ler mais sobre as diferentes opções de instalação e parâmetros no artigo [instalar o Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md). Depois de instalar e configurar o Docker desktop para contêineres do Linux, a principal diferença de instalação é declarar o Linux com o parâmetro **-ContainerOs** . Por exemplo:

1. Se você ainda não fez isso, registre um novo dispositivo de IoT Edge e recupere a cadeia de conexão do dispositivo. Copie a cadeia de conexão a ser usada posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Azure portal](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão do AMD64 do PowerShell para instalar o IoT Edge e não o PowerShell (x86). Se não tiver certeza de qual tipo de sessão você está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby (que não é usado para contêineres do Linux) e o tempo de execução de IOT Edge. O padrão do comando para contêineres do Windows, portanto, declare o Linux como o sistema operacional do contêiner desejado.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando usa como padrão o provisionamento manual com uma cadeia de conexão de dispositivo. Declare o Linux como o sistema operacional do contêiner desejado novamente.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na etapa 1. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT.

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Verifique o status do serviço de IoT Edge:

```powershell
Get-Service iotedge
```

Examine os logs de serviço dos últimos 5 minutos:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para os erros mais comuns de configuração e rede:

```powershell
iotedge check
```

Módulos de execução da lista. Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**. Depois de [implantar os módulos do IOT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **edgeHub**, também será iniciado no dispositivo.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
