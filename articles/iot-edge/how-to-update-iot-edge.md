---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456554"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o runtime

As the IoT Edge service releases new versions, you'll want to update your IoT Edge devices for the latest features and security improvements. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível. 

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de runtime quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o runtime, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o runtime poderá ser atualizado no dispositivo ou remotamente. 

Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge. 

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivos do Linux

On Linux x64 devices, use apt-get or your appropriate package manager to update the security daemon. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

On Linux ARM32 devices, use the steps in [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) to install the latest version of the security daemon. 

### <a name="windows-devices"></a>Dispositivos Windows

On Windows devices, use the PowerShell script to update the security daemon. The script automatically pulls the latest version of the security daemon. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Running the Update-IoTEdge command removes the security daemon from your device, along with the two runtime container images. The config.yaml file is kept on the device, as well as data from the Moby container engine (if you're using Windows containers). Keeping the configuration information means that you don't have to provide the connection string or Device Provisioning Service information for your device again during the update process. 

If you want to install a specific version of the security daemon, download the appropriate Microsoft-Azure-IoTEdge.cab file from [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases). Em seguida, use o parâmetro `-OfflineInstallationPath` para apontar para o local do arquivo. Para obter mais informações, consulte [Instalação offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de runtime

The way that you update the IoT Edge agent and IoT Edge hub containers depends on whether you use rolling tags (like 1.0) or specific tags (like 1.0.7) in your deployment. 

Verifique a versão do agente do IoT Edge e de módulos de hub do IoT Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`. 

  ![Encontre a versão do contêiner nos logs](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do IoT Edge e as imagens de hub do IoT Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de runtime: 

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o runtime do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de runtime serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção. 
* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. For example, 1.0.7 won't change after its initial release. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de tag sem interrupção

Se você usar marcas sem interrupção em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), será necessário forçar o runtime do contêiner em seu dispositivo para obter a versão mais recente da imagem. 

Exclua a versão local da imagem do seu dispositivo IoT Edge. Em computadores Windows, desinstalar o daemon de segurança também remove as imagens de runtime, portanto, você não precisará realizar esta etapa novamente. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Talvez você precise usar a marca `-f` de forçar para remover as imagens. 

O serviço do IoT Edge obterá as versões mais recentes das imagens do runtime e as iniciará automaticamente em seu dispositivo outra vez. 

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem de tag específica

If you use specific tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0.7**) then all you need to do is update the tag in your deployment manifest and apply the changes to your device. 

No portal do Azure, as imagens de implantação de runtime são declaradas na seção **Definir configurações de runtime do Edge avançadas**. 

![Configure advanced edge runtime settings](./media/how-to-update-iot-edge/configure-runtime.png)

Em um manifesto de implantação de JSON, atualize as imagens de módulo na seção **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Update to a release candidate version

Azure IoT Edge regularly releases new versions of the IoT Edge service. Before each stable release, there is one or more release candidate (RC) versions. RC versions include all the planned features for the release, but are still going through the testing and validation processes required for a stable release. If you want to test a new feature early, you can install the RC version and provide feedback through GitHub. 

Release candidate versions follow the same numbering convention of releases, but have **-rc** plus an incremental number appended to the end. You can see the release candidates in the same list of [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) as the stable versions. For example, find **1.0.7-rc1** and **1.0.7-rc2**, the two release candidates that came before **1.0.7**. You can also see that RC versions are marked with **pre-release** labels. 

As previews, release candidate versions aren't included as the latest version that the regular installers target. Instead, you need to manually target the assets for the RC version that you want to test. Depending on your IoT Edge device operating system, use the following sections to update IoT Edge to a specific version:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Próximos passos

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Fique por dentro das atualizações e comunicados mais recentes no [blog da Internet das Coisas](https://azure.microsoft.com/blog/topics/internet-of-things/) 