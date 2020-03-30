---
title: Suporte da plataforma de SDKs do dispositivo IoT do Azure | Microsoft Docs
description: Os SDKs de dispositivos de código aberto estão disponíveis no GitHub em C, .NET (C#), Java, Node.js e Python, para conectar dispositivos ao Azure IoT Hub and Device Provisioning Service (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045126"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Suporte à plataforma sdks do dispositivo Azure IoT

A Microsoft se esforça para expandir continuamente o universo de dispositivos capazes de IoT Hub Do Zure IoT. A Microsoft publica SDKs de dispositivos de código aberto no GitHub para ajudar a conectar dispositivos ao Azure IoT Hub e ao Serviço de Provisionamento de Dispositivos. Os SDKs do dispositivo estão disponíveis para C, .NET (C#), Java, Node.js e Python. A Microsoft testa cada SDK para garantir que ele seja executado nas configurações suportadas detalhadas para ele na seção de suporte à [plataforma de dispositivos da Microsoft SDKs e dispositivos.](#microsoft-sdks-and-device-platform-support)

Além dos SDKs do dispositivo, a Microsoft fornece vários outros caminhos para capacitar clientes e desenvolvedores a conectar seus dispositivos ao Azure IoT:

* A Microsoft colabora com várias empresas parceiras para ajudá-los a publicar kits de desenvolvimento, baseados no Azure IoT C SDK, para suas plataformas de hardware.

* A Microsoft trabalha com parceiros confiáveis da Microsoft para fornecer um conjunto cada vez maior de dispositivos que foram testados e certificados para o Azure IoT. Para obter uma lista atual desses dispositivos, consulte o [certificado Azure para catálogo de dispositivos IoT](https://catalog.azureiotsolutions.com/).

* A Microsoft fornece uma camada de abstração de plataforma (PAL) no Azure IoT Hub Device C SDK que ajuda os desenvolvedores a portar facilmente o SDK para sua plataforma. Para saber mais, consulte a [orientação de portação c SDK](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Este tópico fornece informações sobre os SDKs da Microsoft e as configurações da plataforma que eles suportam, bem como cada uma das outras opções listadas acima.

## <a name="microsoft-sdks-and-device-platform-support"></a>Suporte a SDKs e dispositivos da Microsoft

A Microsoft publica SDKs de código aberto no GitHub para os seguintes idiomas: C, .NET (C#), Node.js, Java e Python. Os SDKs e suas dependências estão listados nesta seção. Os SDKs são suportados em qualquer plataforma de dispositivo que satisfaça essas dependências.

Para cada um dos SDKs listados, Microsoft:

* Continuamente, realiza e executa testes de ponta a ponta contra o ramo mestre do SDK relevante no GitHub em várias plataformas populares.  Para fornecer cobertura de teste em diferentes versões de compilador, geralmente testamos contra a versão mais recente do LTS e a versão mais popular.

* Fornece orientação de instalação ou pacotes de instalação, se aplicável.

* Suporta totalmente os SDKs no GitHub com código aberto, um caminho para contribuições de clientes e engajamento da equipe de produtos com problemas do GitHub.

### <a name="c-sdk"></a>SDK C

O [Dispositivo Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c) é testado e suporta as seguintes configurações.

| Sistema operacional                  | Biblioteca TLS                  | Requisitos adicionais                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL ou BearSSL | Soquetes de Berkeley</br></br>Interface do sistema operacional portátil (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulado em OSX 10.13.4                                                               |
| Família Windows 10   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [Kit de dev MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | Wolfssl                      | [Esfera azure MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | UrsoSsl                      | [ESP32 ou ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>SDK do Python

O [Dispositivo Azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python) é testado e suporta as seguintes configurações.

| Sistema operacional                  | Compilador                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 ou posterior |
| MacOS High Sierra   | Python 2.7.*, 3.5 ou posterior |
| Família Windows 10   | Python 2.7.*, 3.5 ou posterior |

Apenas a versão Python 3.5.3 ou posterior suporta as APIs assíncronas, recomendamos o uso da versão 3.7 ou posterior.

### <a name="net-sdk"></a>SDK .NET

O [dispositivo Azure IoT Hub .NET (C#) sDK](https://github.com/Azure/azure-iot-sdk-csharp) é testado e suporta as seguintes configurações.

| Sistema operacional                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop and Server SKUs   | .NET Core 2.1, .NET Framework 4.5.1 ou .NET Framework 4.7 |

O SDK .NET também pode ser usado com o Windows IoT Core com o [Azure Device Agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) ou [um NTService personalizado que pode usar o RPC para se comunicar com aplicativos UWP](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>SDK do Node.js

O [dispositivo Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node) é testado e suporta as seguintes configurações.

| Sistema operacional                  | Versão do nó    |
|---------------------|-----------------|
| Linux               | LTS e Corrente |
| Família Windows 10   | LTS e Corrente |

### <a name="java-sdk"></a>Java SDK

O [Dispositivo Azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java) é testado e suporta as seguintes configurações.

| Sistema operacional                     | Versão Java |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Família Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kits de desenvolvimento suportados por parceiros

A Microsoft trabalha com vários parceiros para fornecer kits de desenvolvimento para várias arquiteturas de microprocessadores. Esses parceiros portaram o Azure IoT C SDK para sua plataforma. Os parceiros criam e mantêm a camada de abstração da plataforma (PAL) do SDK. A Microsoft trabalha com esses parceiros para fornecer suporte estendido.

| Partner (parceiro)             | Dispositivos                            | Link                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE para SDK de IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta STM32L4 para nó de IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | Plataforma de lançamento CC3220SF </br> Plataforma de lançamento CC3220S </br> Plataforma de lançamento CC3235SF </br> Plataforma de lançamento CC3235s </br> Plataforma de lançamento MSP432E4 | [Plugin do IoT do Azure para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum de TI E2E](https://e2e.ti.com) <br/> [Fórum de TI E2E para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum de TI E2E para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Portando o Microsoft Azure IoT C SDK

Se a plataforma do seu dispositivo não estiver coberta por uma das seções anteriores, você pode considerar a portação do Azure IoT C SDK. A portação do C SDK envolve principalmente a implementação da camada de abstração da plataforma (PAL) do SDK. O PAL define primitivos que fornecem a cola entre seu dispositivo e funções de alto nível no SDK. Para obter mais informações, consulte [Orientação de Portação](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Parceiros da Microsoft e dispositivos Azure IoT certificados

A Microsoft trabalha com uma série de parceiros para expandir continuamente o universo de IoT do Azure com dispositivos Testados e Certificados pelo Azure IoT.

* Para procurar dispositivos certificados pelo Azure IoT, consulte [o Microsoft Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com/).

* Para saber mais sobre o ecossistema Azure Certified for IoT, consulte [Junte-se ao ecossistema Certificado para IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Conectando-se ao IoT Hub sem um SDK

Se você não for capaz de usar um dos SDKs do dispositivo IoT Hub, você pode se conectar diretamente ao IoT Hub usando as [APIs IoT Hub REST](https://docs.microsoft.com/rest/api/iothub/) de qualquer aplicativo capaz de enviar e receber solicitações e respostas HTTPS.

## <a name="support-and-other-resources"></a>Suporte e outros recursos

Se você tiver problemas ao usar os SDKs do dispositivo Azure IoT, existem várias maneiras de buscar suporte. Você pode experimentar um dos seguintes canais:

**Relatórios de bugs** – Bugs nos SDKs do dispositivo podem ser relatados na página de problemas do projeto GitHub relevante. As correções passam rapidamente do projeto para as atualizações do produto.

* [Problemas de SDK do Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemas do Azure IoT Hub .NET (C#) SDK](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemas de SDK do Azure IoT Hub Java](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemas do Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemas de Python do Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-python/issues)

**Equipe de suporte ao cliente da Microsoft** – Os usuários que possuem um plano de [suporte](https://azure.microsoft.com/support/plans/) podem engajar a equipe de suporte ao cliente da Microsoft criando uma nova solicitação de suporte diretamente do [portal Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Solicitações de recursos** – As solicitações de recursos do Azure IoT são rastreadas através da [página Voz](https://feedback.azure.com/forums/321918-azure-iot)do Usuário do produto .

## <a name="next-steps"></a>Próximas etapas

* [SDKs de serviço e dispositivo](iot-hub-devguide-sdks.md)
* [Diretrizes de Portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
