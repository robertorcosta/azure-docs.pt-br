---
title: Instalar Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge instruções de instalação em dispositivos Windows ou Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: e5dc5601be5bd6d8003b196b5e7768e66bc48478
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979387"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Instalar ou desinstalar o tempo de execução de Azure IoT Edge

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Há duas etapas para configurar um dispositivo IoT Edge. A primeira etapa é instalar o tempo de execução e suas dependências, que é abordado neste artigo. A segunda etapa é conectar o dispositivo à sua identidade na nuvem e configurar a autenticação com o Hub IoT. Essas etapas estão nos próximos artigos.

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime em dispositivos Linux ou Windows. Para dispositivos Windows, você tem uma opção adicional de usar contêineres do Linux ou contêineres do Windows. Atualmente, os contêineres do Windows no Windows são recomendados para cenários de produção. Os contêineres do Linux no Windows são úteis para cenários de desenvolvimento e teste, especialmente se você estiver desenvolvendo em um computador Windows para ser implantado em dispositivos Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para obter as informações mais recentes sobre quais sistemas operacionais têm suporte no momento para cenários de produção, consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

Ter um dispositivo Linux de x64, ARM32 ou ARM64. A Microsoft fornece pacotes de instalação para os sistemas operacionais Ubuntu Server 16, 4, Ubuntu Server 18, 4 e Raspbian Stretch.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prepare seu dispositivo para acessar os pacotes de instalação da Microsoft.

1. Instale a configuração do repositório que corresponde ao sistema operacional do seu dispositivo.

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

2. Copie a lista gerada para o diretório sources. List. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Versão do Windows

IoT Edge com contêineres do Windows requer o Windows versão 1809/Build 17762, que é a mais recente [compilação de suporte a longo prazo do Windows](/windows/release-information/). Para cenários de desenvolvimento e teste, qualquer SKU (Pro, Enterprise, Server, etc.) que dê suporte ao recurso de contêineres funcionará. No entanto, certifique-se de examinar a lista de sistemas com suporte antes de ir para a produção.

IoT Edge com contêineres do Linux podem ser executados em qualquer versão do Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Requisitos do mecanismo de contêiner

Azure IoT Edge se baseia em um mecanismo de contêiner [compatível com o OCI](https://www.opencontainers.org/) . Verifique se o dispositivo pode dar suporte a contêineres.

Se você estiver instalando o IoT Edge em uma máquina virtual, habilite a virtualização aninhada e aloque pelo menos 2 GB de memória. Para Hyper-V, as máquinas virtuais de geração 2 têm virtualização aninhada habilitada por padrão. Para o VMware, há uma alternância para habilitar o recurso em sua máquina virtual.

---

Azure IoT Edge pacotes de software estão sujeitos aos termos de licença localizados em cada pacote ( `usr/share/doc/{package-name}` ou no `LICENSE` diretório). Leia os termos de licença antes de usar um pacote. A instalação e o uso de um pacote constitui sua aceitação desses termos. Se você não concordar com os termos de licença, não use esse pacote.

## <a name="install-a-container-engine"></a>Instalar um mecanismo de contêiner

O Azure IoT Edge depende de um runtime de contêiner compatível com OCI. Para cenários de produção, recomendamos que você use o mecanismo baseado em Moby. O mecanismo Moby é o único mecanismo de contêiner com suporte oficial com Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o runtime Moby.

# <a name="linux"></a>[Linux](#tab/linux)

Atualize as listas de pacotes em seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o mecanismo de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Se você receber erros ao instalar o mecanismo de contêiner do Moby, verifique a compatibilidade do kernel do Linux para Moby. Alguns fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm kernels Linux personalizados sem os recursos necessários para a compatibilidade do mecanismo de contêiner. Execute o comando a seguir, que usa o [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fornecido por Moby para verificar a configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Na saída do script, verifique se todos os itens em `Generally Necessary` e `Network Drivers` estão habilitados. Se você não tiver recursos, habilite-os recriando o kernel da origem e selecionando os módulos associados para inclusão no kernel. config apropriado. Da mesma forma, se você estiver usando um gerador de configuração de kernel como `defconfig` ou `menuconfig` , localize e habilite os respectivos recursos e reconstrua o kernel de acordo. Depois de implantar o kernel recém-modificado, execute o script check-config novamente para verificar se todos os recursos necessários foram habilitados com êxito.

# <a name="windows"></a>[Windows](#tab/windows)

Para cenários de produção, use o mecanismo baseado em Moby que está incluído no script de instalação. Não há etapas adicionais para instalar o mecanismo.

Para IoT Edge com contêineres do Linux, você precisa fornecer seu próprio tempo de execução de contêiner. Instale o [Docker desktop](https://docs.docker.com/docker-for-windows/install/) em seu dispositivo e configure-o para [usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de continuar.

---

## <a name="install-the-iot-edge-security-daemon"></a>Instalar o daemon de segurança do IoT Edge

O daemon de segurança do IoT Edge fornece e mantém os padrões de segurança no dispositivo IoT Edge. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do runtime do IoT Edge.

As etapas nesta seção representam o processo típico para instalar a versão mais recente em um dispositivo que tenha conexão com a Internet. Se você precisar instalar uma versão específica, como uma versão de pré-lançamento ou precisar instalar enquanto estiver offline, siga as etapas de [instalação de versão offline ou específica](#offline-or-specific-version-installation) na próxima seção.

# <a name="linux"></a>[Linux](#tab/linux)

Atualize as listas de pacotes em seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se você quiser instalar a versão mais recente do daemon de segurança, use o seguinte comando que também instala a versão mais recente do pacote **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Se você quiser instalar uma versão específica do daemon de segurança, especifique a versão da saída da lista de apt. Especifique também a mesma versão para o pacote **libiothsm-STD** , que, caso contrário, instalaria sua versão mais recente. Por exemplo, o seguinte comando instala a versão mais recente da versão 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Se a versão que você deseja instalar não estiver listada, siga as etapas de [instalação de versão offline ou específica](#offline-or-specific-version-installation) na próxima seção. Essa seção mostra como direcionar qualquer versão anterior do daemon de segurança do IoT Edge ou versões Release Candidate.

# <a name="windows"></a>[Windows](#tab/windows)

1. Execute o PowerShell como administrador.

   Use uma sessão AMD64 do PowerShell, não o PowerShell (x86). Se não tiver certeza de qual tipo de sessão você está usando, execute o seguinte comando:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Execute o comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , que executa as seguintes tarefas:

   * Verifica se o computador Windows está em uma versão com suporte.
   * Ativa o recurso de contêineres.
   * Baixa o mecanismo Moby e o tempo de execução IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   O `Deploy-IoTEdge` padrão do comando é usar contêineres do Windows. Se você quiser usar contêineres do Linux, adicione o `ContainerOs` parâmetro:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. Neste ponto, a saída pode solicitar que você reinicie. Nesse caso, reinicie o dispositivo agora.

Ao instalar o IoT Edge em um dispositivo, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório local para instalação offline.

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge no Windows](reference-windows-scripts.md).

---

Agora que o mecanismo de contêiner e o tempo de execução do IoT Edge estão instalados em seu dispositivo, você está pronto para a próxima etapa, que é registrar seu dispositivo com o Hub IoT e configurar o dispositivo com suas informações de autenticação e identidade de nuvem.

Escolha o próximo artigo com base no tipo de autenticação que você deseja usar:

* A [autenticação de chave simétrica](how-to-manual-provision-symmetric-key.md) é mais rápida para começar.
* A [autenticação de certificado X. 509](how-to-manual-provision-x509.md) é mais segura para cenários de produção.

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

As etapas nesta seção são para cenários não cobertos pelas etapas de instalação padrão. Isso pode incluir:

* Instalar IoT Edge enquanto estiver offline
* Instalar uma versão Release Candidate
* No Windows, instale uma versão diferente da mais recente

# <a name="linux"></a>[Linux](#tab/linux)

Use as etapas nesta seção se você quiser instalar uma versão específica do Azure IoT Edge Runtime que não está disponível por meio do `apt-get install` . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas subversãos, portanto, essas etapas são para qualquer pessoa que queira instalar uma versão mais antiga ou uma versão Release Candidate.

Usando comandos de rotação, você pode direcionar os arquivos de componentes diretamente do repositório GitHub IoT Edge.

<!-- TODO: Offline installation? -->

1. Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar.

2. Expanda a seção **ativos** para essa versão.

3. Cada versão deve ter novos arquivos para o daemon de segurança IoT Edge e o hsmlib. Use os comandos a seguir para atualizar esses componentes.

   1. Localize o arquivo **libiothsm-STD** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Localize o arquivo **iotedge** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão do daemon de segurança IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

Durante a instalação, três arquivos são baixados:

* Um script do PowerShell, que contém as instruções de instalação
* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby
* Instalador do pacote redistribuível (tempo de execução do VC) Visual C++

Se o dispositivo ficar offline durante a instalação ou se você quiser instalar uma versão específica do IoT Edge, poderá baixar esses arquivos antecipadamente para o dispositivo. Quando for o momento de instalar, aponte o script de instalação no diretório que contém os arquivos baixados. O instalador verifica primeiro o diretório e, em seguida, baixa apenas os componentes que não foram encontrados. Se todos os arquivos estiverem disponíveis offline, você poderá instalar o sem conexão com a Internet.

1. Para obter os arquivos de instalação mais recentes do IoT Edge juntamente com as versões anteriores, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

2. Localize a versão que você deseja instalar e baixe os seguintes arquivos da seção **ativos** das notas de versão para seu dispositivo IOT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab das versões 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab das versões 1.0.8 e mais antigas.

   Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir do 1.0.9 apenas para fins de teste. No momento, não há suporte para IoT Edge em dispositivos Windows ARM32.

   É importante usar o script do PowerShell da mesma versão que o arquivo. cab que você usa porque a funcionalidade muda para dar suporte aos recursos em cada versão.

3. Se o arquivo. cab baixado tiver um sufixo de arquitetura, renomeie o arquivo para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, baixe um instalador para Visual C++ redistribuível. Por exemplo, o script do PowerShell usa esta versão: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Salve o instalador na mesma pasta em seu dispositivo IoT que os arquivos de IoT Edge.

5. Para instalar com componentes offline, [dot Source](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a cópia local do script do PowerShell. 

6. Execute o comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) com o `-OfflineInstallationPath` parâmetro. Forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   O comando de implantação usará todos os componentes encontrados no diretório de arquivos local fornecido. Se o arquivo. cab ou o instalador do Visual C++ estiver ausente, ele tentará baixá-los.

---

Agora que o mecanismo de contêiner e o tempo de execução do IoT Edge estão instalados em seu dispositivo, você está pronto para a próxima etapa, que é [autenticar um dispositivo de IOT Edge no Hub IOT](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo, use os comandos a seguir.

# <a name="linux"></a>[Linux](#tab/linux)

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução de IoT Edge é removido, todos os contêineres que ele criou são interrompidos, mas ainda existem em seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem.

```bash
sudo docker ps -a
```

Exclua os contêineres do seu dispositivo, incluindo dois contêineres de runtime.

```bash
sudo docker rm -f <container name>
```

Por fim, remova o runtime do contêiner do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) em uma janela administrativa do PowerShell. Esse comando remove o runtime do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para obter mais informações sobre as opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Próximas etapas

Depois de instalar o IoT Edge Runtime, configure seu dispositivo para se conectar ao Hub IoT. Os artigos a seguir orientam o registro de um novo dispositivo na nuvem e, em seguida, fornece ao dispositivo suas informações de identidade e autenticação.

Escolha o próximo artigo com base no tipo de autenticação que você deseja usar:

* **Chave simétrica**: o Hub IOT e o dispositivo IOT Edge têm uma cópia de uma chave segura. Quando o dispositivo se conecta ao Hub IoT, eles verificam se as chaves correspondem. Esse método de autenticação é mais rápido para começar, mas não tão seguro.

  [Configurar um dispositivo Azure IoT Edge com autenticação de chave simétrica](how-to-manual-provision-symmetric-key.md)

* **X. 509 autoassinado**: o dispositivo IOT Edge tem certificados de identidade x. 509 e o Hub IOT recebe a impressão digital dos certificados. Quando o dispositivo se conecta ao Hub IoT, ele compara o certificado com sua impressão digital. Esse método de autenticação é mais seguro e recomendado para cenários de produção.

  [Configurar um dispositivo Azure IoT Edge com autenticação de certificado X. 509](how-to-manual-provision-x509.md)