---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f31dec1d654124f9071c01c0e5d033db4b65a1d3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509658"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o runtime

Como o serviço de IoT Edge libera novas versões, convém atualizar seus dispositivos de IoT Edge para os recursos e aprimoramentos de segurança mais recentes. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de runtime quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o runtime, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o runtime poderá ser atualizado no dispositivo ou remotamente.

Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivos do Linux

Em dispositivos Linux x64, use apt-get ou o Gerenciador de pacotes apropriado para atualizar o daemon de segurança.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Em dispositivos Linux ARM32, use as etapas em [instalar Azure IOT Edge Runtime no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) para instalar a versão mais recente do daemon de segurança.

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, use o script do PowerShell para atualizar o daemon de segurança. O script obtém automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A execução do comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner de tempo de execução. O arquivo config. YAML é mantido no dispositivo, bem como os dados do mecanismo de contêiner do Moby (se você estiver usando contêineres do Windows). Manter as informações de configuração significa que você não precisa fornecer à cadeia de conexão ou às informações do serviço de provisionamento de dispositivos para seu dispositivo novamente durante o processo de atualização.

Se você quiser instalar uma versão específica do daemon de segurança, baixe o arquivo Microsoft-Azure-IoTEdge. cab apropriado em [versões IOT Edge](https://github.com/Azure/azure-iotedge/releases). Em seguida, use o parâmetro `-OfflineInstallationPath` para apontar para o local do arquivo. Para obter mais informações, consulte [Instalação offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de runtime

A maneira como você atualiza o agente de IoT Edge e os contêineres de IoT Edge Hub depende se você usa marcas sem interrupção (como 1,0) ou marcas específicas (como 1.0.7) em sua implantação.

Verifique a versão do agente do IoT Edge e de módulos de hub do IoT Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`.

  ![Encontre a versão do contêiner nos logs](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do IoT Edge e as imagens de hub do IoT Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de runtime:

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o runtime do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de runtime serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção.

* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.7 não será alterado após sua versão inicial. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

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

Se você usar marcas específicas em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), tudo o que você precisa fazer é atualizar a marca em seu manifesto de implantação e aplicar as alterações ao seu dispositivo.

1. No Hub IoT no portal do Azure, selecione o dispositivo IoT Edge e selecione **definir módulos**.

1. Na seção **módulos IOT Edge** , selecione **configurações de tempo de execução**.

   ![Definir configurações de tempo de execução](./media/how-to-update-iot-edge/configure-runtime.png)

1. Em **configurações de tempo de execução**, atualize o valor da **imagem** para o **Hub do Edge** com a versão desejada. Não selecione **salvar** ainda.

   ![Atualizar versão da imagem do Hub do Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Recolha as configurações do **Hub do Edge** ou role para baixo e atualize o valor da **imagem** para o **agente do Edge** com a mesma versão desejada.

   ![Atualizar versão do agente do Hub do Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Clique em **Salvar**.

1. Selecione **revisão + criar**, examine a implantação e selecione **criar**.

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão Release Candidate

Azure IoT Edge lança regularmente novas versões do serviço de IoT Edge. Antes de cada versão estável, há uma ou mais versões RC (Release Candidate). As versões RC incluem todos os recursos planejados para a versão, mas ainda passam pelos processos de teste e validação necessários para uma versão estável. Se você quiser testar um novo recurso antecipadamente, poderá instalar a versão RC e fornecer comentários por meio do GitHub.

As versões Release Candidate seguem a mesma convenção de numeração de versões, mas têm **-RC** mais um número incremental acrescentado ao final. Você pode ver os candidatos à versão na mesma lista de [versões de Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) como as versões estáveis. Por exemplo, encontre **1.0.7-RC1** e **1.0.7-RC2**, os dois candidatos de lançamento que vieram antes de **1.0.7**. Você também pode ver que as versões RC são marcadas com rótulos de **pré-lançamento** .

Como as visualizações, as versões Release Candidate não são incluídas como a versão mais recente que os instaladores normais visam. Em vez disso, você precisa direcionar manualmente os ativos para a versão RC que deseja testar. Dependendo do sistema operacional do dispositivo IoT Edge, use as seções a seguir para atualizar IoT Edge para uma versão específica:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Próximos passos

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Fique por dentro das atualizações e comunicados mais recentes no [blog da Internet das Coisas](https://azure.microsoft.com/blog/topics/internet-of-things/)
