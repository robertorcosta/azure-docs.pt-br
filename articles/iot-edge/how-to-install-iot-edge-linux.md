---
title: Instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Azure IoT Edge installation instructions on Linux devices running Ubuntu or Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: ec463efb1282c311757bb90fd614e1247459c80f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457334"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. To learn more, see [Understand the Azure IoT Edge runtime and its architecture](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on an X64, ARM32, or ARM64 Linux device. Installation packages are provided for Ubuntu Server 16.04, Ubuntu Server 18.04, and Raspbian Stretch. Refer to [Azure IoT Edge supported systems](support.md#operating-systems) for a list of supported Linux operating systems and architectures.

>[!NOTE]
>Support for ARM64 devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name* ). Leia os termos da licença antes de usar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concordar com os termos da licença, não use o pacote.

## <a name="install-the-latest-runtime-version"></a>Install the latest runtime version

Use the following sections to install the most recent version of the Azure IoT Edge runtime onto your device. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrar a chave da Microsoft e o feed do repositório de software

Prepare your device for the IoT Edge runtime installation.

Install the repository configuration. Choose the **16.04** or **18.04** command that matches your device operating system:

* **Ubuntu Server 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copy the generated list.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Install Microsoft GPG public key

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalar o runtime de contêiner

O Azure IoT Edge depende de um runtime de contêiner [compatível com OCI](https://www.opencontainers.org/). For production scenarios, we recommended that you use the [Moby-based](https://mobyproject.org/) engine provided below. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o runtime Moby.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

Instale o mecanismo de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Instale a interface de linha de comando Moby (CLI). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

   ```bash
   sudo apt-get install moby-cli
   ```

If you encounter errors when installing the Moby container runtime, follow the steps to [Verify your Linux kernel for Moby compatibility](#verify-your-linux-kernel-for-moby-compatibility), provided later in this article. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

The **IoT Edge security daemon** provides and maintains security standards on the IoT Edge device. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do runtime do IoT Edge.

The installation command also installs the standard version of the **libiothsm** if not already present.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. O pacote está instalado em `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the [Configure the Azure IoT Edge security daemon](#configure-the-security-daemon) section to finish provisioning your device. 

## <a name="install-a-specific-runtime-version"></a>Install a specific runtime version

If you want to install a specific version of the Azure IoT Edge runtime, you can target the component files directly from the IoT Edge GitHub repository. Use the following steps to get all of the IoT Edge components onto your device: the Moby engine and CLI, the libiothsm, and finally the IoT Edge security daemon.

1. Navigate to the [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases), and find the release version that you want to target. 

2. Expand the **Assets** section for that version.

3. There may or may not be updates to the Moby engine in any given release. If you see files that start with **moby-engine** and **moby-cli**, use the following commands to update those components. If you don't see any Moby files, go back through the older release assets until you find the most recent version. 

   1. Find the **moby-engine** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address.

   2. Use the copied link in the following command to install that version of the Moby engine: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Find the **moby-cli** file that matches your IoT Edge device's architecture. The Moby CLI is an optional component, but can be helpful during development. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Every release should have new files for the IoT Edge security daemon and the hsmlib. Use the following commands to update those components. 

   1. Find the **libiothsm-std** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   2. Use the copied link in the following command to install that version of the hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Find the **iotedge** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the IoT Edge security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the next section to finish provisioning your device. 

## <a name="configure-the-security-daemon"></a>Configure the security daemon

Configure o runtime do IoT Edge para vincular seu dispositivo físico com uma identidade do dispositivo existente em um Hub IoT do Azure.

O daemon pode ser configurado usando o arquivo de configuração no `/etc/iotedge/config.yaml`. O arquivo está protegido contra gravação por padrão, permissões elevadas talvez sejam necessárias para editá-lo.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: provisionamento manual

Para provisionar um dispositivo manualmente, é necessário fornecer a ele uma [cadeia de conexão do dispositivo](how-to-register-device.md#register-in-the-azure-portal), que poderá ser criada registrando um novo dispositivo no Hub IoT.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the **Manual provisioning configuration** section. Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Make sure any other provisioning sections are commented out.

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
```
To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: provisionamento automático

Para provisionar um dispositivo automaticamente, [configure o Serviço de provisionamento de dispositivos e recupere a ID de registro do dispositivo](how-to-auto-provision-simulated-device-linux.md). There are a number of attestation mechanisms supported by IoT Edge when using automatic provisioning but your hardware requirements also impact your choices. For example, Raspberry Pi devices do not come with a Trusted Platform Module (TPM) chip by default.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the section appropriate for your attestation mechanism. When using TPM attestation, for example, update the values of **scope_id** and **registration_id** with the values from your IoT Hub Device Provisioning service and your IoT Edge device with TPM, respectively.

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

Se você tiver usado as etapas de **configuração manual** na seção anterior, o runtime do IoT Edge deve ser provisionado e em execução no seu dispositivo com êxito. Se você tiver usado as etapas de **configuração automática**, você precisará concluir algumas etapas adicionais para que o runtime pode registrar seu dispositivo no hub IoT em seu nome. For next steps, see [Create and provision a simulated TPM IoT Edge device on a Linux virtual machine](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

You can check the status of the IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine daemon logs:

```bash
journalctl -u iotedge --no-pager --no-full
```

Run an automated check for the most common configuration and networking errors: 

```bash
sudo iotedge check
```

And, list running modules:

```bash
sudo iotedge list
```

After installing IoT Edge on your device, the only module you should see running is **edgeAgent**. Once you create your first deployment, the other system module **$edgeHub** will start on the device as well. For more information, see [deploy IoT Edge modules](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

Você precisa de privilégios elevados para executar comandos `iotedge`. Após instalar o runtime, saia da sua máquina virtual e entre novamente para atualizar suas permissões automaticamente. Até lá, use **sudo** na frente de qualquer um dos comandos `iotedge`.

Em dispositivos com restrição de recursos, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas](troubleshoot.md).

Se a rede tem um servidor proxy, siga as etapas em [Configurar o dispositivo do IoT Edge para comunicar-se por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verify your Linux kernel for Moby compatibility

Many embedded device manufacturers ship device images that contain custom Linux kernels without the features required for container runtime compatibility. If you encounter issues while installing the recommended Moby container runtime, you may be able to troubleshoot your Linux kernel configuration using the [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script from the official [Moby GitHub repository](https://github.com/moby/moby). Run the following commands on the device to check your kernel configuration:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

This will provide a detailed output that contains the status of kernel features that are used by the Moby runtime. You will want to ensure that all items under `Generally Necessary` and  `Network Drivers` are enabled to ensure that your kernel is fully compatible with the Moby runtime.  If you have identified any missing features, enable them by rebuilding your kernel from source and selecting the associated modules for inclusion in the appropriate kernel .config.  Similarly, if you are using a kernel configuration generator like defconfig or menuconfig, find and enable the respective features and rebuild your kernel accordingly.  Once you have deployed your newly modified kernel, run the check-config script again to verify that all the required features were successfully enabled.


## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Para remover a instalação do IoT Edge do dispositivo Linux, use o seguinte comando em uma linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o runtime do IoT Edge for removido, o contêiner criado por ele será interrompido, mas ainda existará no seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem.

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

## <a name="next-steps"></a>Próximos passos

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

If you are having problems with the IoT Edge runtime installing properly, check out the [troubleshooting](troubleshoot.md) page.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
