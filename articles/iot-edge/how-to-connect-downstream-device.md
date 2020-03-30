---
title: Conectar dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Como configurar dispositivos a jusante ou folha para conectar-se aos dispositivos gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510797"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Conecte um dispositivo downstream a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma conexão confiável entre dispositivos downstream e gateways transparentes ioT Edge. Em um cenário de gateway transparente, um ou mais dispositivos podem passar suas mensagens através de um único dispositivo gateway que mantém a conexão com o IoT Hub. Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser um aplicativo em execução no próprio dispositivo de gateway IoT Edge.

Existem três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo abrange o terceiro passo:

1. O dispositivo gateway precisa se conectar com segurança a dispositivos a jusante, receber comunicações de dispositivos a jusante e encaminhar mensagens para o destino adequado. Para obter mais informações, consulte [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. O dispositivo a jusante precisa de uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub, e saber se comunicar através de seu dispositivo gateway. Para obter mais informações, consulte [Authenticate um dispositivo downstream no Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **O dispositivo a jusante precisa se conectar ao dispositivo gateway com segurança.**

Este artigo identifica problemas comuns com conexões de dispositivos downstream e orienta você na configuração de seus dispositivos de recebimento de dados por meio de:

* Explicando a segurança do protocolo TLS e os fundamentos do certificado.
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operacionais e como cada sistema operacional lida com certificados.
* Explicando passo a passo exemplos de IoT do Azure em vários idiomas para ajudar você a começar.

Neste artigo, os termos *gateway* e *gateway IoT Edge* se referem a um dispositivo IoT Edge configurado como um gateway transparente.

## <a name="prerequisites"></a>Pré-requisitos

* Tenha o arquivo de certificado **azure-iot-test-only.root.ca.cert.pem** que foi gerado no [Configure um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md) disponível em seu dispositivo downstream. O dispositivo downstream usa este certificado para validar a identidade do dispositivo gateway.
* Tenha a seqüência de conexão modificada que aponta para o dispositivo gateway, conforme explicado no [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser um aplicativo em execução no próprio dispositivo de gateway IoT Edge. No entanto, outro dispositivo IoT Edge não pode ser a jusante de um gateway IoT Edge.

>[!NOTE]
>Dispositivos IoT que têm identidades registradas no IoT Hub podem usar [gêmeos de módulo](../iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções em um único dispositivo. Os gateways IoT Edge suportam conexões de módulos a jusante usando autenticação de chave simétrica, mas não autenticação de certificado X.509.

Para conectar um dispositivo downstream a um gateway IoT Edge, você precisa de duas coisas:

* Um dispositivo ou aplicativo configurado com uma cadeia de conexão de dispositivo Hub IoT anexada com informações para conectá-lo ao gateway.

    Esta etapa é explicada no [Authenticate um dispositivo downstream para o Azure IoT Hub](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicativo tem que confiar no certificado **de CA raiz** do gateway para validar as conexões TLS ao dispositivo gateway.

    Esta etapa é explicada detalhadamente no resto deste artigo. Esta etapa pode ser realizada de duas maneiras: instalando o certificado CA na loja de certificados do sistema operacional, ou (para determinados idiomas) fazendo referência ao certificado dentro de aplicativos usando os SDKs IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Conceitos básicos TLS e o certificado

O desafio de conectar com segurança dispositivos downstream ao IoT Edge é como qualquer outra comunicação cliente/servidor segura que ocorre na Internet. Um cliente e um servidor se comunicam com segurança pela Internet usando [ segurança do protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído usando as construções [padrão de infraestrutura de chave pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas de certificados. O TLS é uma especificação bastante envolvida e aborda uma ampla gama de tópicos relacionados à garantia de dois pontos finais. Esta seção resume os conceitos relevantes para você conectar dispositivos com segurança a um gateway IoT Edge.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada de *cadeia de certificados do servidor*. Uma cadeia de certificados normalmente inclui um certificado de autoridade de certificação raiz (AC), um ou mais certificados AC intermediários e, finalmente, o próprio certificado do servidor. Um cliente estabelece confiança com um servidor, verificando criptograficamente toda a cadeia de certificados do servidor. Essa validação do cliente da cadeia de certificados do servidor é chamada *validação da cadeia do servidor*. O cliente desafia criptograficamente o serviço para comprovar a posse da chave privada associada ao certificado do servidor em um processo chamado *prova de posse*. A combinação de validação da cadeia do servidor e prova de posse é chamada *de autenticação do servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de autoridade de certificação raiz que foi usado para criar o certificado do servidor (ou emitir uma). Normalmente ao se conectar a sites, um navegador vem pré-configurado com certificados de autoridade de certificação comumente usados para que o cliente tem um processo contínuo.

Quando um dispositivo se conecta ao Hub IoT do Azure, o dispositivo é o cliente e o serviço de nuvem do Hub IoT é o servidor. O serviço de nuvem Hub IoT é respaldado por um certificado de AC raiz chamado **Baltimore CyberTrust Root**, que está publicamente disponível e é amplamente usado. Como o certificado CA do Hub IoT já está instalado na maioria dos dispositivos, muitas implementações TLS (OpenSSL, Schannel, LibreSSL) o usam automaticamente durante a validação do certificado do servidor. Um dispositivo que pode se conectar com êxito ao Hub IoT pode ter problemas ao tentar se conectar a um gateway IoT Edge.

Quando um dispositivo se conecta a um gateway IoT Edge, o dispositivo downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criem cadeias de certificados de gateway da maneira que acharem melhor. O operador pode optar por usar um certificado de autoridade de certificação público, como o Baltimore, ou usar um certificado de autoridade de certificação raiz autoassinado (ou interno). Os certificados públicos de CA costumam ter um custo associado a eles, então são normalmente usados em cenários de produção. Os certificados de CA autoassinados são preferidos para desenvolvimento e teste. Os artigos de configuração de gateway transparentelistados na introdução usam certificados de CA raiz auto-assinados.

Quando você usa um certificado de autoridade de certificação AC raiz autoassinado para um gateway IoT Edge, ele precisa ser instalado ou fornecido a todos os dispositivos de recebimento de dados que tentam se conectar ao gateway.

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre os certificados do IoT Edge e algumas implicações de produção, consulte [Detalhes do uso do certificado do IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Forneça o certificado de CA raiz

Para verificar os certificados do dispositivo gateway, o dispositivo a jusante precisa de sua própria cópia do certificado de CA raiz. Se você usou os scripts fornecidos no repositório git do IoT Edge para criar certificados de teste, então o certificado de CA raiz será chamado **de azure-iot-test-only.root.ca.cert.pem**. Se você ainda não tiver feito parte das outras etapas de preparação do dispositivo a jusante, mova este arquivo de certificado para qualquer diretório em seu dispositivo a jusante. Você pode usar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo Secure [copy](https://www.ssh.com/ssh/scp/) para mover o arquivo do certificado.

## <a name="install-certificates-in-the-os"></a>Instale certificados no SISTEMA OPERACIONAL

A instalação do certificado de CA raiz na loja de certificados do sistema operacional geralmente permite que a maioria dos aplicativos use o certificado de CA raiz. Existem algumas exceções, como aplicativos NodeJS que não usam a loja de certificados do SO, mas usam a loja de certificados internos do Node runtime. Se você não puder instalar o certificado no nível do sistema operacional, pule para [usar certificados com SDKs IoT Do Zure](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado de CA em um host Ubuntu. Este exemplo pressupõe que você está usando o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos pré-requisitos e que você copiou o certificado para um local no dispositivo a jusante.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Você deve ver uma mensagem que diz: "Atualizando certificados em /etc/ssl/certs... 1 adicionado, 0 removido; feito.

### <a name="windows"></a>Windows

As etapas a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows. Este exemplo pressupõe que você está usando o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos pré-requisitos e que você copiou o certificado para um local no dispositivo a jusante.

Você pode instalar certificados usando o [Certificado de Importação](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) da PowerShell como administrador:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Você também pode instalar certificados usando o utilitário **certlm:**

1. No menu Iniciar, pesquise e selecione **Gerenciar certificados de computador**. Um utilitário chamado **certlm** é aberto.
2. Navegar para **certificados - Autoridades locais** > **de certificação raiz confiável em computadores**.
3. Clique com o botão direito do mouse em **Certificates** e selecione **All Tasks** > **Import**. O assistente para importação de certificados deve ser iniciado.
4. Siga as etapas conforme direcionado e importe o arquivo de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluído, você verá uma mensagem "Importada com êxito".

Você também pode instalar certificados programaticamente usando APIs .NET, conforme mostrado na amostra .NET mais adiante neste artigo.

Geralmente, os aplicativos usam a pilha TLS fornecida pelo Windows chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectar com segurança através de TLS. O Schannel *requer* que quaisquer certificados sejam instalados no repositório de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usar certificados com SDKs de IoT do Azure

Esta seção descreve como os SDKs do IoT do Azure se conectam a um dispositivo IoT Edge usando aplicativos de amostra simples. O objetivo de todas as amostras é conectar o cliente do dispositivo e enviar mensagens de telemetria ao gateway, em seguida, fechar a conexão e sair.

Ter duas coisas prontas antes de usar os exemplos de nível de aplicativo:

* A seqüência de conexão IoT Hub do dispositivo a jusante foi modificada para apontar para o dispositivo gateway e quaisquer certificados necessários para autenticar seu dispositivo a jusante para o IoT Hub. Para obter mais informações, consulte [Authenticate um dispositivo downstream no Azure IoT Hub](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de AC raiz que você copiou e salvou em algum lugar em seu dispositivo downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT NodeJS a um gateway do IoT Edge. Para aplicações NodeJS, você deve instalar o certificado DE CA raiz no nível de aplicação, conforme mostrado aqui. Os aplicativos NodeJS não usam a loja de certificados do sistema.

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

1. Obtenha a amostra para **send_message** do [dispositivo Azure IoT SDK para amostras Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios).
2. Certifique-se de que você está executando em um contêiner IoT `EdgeHubConnectionString` `EdgeModuleCACertificateFile` Edge, ou em um cenário de depuração, tenha as variáveis de ambiente definidas.
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

## <a name="test-the-gateway-connection"></a>Testar a conexão de gateway

Use este comando de amostra para testar se o dispositivo downstream pode se conectar ao dispositivo gateway:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Este comando testa conexões sobre MQTTS (porta 8883). Se você estiver usando um protocolo diferente, ajuste o comando conforme necessário para AMQPS (5671) ou HTTPS (433)

A saída deste comando pode ser longa, incluindo informações sobre todos os certificados da cadeia. Se sua conexão for bem sucedida, `Verification: OK` `Verify return code: 0 (ok)`você verá uma linha como ou .

![Verificar conexão de gateway](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Solucionar problemas na conexão gateway

Se o dispositivo leaf tiver conexão intermitente com o dispositivo gateway, tente as seguintes etapas para resolução.

1. O nome de host do gateway na seqüência de conexão é o mesmo que o valor do nome do host no arquivo IoT Edge config.yaml no dispositivo gateway?
2. O nome de host do gateway é solucionável para um endereço IP? Você pode resolver conexões intermitentes usando DNS ou adicionando uma entrada de arquivo host no dispositivo folha.
3. As portas de comunicação estão abertas no firewall? A comunicação com base no protocolo utilizado (MQTTS:8883/AMQPS:5671/HTTPS:433) deve ser possível entre o dispositivo downstream e o IoT Edge transparente.

## <a name="next-steps"></a>Próximas etapas

Saiba como o IoT Edge pode estender [recursos off-line](offline-capabilities.md) para dispositivos downstream.
