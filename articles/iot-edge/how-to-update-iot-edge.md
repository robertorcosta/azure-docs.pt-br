---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee00425da89391e5228f2d48b49ca85426066f1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85299000"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o runtime

Como o serviço de IoT Edge libera novas versões, convém atualizar seus dispositivos de IoT Edge para os recursos e aprimoramentos de segurança mais recentes. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de runtime quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o runtime, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o runtime poderá ser atualizado no dispositivo ou remotamente.

Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivos do Linux

Em dispositivos Linux x64, use apt-get ou o Gerenciador de pacotes apropriado para atualizar o daemon de segurança para a versão mais recente.

Obtenha a configuração mais recente do repositório da Microsoft:

* **Ubuntu Server 16, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Atualizar apt.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se você quiser atualizar para a versão mais recente do daemon de segurança, use o seguinte comando que também atualiza **libiothsm-STD** para a versão mais recente:

   ```bash
   sudo apt-get install iotedge
   ```

Se você quiser atualizar para uma versão específica do daemon de segurança, especifique a versão da saída da lista de apt. Sempre que o **iotedge** for atualizado, ele tentará atualizar automaticamente o pacote **libiothsm-STD** para sua versão mais recente, o que pode causar um conflito de dependência. Se você não for para a versão mais recente, certifique-se de direcionar ambos os pacotes para a mesma versão. Por exemplo, o comando a seguir instala uma versão específica da versão 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Se a versão que você deseja instalar não estiver disponível por meio de apt-get, você poderá usar a rotação para direcionar qualquer versão do repositório de [versões IOT Edge](https://github.com/Azure/azure-iotedge/releases) . Para qualquer versão que você deseja instalar, localize os arquivos **libiothsm-STD** e **iotedge** apropriados para seu dispositivo. Para cada arquivo, clique com o botão direito do mouse no link do arquivo e copie o endereço do link. Use o link address para instalar as versões específicas desses componentes:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, use o script do PowerShell para atualizar o daemon de segurança. O script obtém automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A execução do comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner de tempo de execução. O arquivo config. YAML é mantido no dispositivo, bem como os dados do mecanismo de contêiner do Moby (se você estiver usando contêineres do Windows). Manter as informações de configuração significa que você não precisa fornecer à cadeia de conexão ou às informações do serviço de provisionamento de dispositivos para seu dispositivo novamente durante o processo de atualização.

Se você quiser atualizar para uma versão específica do daemon de segurança, localize a versão que você deseja direcionar de [IOT Edge versões](https://github.com/Azure/azure-iotedge/releases). Nessa versão, baixe o arquivo de **Microsoft-Azure-IoTEdge.cab** . Em seguida, use o `-OfflineInstallationPath` parâmetro para apontar para o local do arquivo local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um arquivo chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. A partir do IoT Edge versão 1.0.9-RC4, há dois arquivos. cab disponíveis para uso, um para dispositivos AMD64 e outro para ARM32. Baixe o arquivo correto para seu dispositivo e, em seguida, renomeie o arquivo para remover o sufixo de arquitetura.

Para obter mais informações sobre as opções de atualização, use o comando `Get-Help Update-IoTEdge -full` ou consulte [todos os parâmetros de instalação](how-to-install-iot-edge-windows.md#all-installation-parameters).

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

## <a name="update-offline-or-to-a-specific-version"></a>Atualizar offline ou para uma versão específica

Se você quiser atualizar um dispositivo offline ou atualizar para uma versão específica do IoT Edge em vez da versão mais recente, poderá fazer isso com o `-OfflineInstallationPath` parâmetro.

Dois componentes são usados para atualizar um dispositivo IoT Edge:

* Um script do PowerShell, que contém as instruções de instalação
* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby

1. Para obter os arquivos de instalação mais recentes do IoT Edge juntamente com as versões anteriores, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

2. Localize a versão que você deseja instalar e baixe os seguintes arquivos da seção **ativos** das notas de versão para seu dispositivo IOT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab das versões 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab das versões 1.0.8 e mais antigas.

   Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir do 1.0.9 apenas para fins de teste. No momento, não há suporte para IoT Edge em dispositivos Windows ARM32.

   É importante usar o script do PowerShell da mesma versão que o arquivo. cab que você usa porque a funcionalidade muda para dar suporte aos recursos em cada versão.

3. Se o arquivo. cab baixado tiver um sufixo de arquitetura, renomeie o arquivo para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Para atualizar com componentes offline, [dot Source](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) a cópia local do script do PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do `Update-IoTEdge` comando e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão Release Candidate

Azure IoT Edge lança regularmente novas versões do serviço de IoT Edge. Antes de cada versão estável, há uma ou mais versões RC (Release Candidate). As versões RC incluem todos os recursos planejados para a versão, mas ainda passam por testes e validação. Se você quiser testar um novo recurso antecipadamente, poderá instalar uma versão RC e fornecer comentários por meio do GitHub.

As versões Release Candidate seguem a mesma convenção de numeração de versões, mas têm **-RC** mais um número incremental acrescentado ao final. Você pode ver os candidatos à versão na mesma lista de [versões de Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) como as versões estáveis. Por exemplo, localize **1.0.9-RC5** e **1.0.9-RC6**, dois dos candidatos à versão que vieram antes de **1.0.9**. Você também pode ver que as versões RC são marcadas com rótulos de **pré-lançamento** .

O agente de IoT Edge e os módulos de Hub têm versões RC marcadas com a mesma convenção. Por exemplo, **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

Como as visualizações, as versões Release Candidate não são incluídas como a versão mais recente que os instaladores normais visam. Em vez disso, você precisa direcionar manualmente os ativos para a versão RC que deseja testar. Para a maior parte, instalar ou atualizar para uma versão RC é o mesmo que visar qualquer outra versão específica do IoT Edge.

Use as seções deste artigo para saber como atualizar um dispositivo IoT Edge para uma versão específica do daemon de segurança ou módulos de tempo de execução.

Se você estiver instalando o IoT Edge em um novo computador, use os links a seguir para saber como instalar uma versão específica, dependendo do sistema operacional do seu dispositivo:

* [Linux](how-to-install-iot-edge-linux.md#install-runtime-using-release-assets)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Próximas etapas

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Mantenha-se atualizado com as atualizações e os comunicados recentes no [blog Internet das coisas](https://azure.microsoft.com/blog/topics/internet-of-things/)
