---
title: Criar um dispositivo de gateway transparente - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo Azure IoT Edge como gateway transparente que possa processar informações de dispositivos downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456786"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para agir como gateway transparente

This article provides detailed instructions for configuring an IoT Edge device to function as a transparent gateway for other devices to communicate with IoT Hub. Neste artigo, o termo *gateway do IoT Edge* se refere a um dispositivo de IoT Edge usado como um gateway transparente. For more information, see [How an IoT Edge device can be used as a gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>No momento:
> * Os dispositivos habilitados para o Edge não podem se conectar com gateways do IoT Edge. 
> * Dispositivos downstream não podem usar o upload de arquivo.

There are three general steps to set up a successful transparent gateway connection. This article covers the first step:

1. **The gateway device needs to be able to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination.**
2. The downstream device needs to have a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. The downstream device needs to be able to securely connect to its gateway device. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).


For a device to function as a gateway, it needs to be able to securely connect to its downstream devices. Azure IoT Edge permite que você use a infraestrutura de chave pública (PKI) para configurar conexões seguras entre dispositivos. Neste caso, nós estamos permitindo um dispositivo downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente. To maintain reasonable security, the downstream device should confirm the identity of the gateway device. This identity check prevents your devices from connecting to potentially malicious gateways.

A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo downstream pode até ser um aplicativo em execução no próprio dispositivo de gateway IoT Edge. However, an IoT Edge device cannot be downstream of an IoT Edge gateway. 

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. In this article, we assume the same certificate setup that you would use to enable [X.509 CA security](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub, which involves an X.509 CA certificate associated to a specific IoT hub (the IoT hub root CA), a series of certificates signed with this CA, and a CA for the IoT Edge device.

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the PKI certificate chain, and not necessarily the certificate root of a syndicated certificate authority. In many cases, it is actually an intermediate CA public certificate. 

The gateway presents its IoT Edge device CA certificate to the downstream device during the initiation of the connection. The downstream device checks to make sure the IoT Edge device CA certificate is signed by the root CA certificate. This process allows the downstream device to confirm that the gateway comes from a trusted source.

The following steps walk you through the process of creating the certificates and installing them in the right places on the gateway. Você pode usar qualquer computador para gerar os certificados e, em seguida, copiá-los para seu dispositivo IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

* A development machine to create certificates. 
* Um dispositivo do IoT Edge para configurar como gateway. Use the IoT Edge installation steps for one of the following operating systems:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Gerar certificados com o Windows

Use the steps in this section to generate test certificates on Windows. You can use a Windows machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

Os certificados gerados nesta seção são somente para fins de teste. 

### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para Windows no computador que você está usando para gerar os certificados. If you already have OpenSSL installed on your Windows device, you may skip this step, but ensure that openssl.exe is available in your PATH environment variable. 

There are several ways to install OpenSSL, including:

* **Easier:** Download and install any [third-party OpenSSL binaries](https://wiki.openssl.org/index.php/Binaries), for example, from [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe à variável de ambiente PATH. 
   
* **Recomendado:** Faça o download do código-fonte do OpenSSL e construa os binários em sua máquina sozinho ou por meio do [vcpkg](https://github.com/Microsoft/vcpkg). As instruções a seguir usam vcpkg para baixar o código-fonte, compilação e instalar o OpenSSL em seu computador Windows com etapas simples.

   1. Navegue para um diretório onde você deseja instalar vcpkg. Chamaremos esse diretório de *\<VCPKGDIR>* . Siga as instruções para fazer o download e instalar o [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Once vcpkg is installed, run the following command from a powershell prompt to install the OpenSSL package for Windows x64. Normalmente, a instalação leva cerca de 5 minutos para concluir.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adicione `<VCPKGDIR>\installed\x64-windows\tools\openssl` à variável de ambiente PATH para que o arquivo openssl.exe fique disponível para invocação.

### <a name="prepare-creation-scripts"></a>Preparar scripts de criação

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Abra uma janela do PowerShell no modo de administrador. 

2. Clone o repositório git que contém scripts para gerar certificados de não produção. Esses scripts ajudam você a criar os certificados necessários para configurar um gateway transparente. Use o comando `git clone` ou [faça o download do ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue até o diretório no qual você deseja trabalhar. Throughout this article, we'll call this directory *\<WRKDIR>* . All certificates and keys will be created in this working directory.

4. Copy the configuration and script files from the cloned repo into your working directory. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   If you downloaded the repo as a ZIP, then the folder name is `iotedge-master` and the rest of the path is the same. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Habilite o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Bring the functions used by the scripts into PowerShell's global namespace.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   The PowerShell window will display a warning that the certificates generated by this script are only for testing purposes, and should not be used in production scenarios.

8. Verify that OpenSSL has been installed correctly and make sure that there won't be name collisions with existing certificates. Se houver problemas, o script deve descrever como corrigi-los em seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta seção, você criará três certificados, conectando-os, em seguida, em uma cadeia. A colocação dos certificados em um arquivo de cadeia permite que você instale-os facilmente no seu dispositivo de gateway do IoT Edge e qualquer o dispositivo downstream.  

1. Create the root CA certificate and have it sign one intermediate certificate. The certificates are all placed in your working directory.

   ```powershell
   New-CACertsCertChain rsa
   ```

   This script command creates several certificate and key files, but we're going to refer to one in particular later in this article:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   This script command creates several certificate and key files, including two that we're going to refer to later in this article:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

Now that you have the certificates, skip ahead to [Install certificates on the gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Gerar certificados com o Linux

Use the steps in this section to generate test certificates on Linux. You can use a Linux machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

Os certificados gerados nesta seção são somente para fins de teste. 

### <a name="prepare-creation-scripts"></a>Preparar scripts de criação

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Clone o repositório git que contém scripts para gerar certificados de não produção. Esses scripts ajudam você a criar os certificados necessários para configurar um gateway transparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue até o diretório no qual você deseja trabalhar. We'll refer to this directory throughout the article as *\<WRKDIR>* . All certificate and key files will be created in this directory.
  
3. Copy the config and script files from the cloned IoT Edge repo into your working directory.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Criar certificados

Nesta seção, você criará três certificados, conectando-os, em seguida, em uma cadeia. A colocação dos certificados em um arquivo de cadeia permite que você instale-os facilmente no seu dispositivo de gateway do IoT Edge e qualquer o dispositivo downstream.  

1. Create the root CA certificate and one intermediate certificate. Esses certificados são colocados em *\<WRKDIR>* .

   If you've already created root and intermediate certificates in this working directory, don't run this script again. Rerunning this script will overwrite the existing certificates. Instead, proceed to the next step. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   The script creates several certificates and keys. Make note of one, which we'll refer to in the next section:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   The script creates several certificates and keys. Make note of two, which we'll refer to in the next section: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

## <a name="install-certificates-on-the-gateway"></a>Instalar certificados no gateway

Agora que você fez uma cadeia de certificados, precisa instalá-la no dispositivo de gateway do IoT Edge e configurar o runtime do IoT Edge para referenciar os novos certificados. 

1. Copie os seguintes arquivos de *\<WRKDIR >* . Salve-os em qualquer lugar no seu dispositivo IoT Edge. Vamos nos referir ao diretório de destino em seu dispositivo IoT Edge como *\<CERTDIR>* . 

   * Certificado de AC de dispositivo - `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Chave privada de AC do dispositivo - `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files.  If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

2. Abra o artigo de configuração do daemon de segurança do IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Set the **certificate** properties in the config.yaml file to the full path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Remember that indents in yaml are two spaces.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. On Linux devices, make sure that the user **iotedge** has read permissions for the directory holding the certificates. 

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar EdgeHub para o gateway

When you first install IoT Edge on a device, only one system module starts automatically: the IoT Edge agent. Para que seu dispositivo funcione como gateway, você precisa de ambos os módulos do sistema. If you haven't deployed any modules to your gateway device before, create an initial deployment for your device to start the second system module, the IoT Edge hub. The deployment will look empty because you don't add any modules in the wizard, but it will make sure that both system modules are running. 

Você pode verificar quais módulos estão em execução em um dispositivo com o comando `iotedge list`. If the list only returns the module **edgeAgent** without **edgeHub**, use the following steps:

1. No portal do Azure, navegue para o hub IoT.

2. Vá para **IoT Edge** e selecione o dispositivo Edge IoT que você deseja usar como um gateway.

3. Selecione **Definir Módulos**.

4. Selecione **Avançar**.

5. Na página **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Na página **Revisar modelo**, selecione **Enviar**.

## <a name="open-ports-on-gateway-device"></a>Open ports on gateway device

Standard IoT Edge devices don't need any inbound connectivity to function, because all communication with IoT Hub is done through outbound connections. Gateway devices are different because they need to receive messages from their downstream devices. If a firewall is between the downstream devices and the gateway device, then communication needs to be possible through the firewall as well.

For a gateway scenario to work, at least one of the IoT Edge hub's supported protocols must be open for inbound traffic from downstream devices. The supported protocols are MQTT, AMQP, and HTTPS. 

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Rotear de mensagens de dispositivos downstream
O runtime do IoT Edge pode rotear as mensagens enviadas dos dispositivos downstream assim como as mensagens enviadas pelos módulos. This feature allows you to perform analytics in a module running on the gateway before sending any data to the cloud. 

Atualmente, a maneira como você roteia as mensagens enviadas pelos dispositivos downstream é diferenciando-as de mensagens enviadas pelos módulos. Todas as mensagens enviadas por módulos contêm uma propriedade de sistema chamada **connectionModuleId**, mas as mensagens enviadas pelos dispositivos downstream não. Você pode usar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade do sistema. 

The below route is an example that would send messages from any downstream device to a module named `ai_insights`, and then from `ai_insights` to IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Para saber mais sobre o roteamento de mensagens, consulte [Implantar módulos e estabelecer rotas](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Enable extended offline operation

Starting with the [v1.0.4 release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) of the IoT Edge runtime, the gateway device and downstream devices connecting to it can be configured for extended offline operation. 

With this capability, local modules or downstream devices can re-authenticate with the IoT Edge device as needed and communicate with each other using messages and methods even when disconnected from the IoT hub. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge dispositivos, módulos e dispositivos filho](offline-capabilities.md).

To enable extended offline capabilities, you establish a parent-child relationship between an IoT Edge gateway device and downstream devices that will connect to it. Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Próximos passos

Agora que você tem um dispositivo IoT Edge funcionando como gateway transparente, você precisará configurar seus dispositivos downstream para confiar no gateway e enviar mensagens para ele. Continue on to [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for the next steps in setting up your transparent gateway scenario. 
