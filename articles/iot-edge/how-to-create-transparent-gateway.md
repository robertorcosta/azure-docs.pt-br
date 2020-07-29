---
title: Criar um dispositivo de gateway transparente - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo Azure IoT Edge como gateway transparente que possa processar informações de dispositivos downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782578"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para agir como gateway transparente

Este artigo fornece instruções detalhadas para configurar um IoT Edge dispositivo para funcionar como um gateway transparente para outros dispositivos se comunicarem com o Hub IoT. Este artigo usa o termo *IOT Edge gateway* para se referir a um dispositivo de IOT Edge configurado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>No momento:
>
> * Os dispositivos habilitados para o Edge não podem se conectar com gateways do IoT Edge.
> * Dispositivos downstream não podem usar o upload de arquivo.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a primeira etapa:

1. **Configure o dispositivo de gateway como um servidor para que os dispositivos downstream possam se conectar com segurança. Configure o gateway para receber mensagens de dispositivos downstream e roteá-los para o destino apropriado.**
2. Crie uma identidade de dispositivo para o dispositivo downstream para que ele possa autenticar com o Hub IoT. Configure o dispositivo downstream para enviar mensagens por meio do dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. Conecte o dispositivo downstream ao dispositivo de gateway e comece a enviar mensagens. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo atue como um gateway, ele precisa se conectar com segurança a seus dispositivos downstream. Azure IoT Edge permite que você use a infraestrutura de chave pública (PKI) para configurar conexões seguras entre dispositivos. Nesse caso, estamos permitindo que um dispositivo downstream se conecte a um dispositivo IoT Edge atuando como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de gateway. Essa verificação de identidade impede que os dispositivos se conectem a gateways potencialmente mal-intencionados.

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Esses aplicativos geralmente usam o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, um dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança de autoridade de certificação x. 509](../iot-hub/iot-hub-x509ca-overview.md) no Hub IOT, que envolve um certificado de autoridade de certificação x. 509 associado a um hub IOT específico (a AC raiz do Hub IOT), uma série de certificados assinados com essa AC e uma AC para o dispositivo IOT Edge.

>[!NOTE]
>O termo *certificado de autoridade de certificação raiz* usado em todos esses artigos refere-se ao certificado público mais alto da autoridade da cadeia de certificados PKI e não necessariamente à raiz do certificado de uma autoridade de certificação agregada. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

As etapas a seguir orientam você pelo processo de criação dos certificados e sua instalação nos locais certos no gateway. Você pode usar qualquer computador para gerar os certificados e, em seguida, copiá-los para seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Linux ou Windows com o IoT Edge instalado.

## <a name="set-up-the-device-ca-certificate"></a>Configurar o certificado de autoridade de certificação do dispositivo

Todos os gateways de IoT Edge precisam de um certificado de autoridade de certificação de dispositivo instalado neles. O daemon de segurança IoT Edge usa o certificado de AC de dispositivo IoT Edge para assinar um certificado de AC de carga de trabalho que, por sua vez, assina um certificado de servidor para o Hub de IoT Edge. O gateway apresenta seu certificado de servidor para o dispositivo downstream durante o início da conexão. O dispositivo downstream verifica se o certificado do servidor faz parte de uma cadeia de certificados que se acumula no certificado de autoridade de certificação raiz. Esse processo permite que o dispositivo downstream confirme se o gateway vem de uma fonte confiável. Para obter mais informações, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

O certificado de autoridade de certificação raiz e o certificado de autoridade de certificação do dispositivo (com sua chave privada) precisam estar presentes no dispositivo IoT Edge gateway e configurados no arquivo IoT Edge config. YAML. Lembre-se de que, nesse caso, o *certificado de AC raiz* significa a autoridade de certificação mais alta para esse cenário de IOT Edge. O certificado de CA do dispositivo de gateway e os certificados de dispositivo downstream precisam ser acumulados para o mesmo certificado de autoridade de certificação raiz.

>[!TIP]
>O processo de instalação do certificado de autoridade de certificação raiz e do certificado de AC do dispositivo em um IoT Edge dispositivo também é explicado em mais detalhes em [gerenciar certificados em um dispositivo IOT Edge](how-to-manage-device-certificates.md).

Prepare os seguintes arquivos:

* Certificado de AC raiz
* Certificado de autoridade de certificação de dispositivo
* Chave privada da AC do dispositivo

Para cenários de produção, você deve gerar esses arquivos com sua própria autoridade de certificação. Para cenários de desenvolvimento e teste, você pode usar certificados de demonstração.

1. Se você estiver usando certificados de demonstração, use o seguinte conjunto de etapas para criar seus arquivos:
   1. [Criar certificado de autoridade de certificação raiz](how-to-create-test-certificates.md#create-root-ca-certificate). No final dessas instruções, você terá um arquivo de certificado de autoridade de certificação raiz:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [Criar IOT Edge certificado de autoridade de certificação do dispositivo](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). No final dessas instruções, você terá dois arquivos, um certificado de autoridade de certificação do dispositivo e sua chave privada:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` e
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Se você criou esses arquivos em um computador diferente, copie-os para o dispositivo IoT Edge.

3. No dispositivo IoT Edge, abra o arquivo de configuração do daemon de segurança.
   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

4. Localize a seção **certificados** do arquivo e forneça os URIs de arquivo para os três arquivos como valores para as seguintes propriedades:
   * **device_ca_cert**: certificado de autoridade de certificação do dispositivo
   * **device_ca_pk**: chave privada da AC do dispositivo
   * **trusted_ca_certs**: certificado de autoridade de certificação raiz

5. Salve e feche o arquivo.

6. Reinicie IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar edgeHub no gateway

Quando você instala o IoT Edge pela primeira vez em um dispositivo, somente um módulo do sistema é iniciado automaticamente: o agente do IoT Edge. Depois de criar a primeira implantação para um dispositivo, o segundo módulo do sistema, o Hub de IoT Edge, também será iniciado.

O Hub de IoT Edge é responsável por receber mensagens de entrada de dispositivos downstream e roteá-los para o próximo destino. Se o módulo **edgeHub** não estiver em execução no seu dispositivo, crie uma implantação inicial para seu dispositivo. A implantação ficará vazia porque você não adiciona nenhum módulo, mas verificará se ambos os módulos do sistema estão em execução.

Você pode verificar quais módulos estão sendo executados em um dispositivo verificando os detalhes do dispositivo na portal do Azure, exibindo o status do dispositivo no Visual Studio ou Visual Studio Code, ou executando o comando `iotedge list` no próprio dispositivo.

Se o módulo **edgeAgent** estiver em execução sem o módulo **edgeHub** , use as seguintes etapas:

1. No portal do Azure, navegue para o hub IoT.

2. Vá para **IoT Edge** e selecione o dispositivo Edge IoT que você deseja usar como um gateway.

3. Selecione **definir módulos**.

4. Selecione **Avançar: rotas**.

5. Na página **rotas** , você deve ter uma rota padrão que envia todas as mensagens, seja de um módulo ou de um dispositivo downstream, para o Hub IOT. Caso contrário, adicione uma nova rota com os seguintes valores e selecione **revisar + criar**:
   * **Nome**:`route`
   * **Valor**:`FROM /messages/* INTO $upstream`

6. Na página **revisar + criar** , selecione **criar**.

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo de gateway

Os dispositivos IoT Edge padrão não precisam de nenhuma conectividade de entrada para funcionar, pois toda a comunicação com o Hub IoT é feita por meio de conexões de saída. Os dispositivos de gateway são diferentes porque precisam receber mensagens de seus dispositivos downstream. Se um firewall estiver entre os dispositivos downstream e o dispositivo de gateway, a comunicação também precisará ser possível por meio do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub de IoT Edge deve estar aberto para o tráfego de entrada de dispositivos downstream. Os protocolos com suporte são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Rotear de mensagens de dispositivos downstream

O runtime do IoT Edge pode rotear as mensagens enviadas dos dispositivos downstream assim como as mensagens enviadas pelos módulos. Esse recurso permite que você execute análises em um módulo em execução no gateway antes de enviar qualquer dado para a nuvem.

Atualmente, a maneira como você roteia as mensagens enviadas pelos dispositivos downstream é diferenciando-as de mensagens enviadas pelos módulos. Todas as mensagens enviadas por módulos contêm uma propriedade de sistema chamada **connectionModuleId**, mas as mensagens enviadas pelos dispositivos downstream não. Você pode usar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade do sistema.

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo downstream para um módulo chamado `ai_insights` e, em seguida, do `ai_insights` Hub IOT.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Para saber mais sobre o roteamento de mensagens, consulte [Implantar módulos e estabelecer rotas](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Habilitar operação offline estendida

A partir da [versão 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de execução do IOT Edge, o dispositivo de gateway e os dispositivos downstream que se conectam a ele podem ser configurados para operação estendida offline.

Com esse recurso, os módulos locais ou dispositivos downstream podem se autenticar novamente com o dispositivo IoT Edge conforme necessário e se comunicar entre si usando mensagens e métodos mesmo quando estiverem desconectados do Hub IoT. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge dispositivos, módulos e dispositivos filho](offline-capabilities.md).

Para habilitar recursos offline estendidos, você estabelece uma relação pai-filho entre um dispositivo IoT Edge gateway e dispositivos downstream que se conectarão a ele. Essas etapas são explicadas em mais detalhes no próximo artigo desta série, [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge configurado como um gateway transparente, você precisa configurar seus dispositivos downstream para confiar no gateway e enviar mensagens para ele. Continue em para [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para as próximas etapas em Configurando seu cenário de gateway transparente.
