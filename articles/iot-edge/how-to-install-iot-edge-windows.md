---
title: Instalar o Azure IoT Edge no Windows | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: 38e688528d7445b16141d9f1ecc0318faf07e140
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509998"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre o runtime do IoT Edge, consulte [Reconhecer o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime no seu sistema Windows x64 (AMD/Intel) usando contêineres do Windows.

> [!NOTE]
> Um problema conhecido do sistema operacional Windows impede a transição para os Estados de energia de suspensão e hibernação quando IoT Edge módulos (contêineres do Windows nano Server com isolamento de processo) estão em execução. Esse problema afeta a vida útil da bateria no dispositivo.
>
> Como alternativa, use o comando `Stop-Service iotedge` para interromper qualquer módulo IoT Edge em execução antes de usar esses Estados de energia.

O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para o Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. Para saber mais, confira [usar o IOT Edge no Windows para executar contêineres do Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação.

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

Para cenários de desenvolvimento e teste, Azure IoT Edge com contêineres do Windows podem ser instalados em qualquer versão do Windows 10 ou Windows Server 2019 (Build 17763) que ofereça suporte ao recurso de contêineres. Para obter informações sobre quais sistemas operacionais têm suporte no momento para cenários de produção, consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems).

Os dispositivos IoT Core devem incluir o recurso opcional de contêineres IoT core-Windows para dar suporte ao tempo de execução de IoT Edge. Use o seguinte comando em uma [sessão remota do PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) para verificar se os contêineres do Windows têm suporte em seu dispositivo:

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, você deverá obter uma resposta bem-sucedida com o status do serviço listado como **em execução**. Se o serviço vmcompute não for encontrado, o dispositivo não atenderá aos requisitos para IoT Edge. Entre em contato com seu provedor de hardware para perguntar sobre o suporte para esse recurso.

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows no dispositivo Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de runtime, o agente do IoT Edge, que permite implantações remotas de outros módulos.

>[!TIP]
>Para dispositivos IoT Core, é recomendável executar os comandos de instalação usando uma sessão RemotePowerShell. Para obter mais informações, consulte [usando o PowerShell para Windows IOT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando você instala o runtime do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo de IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o DPS (serviço de provisionamento de dispositivos) para provisionar automaticamente os dispositivos, o que é útil quando você tem muitos dispositivos a serem configurados. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado.

As seções a seguir descrevem os casos de uso comuns e os parâmetros do script de instalação do IoT Edge em um novo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opção 1: instalar e provisionar manualmente

Nesta primeira opção, você fornece uma **cadeia de conexão de dispositivo** gerada pelo Hub IOT para provisionar o dispositivo.

Este exemplo demonstra uma instalação manual com contêineres do Windows:

1. Se você ainda não fez isso, registre um novo dispositivo de IoT Edge e recupere a **cadeia de conexão do dispositivo**. Copie a cadeia de conexão a ser usada posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

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

3. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na etapa 1. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT.

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use as etapas em [verificar a instalação bem-sucedida](#verify-successful-installation) para verificar o status de IOT Edge em seu dispositivo.

Ao instalar e provisionar um dispositivo manualmente, é possível usar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, pule adiante para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: instalar e provisionar automaticamente

Nesta segunda opção, você provisiona o dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Forneça a **ID de escopo** de uma instância do serviço de provisionamento de dispositivos junto com outras informações específicas ao seu [mecanismo de atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Criar e provisionar um dispositivo de IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e provisionar um dispositivo IoT Edge usando o atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Ao instalar e provisionar um dispositivo automaticamente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, continue lendo este artigo ou ignore para saber mais sobre [Todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

Durante a instalação, dois arquivos são baixados:

* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby.
* MSI do pacote redistribuível do Visual C++ (runtime de VC)

Você pode baixar um ou ambos os arquivos antes do tempo para o dispositivo e, em seguida, apontar o script de instalação no diretório que contém os arquivos. O instalador primeiro verifica o diretório e, em seguida, baixa apenas os componentes que não são localizados. Se todos os arquivos estiverem disponíveis offline, você poderá instalar o sem conexão com a Internet. Você também pode usar esse recurso para instalar uma versão específica dos componentes do.  

Para obter os arquivos de instalação mais recentes do IoT Edge juntamente com as versões anteriores, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

Para instalar com componentes offline, use o parâmetro `-OfflineInstallationPath` como parte do comando Deploy-IoTEdge e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Você também pode usar o parâmetro caminho de instalação offline com o comando Update-IoTEdge, apresentado posteriormente neste artigo.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Verifique o status do serviço do IoT Edge. Ele deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando. Se acabar de instalar o IoT Edge Runtime, você poderá ver uma lista de erros do tempo entre a execução de **Deploy-IoTEdge** e **Initialize-IoTEdge**. Esses erros são esperados, pois o serviço está tentando iniciar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para os erros de configuração e de rede mais comuns.

```powershell
iotedge check
```

Módulos de execução da lista. Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**. Depois de [implantar os módulos do IOT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **edgeHub**, também será iniciado no dispositivo.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerenciar contêineres de módulo

O serviço de IoT Edge requer um mecanismo de contêiner em execução em seu dispositivo. Quando você implanta um módulo em um dispositivo, o tempo de execução de IoT Edge usa o mecanismo de contêiner para efetuar pull da imagem de contêiner de um registro na nuvem. O serviço de IoT Edge permite que você interaja com seus módulos e recupere logs, mas, às vezes, convém usar o mecanismo de contêiner para interagir com o próprio contêiner.

Para obter mais informações sobre conceitos de módulo, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md).

Se você estiver executando contêineres do Windows em seu dispositivo Windows IoT Edge, a instalação do IoT Edge incluirá o mecanismo de contêiner Moby. O mecanismo Moby foi baseado nos mesmos padrões que o Docker e foi projetado para ser executado em paralelo no mesmo computador que o Docker desktop. Por esse motivo, se você quiser direcionar contêineres gerenciados pelo mecanismo do Moby, será necessário direcionar especificamente esse mecanismo em vez do Docker.

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens Moby, modifique o mesmo comando com um ponteiro para o mecanismo Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O URI do mecanismo é listado na saída do script de instalação ou você pode encontrá-lo na seção Configurações de tempo de execução do contêiner para o arquivo config. YAML.

![URI de moby_runtime em config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre os comandos que você pode usar para interagir com contêineres e imagens em execução em seu dispositivo, consulte [interfaces de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

Se você já tiver instalado o IoT Edge tempo de execução em um dispositivo antes e provisioná-lo com uma identidade do Hub IoT, poderá atualizar o tempo de execução sem precisar inserir novamente as informações do dispositivo.

Para obter mais informações, consulte [Atualize o daemon de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um arquivo de configuração existente e usa contêineres do Windows:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Ao atualizar IoT Edge, você pode usar parâmetros adicionais para modificar a atualização, incluindo:

* Direcionar o tráfego para passar por um servidor proxy ou
* Apontar o instalador para um diretório offline
* Reiniciando sem uma solicitação, se necessário

Você não pode declarar uma imagem de contêiner de agente de IoT Edge com parâmetros de script porque essa informação já está definida no arquivo de configuração da instalação anterior. Se você quiser modificar a imagem de contêiner do agente, faça isso no arquivo config.yaml.

Para obter mais informações sobre essas opções de atualização, use o comando `Get-Help Update-IoTEdge -full` ou consulte [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o seguinte comando em uma janela administrativa do PowerShell. Esse comando remove o runtime do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Windows IoT Core. Para remover IoT Edge de dispositivos Windows IoT Core, você precisa reimplantar sua imagem do Windows IoT Core.

Para obter mais informações sobre as opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários comuns de instalação com exemplos de como usar parâmetros para modificar o script de instalação. Esta seção fornece tabelas de referência dos parâmetros comuns usados para instalar, atualizar ou desinstalar o IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge baixa e implanta o daemon de segurança IoT Edge e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Deploy-IoTEdge -full`.  

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura IoT Edge com a cadeia de conexão do dispositivo e detalhes operacionais. Grande parte das informações geradas por esse comando é armazenada no arquivo iotedge\config.YAML. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Initialize-IoTEdge -full`.

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão de dispositivo para provisionar o dispositivo manualmente |
| **Dps** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma ID do escopo do DPS (Serviço de Provisionamento de Dispositivos) e a ID de Registro do dispositivo para provisionar por meio do DPS.  |
| **DeviceConnectionString** | Uma cadeia de conexão de um dispositivo do IoT Edge registrado em um Hub IoT, entre aspas simples | **Necessário** para instalação manual. Se você não fornecer uma cadeia de conexão nos parâmetros de script, durante a instalação será solicitado que você forneça uma. |
| **ScopeId** | Uma ID do escopo de uma instância do Serviço de Provisionamento de Dispositivos associada ao Hub IoT. | **Necessário** para instalação de DPS. Se você não fornecer uma ID do escopo nos parâmetros do script, durante a instalação será solicitado que você forneça uma. |
| **RegistrationId** | Uma ID de registro gerada pelo dispositivo | **Necessário** para a instalação de DPS se estiver usando o TPM ou o atestado de chave simétrica. |
| **SymmetricKey** | A chave simétrica usada para provisionar a IoT Edge identidade do dispositivo ao usar o DPS | **Necessário** para a instalação de DPS se estiver usando o atestado de chave simétrica. |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de imagem do agente do IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca sem interrupção mais recente para a imagem do agente do IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer a própria imagem de agente. Para obter mais informações, consulte [Reconhecer as marcas do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de usuário** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Senha** | Cadeia de caracteres de senha de segurança | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça a senha para acessar o registro. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Aplicação** | none | Esse sinalizador força a desinstalação caso a tentativa anterior de desinstalar não tenha sido bem-sucedida.
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de desinstalação reinicie o computador sem avisar, se necessário. |

## <a name="next-steps"></a>Próximos passos

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
