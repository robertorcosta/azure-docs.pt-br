---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113312"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o runtime

À medida que o serviço IoT Edge lança novas versões, você vai querer atualizar seus dispositivos IoT Edge para os recursos mais recentes e melhorias de segurança. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de runtime quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o runtime, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o runtime poderá ser atualizado no dispositivo ou remotamente.

Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivos do Linux

Nos dispositivos Linux x64, use o apt-get ou o seu gerenciador de pacotes apropriado para atualizar o daemon de segurança para a versão mais recente.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Se você quiser atualizar para uma versão específica do daemon de segurança, encontre a versão que deseja segmentar a partir de [versões do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, localize os arquivos **de libiothsm-std** e **iotedge** apropriados para o seu dispositivo. Para cada arquivo, clique com o botão direito do mouse no link do arquivo e copie o endereço do link. Use o endereço de link para instalar as versões específicas desses componentes:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, use o script PowerShell para atualizar o daemon de segurança. O script puxa automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A execução do comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner em tempo de execução. O arquivo config.yaml é mantido no dispositivo, bem como dados do mecanismo de contêiner Moby (se você estiver usando recipientes do Windows). Manter as informações de configuração significa que você não precisa fornecer as informações da seqüência de conexões ou do Serviço de Provisionamento de Dispositivos para o seu dispositivo novamente durante o processo de atualização.

Se você quiser atualizar para uma versão específica do daemon de segurança, encontre a versão que deseja segmentar a partir de [versões do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, baixe o arquivo **Microsoft-Azure-IoTEdge.cab.** Em seguida, `-OfflineInstallationPath` use o parâmetro para apontar para a localização do arquivo local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um arquivo chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Começando com a versão IoT Edge 1.0.9-rc4, há dois arquivos .cab disponíveis para uso, um para dispositivos AMD64 e um para ARM32. Baixe o arquivo correto para o seu dispositivo e, em seguida, renomeie o arquivo para remover o sufixo de arquitetura.

Para obter mais informações sobre `Get-Help Update-IoTEdge -full` opções de atualização, use o comando ou consulte [todos os parâmetros de instalação](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de runtime

A maneira como você atualiza o agente IoT Edge e os contêineres do hub IoT Edge depende se você usa tags de rolamento (como 1.0) ou tags específicas (como 1.0.7) em sua implantação.

Verifique a versão do agente do IoT Edge e de módulos de hub do IoT Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`.

  ![Encontre a versão do contêiner nos logs](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do IoT Edge e as imagens de hub do IoT Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de runtime:

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o runtime do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de runtime serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção.

* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. Por exemplo, o 1.0.7 não mudará após sua versão inicial. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

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

Se você usar tags específicas em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8**) então tudo o que você precisa fazer é atualizar a tag em seu manifesto de implantação e aplicar as alterações ao seu dispositivo.

1. No IoT Hub no portal Azure, selecione o dispositivo IoT Edge e selecione **Set Modules**.

1. Na seção **Módulos de borda IoT,** selecione **Configurações de tempo de execução**.

   ![Configure as configurações de tempo de execução](./media/how-to-update-iot-edge/configure-runtime.png)

1. Em **Configurações de tempo de**execução, atualize o valor de **imagem** para O **Hub de** imagem com a versão desejada. Não selecione **Salvar** ainda.

   ![Atualizar versão de imagem do Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Entre em colapso as configurações **do Edge Hub** ou role para baixo e atualize o valor de **imagem** para Agente **de Borda** com a mesma versão desejada.

   ![Atualizar versão do Edge Hub Agent](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Clique em **Salvar**.

1. Selecione **'Revisar + criar',** revisar a implantação e selecionar **Criar**.

## <a name="update-offline-or-to-a-specific-version"></a>Atualizar offline ou para uma versão específica

Se você quiser atualizar um dispositivo offline ou atualizar para uma versão específica do IoT Edge `-OfflineInstallationPath` em vez da versão mais recente, você pode fazê-lo com o parâmetro.

Dois componentes são usados para atualizar um dispositivo IoT Edge:

* Um script PowerShell, que contém as instruções de instalação
* Microsoft Azure IoT Edge cab, que contém o daemon de segurança IoT Edge (iotedged), moby container engine, e Moby CLI

1. Para obter os arquivos de instalação mais recentes do IoT Edge, juntamente com as versões anteriores, consulte [as versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Encontre a versão que deseja instalar e baixe os seguintes arquivos da seção **Ativos** das notas de versão no seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab de lançamentos 1.0.9 ou mais novos, ou Microsoft-Azure-IoTEdge.cab a partir de versões 1.0.8 ou mais antigas.

   O Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para fins de teste. O IoT Edge não é suportado atualmente em dispositivos Windows ARM32.

   É importante usar o script PowerShell da mesma versão do arquivo .cab que você usa porque a funcionalidade muda para suportar os recursos em cada versão.

3. Se o arquivo .cab que você baixou tiver um sufixo de arquitetura nele, renomeie o arquivo para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Para atualizar com componentes offline, [dot origem](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, `-OfflineInstallationPath` use o parâmetro `Update-IoTEdge` como parte do comando e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão do candidato de lançamento

O Azure IoT Edge lança regularmente novas versões do serviço IoT Edge. Antes de cada lançamento estável, há uma ou mais versões do Candidato de Lançamento (RC). As versões RC incluem todos os recursos planejados para o lançamento, mas ainda estão passando por testes e validação. Se você quiser testar um novo recurso mais cedo, você pode instalar uma versão RC e fornecer feedback através do GitHub.

As versões dos candidatos de lançamento seguem a mesma convenção de numeração dos lançamentos, mas têm **-rc** mais um número incremental anexado ao final. Você pode ver os candidatos de lançamento na mesma lista de lançamentos do [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) como as versões estáveis. Por exemplo, encontre **1.0.7-rc1** e **1.0.7-rc2**, os dois candidatos de lançamento que vieram antes **de 1.0.7**. Você também pode ver que as versões RC são marcadas com rótulos **de pré-lançamento.**

O agente IoT Edge e os módulos de hub têm versões RC que são marcadas com a mesma convenção. Por exemplo, **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2.**

Como visualizações, as versões do candidato de lançamento não estão incluídas como a versão mais recente que os instaladores regulares visam. Em vez disso, você precisa direcionar manualmente os ativos para a versão RC que você deseja testar. Na maioria das vezes, instalar ou atualizar uma versão RC é o mesmo que direcionar qualquer outra versão específica do IoT Edge.

Use as seções deste artigo para aprender como atualizar um dispositivo IoT Edge para uma versão específica do daemon de segurança ou módulos de tempo de execução.

Se você estiver instalando o IoT Edge em uma nova máquina, use os seguintes links para aprender como instalar uma versão específica, dependendo do sistema operacional do dispositivo:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Próximas etapas

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Fique por dentro das atualizações e comunicados mais recentes no [blog da Internet das Coisas](https://azure.microsoft.com/blog/topics/internet-of-things/)
