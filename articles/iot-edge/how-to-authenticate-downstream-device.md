---
title: Autenticar dispositivos a jusante - Azure IoT Edge | Microsoft Docs
description: Como autenticar dispositivos a jusante ou dispositivos de folha para o IoT Hub e encaminhar sua conexão através de dispositivos gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8db3fedc5886e86d5f49739b87b26535665bdbc
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389317"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo downstream no Hub IoT do Azure

Em um cenário de gateway transparente, dispositivos a jusante (às vezes chamados de dispositivos folha ou dispositivos infantis) precisam de identidades no IoT Hub como qualquer outro dispositivo. Este artigo percorre as opções para autenticar um dispositivo downstream para o IoT Hub e, em seguida, demonstra como declarar a conexão gateway.

Existem três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo abrange o segundo passo:

1. O dispositivo gateway precisa ser capaz de se conectar com segurança a dispositivos a jusante, receber comunicações de dispositivos a jusante e encaminhar mensagens para o destino adequado. Para obter mais informações, consulte [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. **O dispositivo a jusante precisa ter uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub, e saber se comunicar através de seu dispositivo gateway.**
3. O dispositivo a jusante precisa se conectar ao dispositivo gateway com segurança. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Os dispositivos downstream podem autenticar-se com o IoT Hub usando um dos três métodos: chaves simétricas (às vezes referidas como chaves de acesso compartilhadas), certificados auto-assinados X.509 ou certificados assinados pela Autoridade de Certificado X.509 (CA). As etapas de autenticação são semelhantes às etapas usadas para configurar qualquer dispositivo não-IoT-Edge com IoT Hub, com pequenas diferenças para declarar a relação gateway.

As etapas deste artigo mostram provisionamento manual de dispositivos, não provisionamento automático com o Azure IoT Hub Device Provisioning Service (DPS). O provisionamento de dispositivos a jusante com DPS não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

Complete as etapas em [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). Se você estiver usando a autenticação X.509 para o seu dispositivo a jusante, você precisa usar o mesmo script gerador de certificado que você configurou no artigo de gateway transparente.

Este artigo refere-se ao *nome de host gateway* em vários pontos. O nome de host do gateway é declarado no parâmetro **hostname** do arquivo config.yaml no dispositivo gateway IoT Edge. É referido na seqüência de conexão do dispositivo a jusante. O nome de host do gateway precisa ser solucionável em um endereço IP, usando DNS ou uma entrada de arquivo host.

## <a name="register-device-symmetric-key"></a>Dispositivo de registro (chave simétrica)

Autenticação de chave simétrica, ou autenticação de chave de acesso compartilhado, é a maneira mais simples de autenticar com o IoT Hub. Com autenticação de chave simétrica, uma chave base64 está associada ao seu ID de dispositivo IoT no IoT Hub. Você inclui essa chave em seus aplicativos IoT para que seu dispositivo possa apresentá-la quando ele se conectar ao IoT Hub.

### <a name="create-the-device-identity"></a>Crie a identidade do dispositivo

Adicione um novo dispositivo IoT em seu hub IoT, usando o portal Azure, Azure CLI ou a extensão IoT para Visual Studio Code. Lembre-se que os dispositivos a jusante precisam ser identificados no IoT Hub como dispositivos IoT regulares, não dispositivos IoT Edge.

Quando você criar a nova identidade do dispositivo, forneça as seguintes informações:

* Crie um ID para o seu dispositivo.

* Selecione **a tecla Simétrica** como o tipo de autenticação.

* Opcionalmente, escolha **definir um dispositivo pai** e selecionar o dispositivo gateway IoT Edge pelo qual este dispositivo a jusante se conectará. Esta etapa é opcional para autenticação de chave simétrica, mas é recomendada porque a configuração de um dispositivo pai permite [recursos off-line](offline-capabilities.md) para o dispositivo a jusante. Você sempre pode atualizar os detalhes do dispositivo para adicionar ou alterar o pai mais tarde.

   ![Criar iD de dispositivo com tecla simétrica auth no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Você pode usar a [extensão IoT do Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação. O exemplo a seguir cria um novo dispositivo IoT com autenticação de chave simétrica e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Para obter mais informações sobre os comandos Azure CLI para criação de dispositivos e gerenciamento de pais e filhos, consulte o conteúdo de referência para comandos [de identidade de dispositivo do hub az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Em seguida, [recupere e modifique a seqüência](#retrieve-and-modify-connection-string) de conexão para que seu dispositivo saiba se conectar através de seu gateway.

## <a name="register-device-x509-self-signed"></a>Dispositivo de registro (X.509 auto-assinado)

Para a autenticação auto-assinada X.509, às vezes chamada de autenticação de impressão digital, você precisa criar novos certificados para colocar em seu dispositivo IoT. Esses certificados têm uma impressão digital que você compartilha com o IoT Hub para autenticação.

Se você não tiver uma autoridade de certificado para criar certificados X.509, você pode [criar certificados de demonstração para testar os recursos do dispositivo IoT Edge](how-to-create-test-certificates.md). Ao gerar certificados de teste para o seu dispositivo downstream, use o mesmo certificado de CA raiz que gerou os certificados para o seu dispositivo gateway.

1. Usando seu certificado CA, crie dois certificados de dispositivo (principal e secundário) para o dispositivo a jusante.

   O certificado do dispositivo deve ter o Nome de assunto definido no ID do dispositivo que você usará ao registrar o dispositivo IoT no Hub IoT do Azure. Esta configuração é necessária para autenticação.

2. Recupere a impressão digital SHA1 (chamada de impressão digital na interface IoT Hub) de cada certificado, que é uma seqüência de caracteres hexadecimal de 40 hexadecimais. Use o seguinte comando openssl para visualizar o certificado e encontrar a impressão digital:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Execute este comando duas vezes, uma para o certificado principal e outra para o certificado secundário. Você fornece impressões digitais para ambos os certificados quando registra um novo dispositivo IoT usando certificados X.509 auto-assinados.

3. Navegue até o seu hub de IoT no portal Azure e crie uma nova identidade de dispositivo IoT com os seguintes valores:

   * Forneça o **ID** do dispositivo que corresponde ao nome de assunto dos certificados do seu dispositivo.
   * Selecione **X.509 Auto-Assinado** como o tipo de autenticação.
   * Cole as cordas hexadecimais que você copiou dos certificados primários e secundários do seu dispositivo.
   * Selecione **Definir um dispositivo pai** e escolha o dispositivo gateway IoT Edge pelo qual este dispositivo a jusante se conectará. Um dispositivo pai é necessário para a autenticação X.509 de um dispositivo a jusante.

   ![Criar iD de dispositivo com X.509 auto-assinado auth no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie o certificado do dispositivo e as chaves de qualquer local no dispositivo a jusante. Mova também uma cópia do certificado de CA raiz compartilhado que gerou tanto o certificado de dispositivo gateway quanto os certificados de dispositivo a jusante.

   Você fará referência a esses arquivos nos aplicativos do dispositivo folha que se conectam ao IoT Hub. Você pode usar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo Secure [copy](https://www.ssh.com/ssh/scp/) para mover os arquivos do certificado.

5. Dependendo do seu idioma preferido, revise amostras de como os certificados X.509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configure a segurança X.509 em seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Você pode usar a [extensão IoT do Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivos. O exemplo a seguir cria um novo dispositivo IoT com autenticação auto-assinada X.509 e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obter mais informações sobre os comandos Azure CLI para criação de dispositivos, geração de certificados e gerenciamento de pais e filhos, consulte o conteúdo de referência para comandos [de identidade de dispositivo do hub az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Em seguida, [recupere e modifique a seqüência](#retrieve-and-modify-connection-string) de conexão para que seu dispositivo saiba se conectar através de seu gateway.

## <a name="register-device-x509-ca-signed"></a>Dispositivo de registro (X.509 CA assinado)

Para autenticação assinada pela Autoridade de Certificado X.509 (CA), você precisa de um certificado DE CA raiz registrado no IoT Hub que você usa para assinar certificados para o seu dispositivo IoT. Qualquer dispositivo que use um certificado que tenha sido emitido pelo certificado de CA raiz ou qualquer um de seus certificados intermediários será autorizado a autenticar.

Esta seção é baseada nas instruções detalhadas no artigo IoT Hub [Configure a segurança X.509 em seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md). Siga as etapas desta seção para saber quais valores usar para configurar um dispositivo a jusante que se conecta através de um gateway.

Se você não tiver uma autoridade de certificado para criar certificados X.509, você pode [criar certificados de demonstração para testar os recursos do dispositivo IoT Edge](how-to-create-test-certificates.md). Ao gerar certificados de teste para o seu dispositivo downstream, use o mesmo certificado de CA raiz que gerou os certificados para o seu dispositivo gateway.

1. Siga as instruções nos [certificados de CA Register X.509 para a](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) seção de hub IoT da *segurança De configuração X.509 em seu hub Azure IoT*. Nessa seção, você executa as seguintes etapas:

   1. Faça upload de um certificado de CA raiz. Se você estiver usando os certificados de demonstração, o CA raiz é ** \<o caminho>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verifique se você possui esse certificado de CA raiz.

2. Siga as instruções no [Criar um dispositivo X.509 para a](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) seção de hub IoT da segurança Set up *X.509 em seu hub Azure IoT*. Nessa seção, você executa as seguintes etapas:

   1. Adicione um novo dispositivo. Forneça um nome minúsculo para **ID do dispositivo**e escolha o tipo de autenticação **X.509 CA Signed**.
   2. Defina um dispositivo pai. Para dispositivos a jusante, **selecione Definir um dispositivo pai** e escolha o dispositivo gateway IoT Edge que fornecerá a conexão ao IoT Hub.

3. Crie uma cadeia de certificados para o dispositivo a jusante. Use o mesmo certificado de CA raiz que você enviou para o IoT Hub para fazer essa cadeia. Use o mesmo ID de dispositivo minúsculo que você deu à sua identidade de dispositivo no portal.

4. Copie o certificado do dispositivo e as chaves de qualquer local no dispositivo a jusante. Mova também uma cópia do certificado de CA raiz compartilhado que gerou tanto o certificado de dispositivo gateway quanto os certificados de dispositivo a jusante.

   Você fará referência a esses arquivos nos aplicativos do dispositivo folha que se conectam ao IoT Hub. Você pode usar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo Secure [copy](https://www.ssh.com/ssh/scp/) para mover os arquivos do certificado.

5. Dependendo do seu idioma preferido, revise amostras de como os certificados X.509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configure a segurança X.509 em seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Você pode usar a [extensão IoT do Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivos. O exemplo a seguir cria um novo dispositivo IoT com autenticação assinada pela CA X.509 e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para obter mais informações, consulte o conteúdo de referência azure CLI para comandos [az iot hub de identidade de dispositivo.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Em seguida, [recupere e modifique a seqüência](#retrieve-and-modify-connection-string) de conexão para que seu dispositivo saiba se conectar através de seu gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperar e modificar a seqüência de conexão

Depois de criar uma identidade de dispositivo IoT no portal, você pode recuperar suas chaves primárias ou secundárias. Uma dessas chaves precisa ser incluída na seqüência de conexões que os aplicativos usam para se comunicar com o IoT Hub. Para autenticação de chave simétrica, o IoT Hub fornece a seqüência de conexão totalmente formada nos detalhes do dispositivo para sua conveniência. Você precisa adicionar informações extras sobre o dispositivo gateway à seqüência de conexões.

As seqüências de conexão para dispositivos a jusante precisam dos seguintes componentes:

* O hub IoT ao que o dispositivo se conecta:`Hostname={iothub name}.azure-devices.net`
* O ID do dispositivo registrado no hub:`DeviceID={device ID}`
* A chave primária ou secundária:`SharedAccessKey={key}`
* O dispositivo de gateway através do que o dispositivo se conecta. Forneça o valor do **hostname** a partir do arquivo config.yaml do dispositivo de gateway IoT Edge:`GatewayHostName={gateway hostname}`

Todos juntos, uma seqüência de conexão completa parece:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se você estabeleceu uma relação pai/filho para este dispositivo a jusante, então você pode simplificar a seqüência de conexões chamando o gateway diretamente como o host de conexão. As relações pai/filho são necessárias para a autenticação X.509, mas opcionais para autenticação de chave simétrica. Por exemplo: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Neste ponto, você deve ter um dispositivo IoT Edge registrado e configurado como um gateway. Você também tem um dispositivo IoT a jusante registrado e apontando para o dispositivo gateway. O passo final é colocar certificados no seu dispositivo a jusante para que ele possa se conectar com segurança ao gateway.

Continue para o próximo artigo na série gateway, [Conecte um dispositivo downstream a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Próximas etapas

Ao concluir este artigo, você deve ter um dispositivo IoT Edge funcionando como um gateway transparente e um dispositivo a jusante registrado em um hub IoT. Em seguida, você precisa configurar seus dispositivos a jusante para confiar no dispositivo gateway e conectá-lo com segurança. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).
