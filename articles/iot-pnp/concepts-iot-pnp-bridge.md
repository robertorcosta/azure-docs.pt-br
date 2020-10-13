---
title: Ponte de Plug and Play IoT | Microsoft Docs
description: Entenda a ponte de Plug and Play IoT e como usá-la para conectar dispositivos existentes conectados a um gateway Windows ou Linux como dispositivos de IoT Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579624"
---
# <a name="iot-plug-and-play-bridge"></a>Ponte do IoT Plug and Play

A ponte de Plug and Play IoT é um aplicativo de software livre para conectar dispositivos existentes conectados ao gateway do Windows ou Linux como dispositivos de IoT Plug and Play. Depois de instalar e configurar o aplicativo em seu computador Windows ou Linux, você pode usá-lo para conectar dispositivos anexados a um hub IoT. Você pode usar a ponte para mapear interfaces de Plug and Play de IoT para a telemetria os dispositivos anexados estão enviando, trabalhar com propriedades de dispositivo e invocar comandos.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="No lado esquerdo, há alguns sensores existentes anexados (com e sem fio) a um PC Windows ou Linux que contém a ponte de Plug and Play IoT. Em seguida, a ponte de Plug and Play IoT se conecta a um hub IoT no lado direito":::

A ponte IoT Plug and Play pode ser implantada como um executável autônomo em qualquer dispositivo IoT, PC industrial, servidor ou gateway que executa o Windows 10 ou Linux. Ele também pode ser compilado no código do aplicativo. Um arquivo JSON de configuração simples informa ao IoT Plug and Play Bridge quais dispositivos/periféricos anexados devem ser expostos até o Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolos e sensores com suporte

A ponte de Plug and Play IoT dá suporte aos seguintes tipos de periféricos por padrão, com links para a documentação do adaptador:

|Periférico|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Sim|Não|
|[Câmeras](https://aka.ms/iot-pnp-bridge-camera)               |Sim|Não|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Sim|Sim|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Sim|Sim|
|[Série](https://aka.ms/iot-pnp-bridge-serial)                |Sim|Sim|
|[Periféricos USB do Windows](https://aka.ms/iot-pnp-bridge-usb)  |Sim|Não Aplicável|

>[!Important]
>Os desenvolvedores podem estender a ponte de Plug and Play IoT para dar suporte a protocolos de dispositivo adicionais por meio das instruções na **[documentação do desenvolvedor de ponte do iot plug and Play aqui](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="os-platform"></a>Plataforma do SO

Há suporte para as seguintes plataformas e versões de sistema operacional:

|Plataforma  |Versões compatíveis  |
|---------|---------|
|Windows 10 |     Todos os SKUs do Windows têm suporte. Por exemplo: IoT Enterprise, servidor, desktop, IoT Core. *Para a funcionalidade de monitoramento de integridade da câmera, a compilação 20H1 ou posterior é recomendada. Todas as outras funcionalidades estão disponíveis em todas as compilações do Windows 10.*  |
|Linux     |Testado e com suporte no Ubuntu 18, 4, a funcionalidade em outras distribuições não foi testada.         |
||

### <a name="hardware"></a>Hardware

- Qualquer plataforma de hardware capaz de dar suporte às versões e SKUs do sistema operacional acima.
- Os sensores serial, USB, Bluetooth e de câmera são suportados nativamente. A ponte de Plug and Play IoT pode ser estendida para dar suporte a qualquer periférico ou sensor personalizado ([consulte a seção periféricos acima](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Ambiente de desenvolvimento

Para criar, estender e desenvolver a ponte de Plug and Play IoT, você precisará:  

- Um ambiente de desenvolvimento que dá suporte à compilação de C++ como: [Visual Studio (Comunidade, profissional ou empresarial)](https://visualstudio.microsoft.com/downloads/)– certifique-se de incluir o desenvolvimento de desktop com carga de trabalho do C++ ao instalar o Visual Studio.
- [CMake](https://cmake.org/download/) – quando você instala o CMake, selecione a opção `Add CMake to the system PATH` .
- Se estiver criando no Windows, também será necessário baixar o SDK do Windows 17763: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [SDK do dispositivo C do Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-c). Os scripts de compilação incluídos neste repositório irão clonar automaticamente o SDK do Azure IoT C necessário para você.

### <a name="azure-iot-products-and-tools"></a>Produtos e ferramentas do Azure IoT

- **Hub IOT do Azure** -você precisará de um [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/) em sua assinatura do Azure para conectar seu dispositivo ao. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. Se você não tiver um hub IoT, [siga estas instruções para criar um](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> O IoT Plug and Play está disponível atualmente nos Hubs IoT criados nas regiões EUA Central, Norte da Europa e Leste do Japão. O suporte a IoT Plug and Play não está incluído em Hubs IoT da camada básica. Para interagir com o dispositivo de Plug and Play de IoT, você pode usar a ferramenta do Azure IoT Explorer. [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional.

## <a name="iot-plug-and-play-bridge-architecture"></a>Arquitetura de ponte de Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="No lado esquerdo, há alguns sensores existentes anexados (com e sem fio) a um PC Windows ou Linux que contém a ponte de Plug and Play IoT. Em seguida, a ponte de Plug and Play IoT se conecta a um hub IoT no lado direito":::

## <a name="download-iot-plug-and-play-bridge"></a>Baixar ponte de Plug and Play IoT

Você pode baixar uma versão pré-criada da ponte com adaptadores com suporte nas [versões do IoT plug and Play Bridge](https://aka.ms/iot-pnp-bridge-releases) e expandir a lista de ativos para a versão mais recente. Baixe a versão mais recente do aplicativo para seu sistema operacional.

Você também pode baixar e exibir o código-fonte do [IoT plug and Play Bridge no GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral da arquitetura do IoT Plug and Play Bridge, as próximas etapas são para saber mais sobre:

- [Como usar a ponte de Plug and Play IoT](./howto-use-iot-pnp-bridge.md)
- [Consulte a referência do desenvolvedor do GitHub para o IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [Ponte de Plug and Play IoT no GitHub](https://aka.ms/iotplugandplaybridge)
