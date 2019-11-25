---
title: Automatically provision Linux devices with DPS - Azure IoT Edge | Microsoft Docs
description: Use um TPM simulado em uma VM Linux para testar o Serviço de Provisionamento de Dispositivos do Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457176"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Create and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

Azure IoT Edge devices can be automatically provisioned using the [Device Provisioning Service](../iot-dps/index.yml). Se você não estiver familiarizado com o processo de provisionamento automático, analise os [Conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Crie uma máquina virtual Linux (VM) no Hyper-V com Trusted Platform Module (TPM) para segurança de hardware.
* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um registro individual para o dispositivo
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes how to test DPS provisioning using a TPM simulator, but much of it applies to physical TPM hardware such as the [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), an Azure Certified for IoT device.
>
> If you're using a physical device, you can skip ahead to the [Retrieve provisioning information from a physical device](#retrieve-provisioning-information-from-a-physical-device) section in this article.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento do Windows com [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo usa o Windows 10 em execução de uma VM do Ubuntu Server.
* Um Hub IoT ativo.
* If using a simulated TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crie máquinas virtuais Linux com um TPM virtual

In this section, you create a new Linux virtual machine on Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge. 

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual se conecte a uma rede física.

1. Open Hyper-V Manager on your Windows machine. 

2. No menu **Ações**, selecione **Gerenciador de Comutador Virtual**. 

3. Escolha um comutador virtual **Externo** e, em seguida, selecione **Criar Comutador Virtual**. 

4. Nomeie seu novo comutador virtual, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de conexão é definido como **rede externa**, em seguida, selecione **Ok**.

5. Um pop-up avisa que a conectividade de rede pode ser interrompida. Escolha **Sim** para continuar. 

Se você vir erros ao criar o novo comutador virtual, verifique se nenhum outro comutador está usando o adaptador Ethernet e se nenhum outro comutador usa o mesmo nome. 

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. Baixe um arquivo de imagem de disco para usar para sua máquina virtual e salve-o localmente. Por exemplo: [servidor Ubuntu](https://www.ubuntu.com/download/server). 

2. In Hyper-V Manager again, select **New** > **Virtual Machine** in the **Actions** menu.

3. Conclua o **Assistente de nova máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar geração**: selecione **geração 2**. Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   2. **Configurar rede**: defina o valor de **Conexão** para o comutador virtual que você criou na seção anterior. 
   3. **Opções de instalação**: Selecione **Instalar um sistema operacional a partir de um arquivo de imagem inicializável** e navegue até o arquivo de imagem de disco que você salvou localmente.

4. Select **Finish** in the wizard to create the virtual machine.

A criação da nova VM pode levar alguns minutos. 

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Once your VM is created, open its settings to enable the virtual trusted platform module (TPM) that lets you auto-provision the device.

1. Select the virtual machine, then open its **Settings**.

2. Navegar para **Segurança**. 

3. Desmarque a opção **habilitar inicialização segura**.

4. Verificar **Habilitar Trusted Platform Module**. 

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e coletar dados TPM

In the virtual machine, build a tool that you can use to retrieve the device's **Registration ID** and **Endorsement key**.

1. Start your virtual machine and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

1. Sign in to your VM, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >In the course of this article, you'll copy to and paste from the virtual machine, which is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address: `ifconfig`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. From a command window, navigate to the `azure-iot-sdk-c` directory and run the TPM simulator. Ele escuta em um soquete nas portas 2321 e 2322. Do not close this command window; you will need to keep this simulator running.

   From the `azure-iot-sdk-c` directory, run the following command to start the simulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Using Visual Studio, open the solution generated in the `cmake` directory named `azure_iot_sdks.sln`, and build it using the **Build solution** command on the **Build** menu.

1. No painel **Gerenciador de Soluções** no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**.

1. Run the solution using either of the **Start** commands on the **Debug** menu. The output window displays the TPM simulator's **Registration ID** and the **Endorsement key**, which you should copy for use later when you create an individual enrollment for your device in You can close this window (with Registration ID and Endorsement key), but leave the TPM simulator window running.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Retrieve provisioning information from a physical device

On your device, build a tool that you can use to retrieve the device's provisioning information.

1. Follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM device.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copy the values for **Registration ID** and **Endorsement key**. Você pode usar esses valores para criar um registro individual para seu dispositivo no DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Recuperar as informações de provisionamento da sua máquina virtual e usá-la para criar um registro individual no serviço de provisionamento do dispositivo. 

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md). 

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of IoT Hub Device Provisioning Service. 

2. Em **Configurações**, selecione **Gerenciar registros**. 

3. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Em **Mecanismo**, selecione **TPM**. 

   2. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine.

      > [!TIP]
      > If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

   3. Select **True** to declare that this virtual machine is an IoT Edge device. 

   4. Escolha o **IoT Hub** vinculado que você deseja conectar o dispositivo. You can choose multiple hubs, and the device will be assigned to one of them according to the selected allocation policy. 

   5. Forneça uma ID para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. If you don't provide a device ID, the registration ID is used.

   6. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Por exemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Clique em **Salvar**. 

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation. 

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o runtime do IoT Edge em sua máquina virtual. 

Saiba seu DPS **Escopo da ID** e do dispositivo **ID de registro** antes de começar o artigo que combine com o seu tipo de dispositivo. Se você instalou o servidor do Ubuntu de exemplo, use as instruções **x64**. Certifique-se de configurar o runtime do IoT Edge para provisionamento automático, não manual. 

[Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acesso de IoT Edge no TPM

Em ordem para o runtime do IoT Edge provisionar automaticamente o seu dispositivo, ele precisa acessar o TPM. 

Você pode conceder acesso TPM no runtime do IoT Edge, substituindo as configurações de systemd, de modo que o serviço **iotedge** tenha privilégios de raiz. Se você não deseja elevar os privilégios de serviço, você também pode usar as etapas a seguir para fornecer manualmente o acesso TPM. 

1. Localizar o caminho do arquivo para o módulo de hardware do TPM no seu dispositivo e salve-o como uma variável local. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará ao runtime do IoT Edge acesso ao tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso para o arquivo de regras. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salve e feche o arquivo. 

6. Dispare o sistema de udev para avaliar a nova regra. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com êxito.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem sucedida se parece com o seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se você não vir que as permissões corretas foram aplicadas, tente reinicializar o computador para atualizar udev. 

## <a name="restart-the-iot-edge-runtime"></a>Reinicie o runtime do IoT Edge

Reinicie o runtime do IoT Edge para que ele pega todas as alterações de configuração feitas no dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o runtime do IoT Edge está sendo executado. 

   ```bash
   sudo systemctl status iotedge
   ```

Se você vir erros de provisionamento, pode ser que as alterações de configuração ainda não entraram em vigor. Tente reiniciar o daemon do IoT Edge novamente. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Ou então, tente reiniciar sua máquina virtual para ver se as alterações entram em vigor em um novo início. 

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e veja que o novo dispositivo foi provisionado automaticamente. Agora seu dispositivo está pronto para executar os módulos do IoT Edge. 

Verifique o status do Daemon do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed. 

## <a name="next-steps"></a>Próximos passos

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando a CLI do Azure](how-to-deploy-monitor-cli.md).
