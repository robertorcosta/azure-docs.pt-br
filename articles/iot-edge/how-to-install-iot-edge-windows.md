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
ms.openlocfilehash: 401e988e41a25a999c047bf93b6794388d38461e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456795"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. 

Para saber mais sobre o runtime do IoT Edge, consulte [Reconhecer o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on your Windows x64 (AMD/Intel) system using Windows containers.

> [!NOTE]
> A known Windows operating system issue prevents transition to sleep and hibernate power states when IoT Edge modules (process-isolated Windows Nano Server containers) are running. This issue impacts battery life on the device.
>
> As a workaround, use the command `Stop-Service iotedge` to stop any running IoT Edge modules before using these power states. 

O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para o Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. To learn more, see [Use IoT Edge on Windows to run Linux containers](how-to-install-iot-edge-windows-with-linux.md).

For information about what's included in the latest version of IoT Edge, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação. 

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

For development and test scenarios, Azure IoT Edge with Windows containers can be installed on any version of Windows 10 or Windows Server 2019 (build 17763) that supports the containers feature. For information about which operating systems are currently supported for production scenarios, see [Azure IoT Edge supported systems](support.md#operating-systems). 

IoT Core devices must include the IoT Core- Windows Containers optional feature to support the IoT Edge runtime. Use the following command in a [remote PowerShell session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) to check that Windows containers are supported on your device: 

```powershell
Get-Service vmcompute
```

If the service is present, you should get a successful response with the service status listed as **running**. If the vmcompute service is not found, then your device does not meet the requirements for IoT Edge. Contact your hardware provider to ask about support for this feature. 

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner 

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows no dispositivo Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concordar com os termos da licença, não use o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de runtime, o agente do IoT Edge, que permite implantações remotas de outros módulos. 

>[!TIP]
>For IoT Core devices, we recommend running the installation commands using a RemotePowerShell session. For more information, see [Using PowerShell for Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando você instala o runtime do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. A single IoT Edge device can be provisioned manually using a device connection string provided by IoT Hub. Or, you can use the Device Provisioning Service (DPS) to automatically provision devices, which is helpful when you have many devices to set up. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

As seções a seguir descrevem os casos de uso comuns e os parâmetros do script de instalação do IoT Edge em um novo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: instalar e provisionar manualmente

In this first option, you provide a **device connection string** generated by IoT Hub to provision the device. 

This example demonstrates a manual installation with Windows containers:

1. If you haven't already, register a new IoT Edge device and retrieve the **device connection string**. Copy the connection string to use later in this section. You can complete this step using the following tools:

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

3. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

5. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. When prompted, provide the device connection string that you retrieved in step 1. The device connection string associates the physical device with a device ID in IoT Hub. 

   The device connection string takes the following format, and should not include quotation marks: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use the steps in [Verify successful installation](#verify-successful-installation) to check the status of IoT Edge on your device. 

Ao instalar e provisionar um dispositivo manualmente, é possível usar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

For more information about these installation options, skip ahead to learn about [all installation parameters](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: instalar e provisionar automaticamente

Nesta segunda opção, você provisiona o dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Provide the **Scope ID** from a Device Provisioning Service instance along with any other information specific to your preferred [attestation mechanism](../iot-dps/concepts-security.md#attestation-mechanism):

* [Create and provision a simulated IoT Edge device with a virtual TPM on Windows](how-to-auto-provision-simulated-device-windows.md)
* [Create and provision an IoT Edge device using symmetric key attestation](how-to-auto-provision-symmetric-keys.md)

When you install and provision a device automatically, you can use additional parameters to modify the installation including:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, continue lendo este artigo ou ignore para saber mais sobre [Todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

During installation two files are downloaded:

* Microsoft Azure IoT Edge cab, which contains the IoT Edge security daemon (iotedged), Moby container engine, and Moby CLI.
* MSI do pacote redistribuível do Visual C++ (runtime de VC)

You can download one or both of these files ahead of time to the device, then point the installation script at the directory that contains the files. O instalador primeiro verifica o diretório e, em seguida, baixa apenas os componentes que não são localizados. If all the files are available offline, you can install with no internet connection. You can also use this feature to install a specific version of the components.  

For the latest IoT Edge installation files along with previous versions, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

To install with offline components, use the `-OfflineInstallationPath` parameter as part of the Deploy-IoTEdge command and provide the absolute path to the file directory. Por exemplo,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

You can also use the offline installation path parameter with the Update-IoTEdge command, introduced later in this article. 

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

Verifique o status do serviço do IoT Edge. It should be listed as running.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando. If you just finished installing the IoT Edge runtime, you may see a list of errors from the time between running **Deploy-IoTEdge** and **Initialize-IoTEdge**. These errors are expected, as the service is trying to start before being configured. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Run an automated check for the most common configuration and networking errors.

```powershell
iotedge check
```

Módulos de execução da lista. After a new installation, the only module you should see running is **edgeAgent**. After you [deploy IoT Edge modules](how-to-deploy-modules-portal.md) for the first time, the other system module, **edgeHub**, will start on the device too. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Manage module containers

The IoT Edge service requires a container engine running on your device. When you deploy a module to a device, the IoT Edge runtime uses the container engine to pull the container image from a registry in the cloud. The IoT Edge service enables you to interact with your modules and retrieve logs, but sometimes you may want to use the container engine to interact with the container itself. 

For more information about module concepts, see [Understand Azure IoT Edge modules](iot-edge-modules.md). 

If you're running Windows containers on your Windows IoT Edge device, then the IoT Edge installation included the Moby container engine. The Moby engine was based on the same standards as Docker, and was designed to run in parallel on the same machine as Docker Desktop. For that reason, if you want to target containers managed by the Moby engine, you have to specifically target that engine instead of Docker. 

For example, to list all Docker images, use the following command:

```powershell
docker images
```

To list all Moby images, modify the same command with a pointer to the Moby engine: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

The engine URI is listed in the output of the installation script, or you can find it in the container runtime settings section for the config.yaml file. 

![moby_runtime uri in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

For more information about commands you can use to interact with containers and images running on your device, see [Docker command-line interfaces](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

If you've already installed the IoT Edge runtime on a device before and provisioned it with an identity from IoT Hub, then you can update the runtime without having to reenter your device information. 

Para obter mais informações, consulte [Atualize o daemon de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um arquivo de configuração existente e usa contêineres do Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

When you update IoT Edge, you can use additional parameters to modify the update, including:

* Direct traffic to go through a proxy server, or
* Apontar o instalador para um diretório offline 
* Restarting without a prompt if necessary

You can't declare an IoT Edge agent container image with script parameters because that information is already set in the configuration file from the previous installation. Se você quiser modificar a imagem de contêiner do agente, faça isso no arquivo config.yaml. 

For more information about these update options, use the command `Get-Help Update-IoTEdge -full` or refer to [all installation parameters](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o seguinte comando em uma janela administrativa do PowerShell. Esse comando remove o runtime do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

The Uninstall-IoTEdge command does not work on Windows IoT Core. To remove IoT Edge from Windows IoT Core devices, you need to redeploy your Windows IoT Core image. 

For more information about uninstallation options, use the command `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários comuns de instalação com exemplos de como usar parâmetros para modificar o script de instalação. This section provides reference tables of the common parameters used to install, update, or uninstall IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

The Deploy-IoTEdge command downloads and deploys the IoT Edge Security Daemon and its dependencies. The deployment command accepts these common parameters, among others. For the full list, use the command `Get-Help Deploy-IoTEdge -full`.  

| . | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Nenhum | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

The Initialize-IoTEdge command configures IoT Edge with your device connection string and operational details. Much of the information generated by this command is then stored in the iotedge\config.yaml file. The initialization command accepts these common parameters, among others. For the full list, use the command `Get-Help Initialize-IoTEdge -full`. 

| . | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhum | **Parâmetro de opção**. If no provisioning type is specified, manual is the default value.<br><br>Declara que você fornecerá uma cadeia de conexão de dispositivo para provisionar o dispositivo manualmente |
| **Dps** | Nenhum | **Parâmetro de opção**. If no provisioning type is specified, manual is the default value.<br><br>Declara que você fornecerá uma ID do escopo do DPS (Serviço de Provisionamento de Dispositivos) e a ID de Registro do dispositivo para provisionar por meio do DPS.  |
| **DeviceConnectionString** | Uma cadeia de conexão de um dispositivo do IoT Edge registrado em um Hub IoT, entre aspas simples | **Necessário** para instalação manual. Se você não fornecer uma cadeia de conexão nos parâmetros de script, durante a instalação será solicitado que você forneça uma. |
| **ScopeId** | Uma ID do escopo de uma instância do Serviço de Provisionamento de Dispositivos associada ao Hub IoT. | **Necessário** para instalação de DPS. Se você não fornecer uma ID do escopo nos parâmetros do script, durante a instalação será solicitado que você forneça uma. |
| **RegistrationId** | Uma ID de registro gerada pelo dispositivo | **Required** for DPS installation if using TPM or symmetric key attestation. |
| **SymmetricKey** | The symmetric key used to provision the IoT Edge device identity when using DPS | **Required** for DPS installation if using symmetric key attestation. |
| **ContainerOs** | **Windows** ou **Linux** | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de imagem do agente do IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca sem interrupção mais recente para a imagem do agente do IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer a própria imagem de agente. Para obter mais informações, consulte [Reconhecer as marcas do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Usuário** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Senha** | Cadeia de caracteres de senha de segurança | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça a senha para acessar o registro. |

### <a name="update-iotedge"></a>Update-IoTEdge

| . | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | If no container OS is specified, Windows is the default value. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **RestartIfNeeded** | Nenhum | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| . | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Force** | Nenhum | This flag forces the uninstallation in case the previous attempt to uninstall was unsuccessful. 
| **RestartIfNeeded** | Nenhum | This flag allows the uninstall script to restart the machine without prompting, if necessary. |

## <a name="next-steps"></a>Próximos passos

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
