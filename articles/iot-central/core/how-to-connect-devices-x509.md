---
title: Conectar dispositivos com certificados X. 509 em um aplicativo de IoT Central do Azure
description: Como conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IoT Central aplicativo
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9a93602327b5c5294d6c17c1804c04c6603dcf37
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999884"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Como conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IoT Central aplicativo

O IoT Central dá suporte a SAS (assinaturas de acesso compartilhado) e a certificados X. 509 para proteger a comunicação entre um dispositivo e seu aplicativo. O tutorial [criar e conectar um aplicativo cliente ao seu aplicativo IOT central do Azure](./tutorial-connect-device-nodejs.md) usa SAS. Neste artigo, você aprenderá a modificar o exemplo de código para usar X. 509.  Os certificados X.509 são recomendados para ambientes de produção. Para obter mais informações, consulte [conectar-se ao Azure IOT central](./concepts-get-connected.md).

Este artigo mostra duas maneiras de usar os registros X. 509- [Group](how-to-connect-devices-x509.md#use-a-group-enrollment) normalmente usados em um ambiente de produção e [registros individuais](how-to-connect-devices-x509.md#use-an-individual-enrollment) úteis para teste.

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão de [criar e conectar um aplicativo cliente ao tutorial do Node.js (aplicativo IOT central do Azure)](./tutorial-connect-device-nodejs.md) .
- [Git](https://git-scm.com/download/).
- Baixe e instale o [OpenSSL](https://www.openssl.org/). Se você estiver usando o Windows, poderá usar os binários da [página OpenSSL em sourceforge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Usar um registro de grupo

Use certificados X. 509 com um registro de grupo em um ambiente de produção. Em um registro de grupo, você adiciona um certificado X. 509 intermediário ou raiz ao seu aplicativo IoT Central. Dispositivos com certificados folha derivados do certificado raiz ou intermediário podem se conectar ao seu aplicativo.

## <a name="generate-root-and-device-cert"></a>Gerar certificado de dispositivo e raiz

Nesta seção, você usa um certificado X. 509 para conectar um dispositivo com um certificado derivado do certificado do grupo de registro, que pode se conectar ao seu aplicativo IoT Central.

> [!WARNING]
> Essa forma de gerar certificados X. 509 é apenas para teste. Para um ambiente de produção, você deve usar seu mecanismo oficial e seguro para a geração de certificado.

1. Abra um prompt de comando. Clone o repositório GitHub para os scripts de geração de certificado:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navegue até o script gerador de certificado e instale os pacotes necessários:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Crie um certificado raiz e, em seguida, derive um certificado de dispositivo executando o script. Certifique-se de usar apenas caracteres alfanuméricos minúsculos e hifens para o nome do certificado:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

Esses comandos produzem três arquivos cada para a raiz e o certificado do dispositivo

nome do arquivo | conteúdos
-------- | --------
\<name\>_cert. pem | A parte pública do certificado X509
\<name\>_key. pem | A chave privada para o certificado X509
\<name\>_fullchain. pem | O conjunto de chaves inteiro para o certificado X509.

## <a name="create-a-group-enrollment"></a>Criar um registro de grupo

1. Abra o aplicativo IoT Central e navegue até **Administração**  no painel esquerdo e selecione **conexão do dispositivo**.

1. Selecione **+ Criar grupo de registro**e crie um novo grupo de registro chamado _MyX509Group_ com um tipo de atestado de **certificados (X. 509)**.

1. Abra o grupo de registro que você criou e selecione **gerenciar primário**.

1. Selecione a opção arquivo e carregue o arquivo de certificado raiz chamado _mytestrootcert_cert. pem_ que você gerou anteriormente:

    ![Upload de certificado](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Para concluir a verificação, gere o código de verificação, copie-o e, em seguida, use-o para criar um certificado de verificação X. 509 no prompt de comando:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Carregue o certificado de verificação assinado _verification_cert. pem_ para concluir a verificação:

    ![Certificado verificado](./media/how-to-connect-devices-x509/verified.png)

Agora você pode conectar dispositivos que têm um certificado X. 509 derivado deste certificado raiz primário. Depois de salvar o grupo de registro, anote o escopo da ID.

## <a name="run-sample-device-code"></a>Executar código de dispositivo de exemplo

1. No aplicativo IoT Central do Azure, selecione **dispositivos**e crie um novo dispositivo com _MYTESTDEVICE_ como a **ID do dispositivo** do modelo de dispositivo do **sensor ambiental** .

1. Copie os arquivos _mytestdevice_key. pem_ e _mytestdevice_cert. pem_ para a pasta que contém o aplicativo _environmentalSensor.js_ . Você criou esse aplicativo quando concluiu o [tutorial conectar um dispositivo (Node.js)](./tutorial-connect-device-nodejs.md).

1. Navegue até a pasta que contém o aplicativo environmentalSensor.js e execute o seguinte comando para instalar o pacote X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Edite o arquivo de **environmentalSensor.js** .
    - Substitua o `idScope` valor pelo **escopo de ID** anotado anteriormente.
    - Substituir `registrationId` valor por `mytestdevice` .

1. Edite as `require` instruções da seguinte maneira:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Edite a seção que cria o cliente da seguinte maneira:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. Modifique a seção que abre a conexão da seguinte maneira:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Execute o script e verifique se o dispositivo foi provisionado com êxito:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Você também pode verificar se a telemetria aparece no painel.

    ![Verificar telemetria do dispositivo](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Usar um registro individual

Use certificados X. 509 com um registro individual para testar seu dispositivo e sua solução. Em um registro individual, não há nenhum certificado X. 509 raiz ou intermediário no aplicativo IoT Central. Os dispositivos usam um certificado X. 509 autoassinado para se conectar ao seu aplicativo.

## <a name="generate-self-signed-device-cert"></a>Gerar certificado de dispositivo autoassinado

Nesta seção, você usa um certificado X. 509 autoassinado para conectar dispositivos para registro individual, que são usados para registrar um único dispositivo. Os certificados autoassinados são apenas para teste.

Crie um certificado de dispositivo X. 509 autoassinado executando o script. Certifique-se de usar apenas caracteres alfanuméricos minúsculos e hifens para o nome do certificado:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Criar registro individual

1. No aplicativo IoT Central do Azure, selecione **dispositivos**e crie um novo dispositivo com a **ID do dispositivo** como _Mytestselfcertprimary_ do modelo de dispositivo sensor ambiental. Anote o **escopo da ID**, você o usará mais tarde.

1. Abra o dispositivo que você criou e selecione **conectar**.

1. Selecione **registros individuais** como o **método Connect** e **certificados (X. 509)** como mecanismo:

    ![Registro individual](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selecione a opção arquivo em primário e carregue o arquivo de certificado chamado _mytestselfcertprimary_cert. pem_ que você gerou anteriormente.

1. Selecione a opção arquivo para o certificado secundário e carregue o arquivo de certificado chamado _mytestselfcertsecondary_cert. PEM._ Em seguida, selecione **salvar**:

    ![Carregamento de certificado de registro individual](./media/how-to-connect-devices-x509/individual-enrollment.png)

O dispositivo agora é provisionado com o certificado X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Executar um dispositivo de registro individual de exemplo

1. Copie os arquivos _mytestselfcertprimary_key. pem_ e _mytestselfcertprimary_cert. pem_ para a pasta que contém o aplicativo environmentalSensor.js. Você criou esse aplicativo quando concluiu o [tutorial conectar um dispositivo (Node.js)](./tutorial-connect-device-nodejs.md).

1. Edite o arquivo de **environmentalSensor.js** da seguinte maneira e salve-o.
    - Substitua o `idScope` valor pelo **escopo de ID** anotado anteriormente.
    - Substituir `registrationId` valor por `mytestselfcertprimary` .
    - Substitua **var deviceCert** como:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Execute o script e verifique se o dispositivo foi provisionado com êxito:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Você também pode verificar se a telemetria aparece no painel.

    ![Registro individual de telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

Você também pode repetir as etapas acima para o certificado _mytestselfcertsecondary_ .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar dispositivos usando certificados X. 509, a próxima etapa sugerida é aprender a [monitorar a conectividade do dispositivo usando o CLI do Azure](howto-monitor-devices-azure-cli.md)
