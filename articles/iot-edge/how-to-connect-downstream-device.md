---
title: Conectar dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457127"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Conecte um dispositivo downstream a um gateway do Azure IoT Edge

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

Este artigo identifica problemas comuns com conexões de dispositivos downstream e orienta você na configuração de seus dispositivos de recebimento de dados por meio de: 

* Explicando a segurança do protocolo TLS e os fundamentos do certificado. 
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operacionais e como cada sistema operacional lida com certificados.
* Explicando passo a passo exemplos de IoT do Azure em vários idiomas para ajudar você a começar. 

Neste artigo, os termos *gateway* e *gateway IoT Edge* se referem a um dispositivo IoT Edge configurado como um gateway transparente. 

## <a name="prerequisites"></a>Pré-requisitos 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

Para conectar um dispositivo downstream a um gateway IoT Edge, você precisa de duas coisas:

* Um dispositivo ou aplicativo configurado com uma cadeia de conexão de dispositivo Hub IoT anexada com informações para conectá-lo ao gateway. 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>Conceitos básicos TLS e o certificado

O desafio de conectar com segurança dispositivos downstream ao IoT Edge é como qualquer outra comunicação cliente/servidor segura que ocorre na Internet. Um cliente e um servidor se comunicam com segurança pela Internet usando [ segurança do protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído usando as construções [padrão de infraestrutura de chave pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas de certificados. TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada de *cadeia de certificados do servidor*. Uma cadeia de certificados normalmente inclui um certificado de autoridade de certificação raiz (AC), um ou mais certificados AC intermediários e, finalmente, o próprio certificado do servidor. Um cliente estabelece confiança com um servidor, verificando criptograficamente toda a cadeia de certificados do servidor. This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de autoridade de certificação raiz que foi usado para criar o certificado do servidor (ou emitir uma). Normalmente ao se conectar a sites, um navegador vem pré-configurado com certificados de autoridade de certificação comumente usados para que o cliente tem um processo contínuo. 

Quando um dispositivo se conecta ao Hub IoT do Azure, o dispositivo é o cliente e o serviço de nuvem do Hub IoT é o servidor. O serviço de nuvem Hub IoT é respaldado por um certificado de AC raiz chamado **Baltimore CyberTrust Root**, que está publicamente disponível e é amplamente usado. Como o certificado CA do Hub IoT já está instalado na maioria dos dispositivos, muitas implementações TLS (OpenSSL, Schannel, LibreSSL) o usam automaticamente durante a validação do certificado do servidor. Um dispositivo que pode se conectar com êxito ao Hub IoT pode ter problemas ao tentar se conectar a um gateway IoT Edge.

Quando um dispositivo se conecta a um gateway IoT Edge, o dispositivo downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criem cadeias de certificados de gateway da maneira que acharem melhor. O operador pode optar por usar um certificado de autoridade de certificação público, como o Baltimore, ou usar um certificado de autoridade de certificação raiz autoassinado (ou interno). Os certificados públicos de CA costumam ter um custo associado a eles, então são normalmente usados em cenários de produção. Os certificados de CA autoassinados são preferidos para desenvolvimento e teste. The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

Quando você usa um certificado de autoridade de certificação AC raiz autoassinado para um gateway IoT Edge, ele precisa ser instalado ou fornecido a todos os dispositivos de recebimento de dados que tentam se conectar ao gateway. 

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre os certificados do IoT Edge e algumas implicações de produção, consulte [Detalhes do uso do certificado do IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado de CA em um host Ubuntu. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

As etapas a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. No menu Iniciar, pesquise e selecione **Gerenciar certificados de computador**. Um utilitário chamado **certlm** é aberto.
2. Navegue para **Certificados - Computador Local** > **Autoridades de Certificação Raiz Confiáveis**.
3. Clique com o botão direito do mouse em **Certificates** e selecione **All Tasks** > **Import**. O assistente para importação de certificados deve ser iniciado. 
4. Siga as etapas conforme direcionado e importe o arquivo de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluído, você verá uma mensagem "Importada com êxito". 

Você também pode instalar certificados programaticamente usando APIs .NET, conforme mostrado na amostra .NET mais adiante neste artigo. 

Geralmente, os aplicativos usam a pilha TLS fornecida pelo Windows chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectar com segurança através de TLS. O Schannel *requer* que quaisquer certificados sejam instalados no repositório de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usar certificados com SDKs de IoT do Azure

Esta seção descreve como os SDKs do IoT do Azure se conectam a um dispositivo IoT Edge usando aplicativos de amostra simples. O objetivo de todas as amostras é conectar o cliente do dispositivo e enviar mensagens de telemetria ao gateway, em seguida, fechar a conexão e sair. 

Ter duas coisas prontas antes de usar os exemplos de nível de aplicativo:

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de AC raiz que você copiou e salvou em algum lugar em seu dispositivo downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT NodeJS a um gateway do IoT Edge. For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

1. Obter a amostra para **edge_downstream_device.js** da [repositório de exemplos do SDK do dispositivo IoT do Azure para Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo edge_downstream_device.js, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para reconhecimento da amostra que você está executando, o snippet de código a seguir é como o SDK do cliente lê o arquivo de certificado e o utiliza para estabelecer uma conexão TLS segura: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta seção apresenta um aplicativo de exemplo para se conectar a um cliente do dispositivo IoT do Azure .NET para um gateway IoT Edge. No entanto, os aplicativos .NET são capazes de usar automaticamente quaisquer certificados instalados no repositório de certificados do sistema em hosts Linux e Windows.

1. Obtenha o exemplo para **EdgeDownstreamDevice** da [pasta de exemplos .NET do IoT Edge](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo **Properties / launchSettings.json**, atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH**. Se você quiser usar o certificado instalado no repositório de certificados confiáveis no sistema host, deixe essa variável em branco. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para instalar programaticamente um certificado confiável no repositório de certificados por meio de um aplicativo .NET, consulte a função **InstallCACert ()** no arquivo **EdgeDownstreamDevice / Program.cs**. Essa operação é idempotente, portanto, pode ser executada várias vezes com os mesmos valores sem efeito adicional. 

### <a name="c"></a>C

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT C a um gateway do IoT Edge. O C SDK pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para obter mais informações, consulte o [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). 

1. Obtenha o aplicativo **iotedge_downstream_device_sample** do [SDK do dispositivo IoT do Azure para amostras C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo iotedge_downstream_device_sample.c, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

O SDK do dispositivo IoT do Azure para C fornece uma opção para registrar um certificado de CA ao configurar o cliente. Essa operação não instala o certificado em nenhum lugar, mas usa um formato de cadeia de caracteres do certificado na memória. O certificado salvo é fornecido para a pilha TLS subjacente ao estabelecer uma conexão. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Nos hosts do Windows, se você não estiver usando o OpenSSL ou outra biblioteca TLS, o padrão do SDK será usar o Schannel. Para que o Schannel funcione, o certificado da AC raiz da IoT Edge deve ser instalado no repositório de certificados do Windows, não definido usando a operação `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo Java IoT do Azure a um gateway IoT Edge. 

1. Obter a amostra para **evento de envio** da [SDK do dispositivo IoT do Azure para exemplos de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

### <a name="python"></a>Python

Esta seção apresenta um aplicativo de exemplo para conectar um cliente de dispositivo Python do IoT do Azure a um gateway do IoT Edge. 

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 


## <a name="test-the-gateway-connection"></a>Testar a conexão de gateway

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>Próximos passos

Saiba como o IoT Edge pode estender [recursos off-line](offline-capabilities.md) para dispositivos downstream. 
