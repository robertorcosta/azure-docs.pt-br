---
title: Automatically provision Windows devices with DPS - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo simulado em seu computador Windows para testar o provisionamento automático de dispositivos para o Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457148"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Create and provision a simulated IoT Edge device with a virtual TPM on Windows

Dispositivos do Azure IoT Edge podem ser autoprovisionados usando o [Serviço de provisionamento de dispositivo](../iot-dps/index.yml) assim como os dispositivos que não são habilitados de borda. Se você não estiver familiarizado com o processo de provisionamento automático, analise os [Conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

DPS supports symmetric key attestation for IoT Edge devices in both individual enrollment and group enrollment. For group enrollment, if you check “is IoT Edge device” option to be true in symmetric key attestation, all the devices that are registered under that enrollment group will be marked as IoT Edge devices. 

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um dispositivo simulado em seu computador Windows com um simulado Trusted Platform Module (TPM) para segurança de hardware.
* Crie um registro individual para o dispositivo.
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT.

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes testing auto-provisioning by using TPM attestation on virtual devices, but much of it applies when using physical TPM hardware as well.

## <a name="prerequisites"></a>Pré-requisitos

* Computador de desenvolvimento do Windows. Este artigo usa Windows 10.
* Um Hub IoT ativo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

> [!TIP]
> If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.
>
> Follow the instructions in the article [How to manage device enrollments with Azure Portal](../iot-dps/how-to-manage-enrollments.md) to create your enrollment in DPS and then proceed with the [Install the IoT Edge runtime](#install-the-iot-edge-runtime) section in this article to continue.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado em sua máquina de desenvolvimento do Windows. Retrieve the **Registration ID** and **Endorsement key** for your device, and use them to create an individual enrollment entry in DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

Escolha o idioma SDK que você deseja usar para criar o dispositivo simulado e siga as etapas até que você crie o registro individual.

When you create the individual enrollment, select **True** to declare that the simulated TPM device on your Windows development machine is an **IoT Edge device**.

Dispositivo simulado e guias de inscrição individuais:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar o registro individual, salve o valor do **ID de registro**. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

You'll need the following information when provisioning your device:

* The DPS **ID Scope** value
* The device **Registration ID** you created

Install the IoT Edge runtime on the device that is running the simulated TPM. You'll configure the IoT Edge runtime for automatic, not manual, provisioning.

> [!TIP]
> Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste.

For more detailed information about installing IoT Edge on Windows, including prerequisites and instructions for tasks like managing containers and updating IoT Edge, see [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Be sure to use an AMD64 session of PowerShell when installing IoT Edge, not PowerShell (x86).

1. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. Use the `-Dps` flag to use the Device Provisioning Service instead of manual provisioning.

   Replace the placeholder values for `{scope_id}` and `{registration_id}` with the data you collected earlier.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.  

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximos passos

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando a CLI do Azure](how-to-deploy-monitor-cli.md)
