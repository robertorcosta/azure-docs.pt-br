---
title: Glossário de termos - IoT Plug and Play Preview | Microsoft Docs
description: Concepts - um glossário de termos comuns relacionados com IoT Plug and Play Preview.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025632"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossário de termos para IoT Plug and Play Preview

Definições de termos comuns como usado nos artigos IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Certificado Azure para Portal IoT

Você pode usar o site do [portal Azure Certified for IoT](https://aka.ms/ACFI) para:

- Complete o [processo de certificação](#device-certification) do seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device).
- Encontre [modelos de capacidade do dispositivo](#device-capability-model).
- Publique um modelo de capacidade do dispositivo no [repositório de modelos públicos.](#public-model-repository)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI é uma ferramenta de linha de comando multiplataforma para gerenciar recursos do Azure. A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir e testar [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Você pode usar a extensão para:

- Conecte-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhe com [propriedades](#properties)do dispositivo .
- Comandos do dispositivo [de chamada](#commands).
- Gerenciar [repositórios de modelos,](#model-repository) [interfaces](#interface)e [modelos de capacidade do dispositivo](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central é uma solução de software como serviço totalmente gerenciada que facilita a conexão, o monitor e o gerenciamento de seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Você pode usar [modelos de capacidade do dispositivo](#device-capability-model) para configurar automaticamente um aplicativo IoT Central para monitorar e gerenciar seus dispositivos.

## <a name="azure-iot-certification-service"></a>Serviço de certificação Azure IoT

O serviço de certificação Azure IoT executa um conjunto de testes de certificação quando você envia um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para certificação através do portal [Azure Certified for IoT](#azure-certified-for-iot-portal). Antes de adicionar um dispositivo ao [catálogo de dispositivos Certified for IoT,](#certified-for-iot-device-catalog)o dispositivo deve ser certificado.

## <a name="azure-iot-tools-extension"></a>Extensão do Azure IoT Tools

Azure IoT Tools é uma coleção de extensões no [código Visual Studio](#visual-studio-code) que ajudam você a interagir com o IoT Hub e desenvolver dispositivos IoT. Para o desenvolvimento de dispositivos IoT Plug and Play, ele ajuda você a:

- Modelos e [interfaces](#interface) [de capacidade do dispositivo](#device-capability-model) autor.
- Publicar para [modelo repositórios](#model-repository).
- Gerar código esqueleto para implementar a aplicação do dispositivo.

## <a name="azure-iot-explorer-tool"></a>Ferramenta de explorador azure IoT

O explorador Azure IoT é uma ferramenta gráfica que você pode usar para interagir e testar seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Depois de instalar a ferramenta em sua máquina local, você pode usá-la para:

- Veja os dispositivos conectados ao seu [hub IoT](#azure-iot-hub).
- Conecte-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhe com [propriedades](#properties)do dispositivo .
- Comandos do dispositivo [de chamada](#commands).

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT é um serviço gerenciado e hospedado na nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos que ele gerencia. [Os dispositivos IoT Plug and Play](#iot-plug-and-play-device) podem se conectar a um hub IoT. Uma solução de IoT usa um hub de IoT para habilitar:

- Dispositivos para enviar telemetria para uma solução baseada em nuvem.
- Uma solução baseada em nuvem para gerenciar dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>SDK do dispositivo IoT do Azure

Existem SDKs de dispositivo para vários idiomas que você pode usar para criar aplicativos clientes de dispositivos IoT Plug and Play. Um dos requisitos para a [certificação](#device-certification) do dispositivo é que o código cliente do dispositivo use um dos SDKs do dispositivo Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Certificado para catálogo de dispositivos IoT

O [catálogo de dispositivos Certified for IoT](https://catalog.azureiotsolutions.com/) lista dispositivos [IoT Plug and Play](#iot-plug-and-play-device) que passaram nos testes de certificação do [dispositivo.](#device-certification) Os [modelos de capacidade](#device-capability-model) do dispositivo para os dispositivos IoT Plug and Play no catálogo e publicados no repositório de modelos públicos.

## <a name="commands"></a>Comandos

Os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [gêmeo digital](#digital-twin). Por exemplo, um comando para reiniciar um dispositivo.

## <a name="common-interface"></a>Interface comum

Espera-se que todos os [dispositivos IoT Plug and Play](#iot-plug-and-play-device) implementem algumas [interfaces comuns.](#interface) Por exemplo, a interface de informações do dispositivo define informações de hardware e sistema operacional sobre o dispositivo. [A certificação do dispositivo](#device-certification) requer que seu dispositivo implemente várias interfaces comuns. Você pode recuperar definições comuns de interface do repositório de modelos públicos.

## <a name="company-model-repository"></a>Repositório de modelos da empresa

Uma organização pode usar um [repositório de modelos](#model-repository) da empresa como uma loja privada para modelos e [interfaces](#interface) [de capacidade de dispositivos.](#device-capability-model)

## <a name="connection-string"></a>Cadeia de conexão

Uma seqüência de conexões encapsula as informações necessárias para se conectar a um ponto final. Uma cadeia de conexão normalmente inclui o endereço das informações de segurança e de ponto de extremidade, mas os formatos da cadeia de conexão variam de acordo com os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- As seqüências de conexão do dispositivo permitem que [os dispositivos IoT Plug and Play](#iot-plug-and-play-device) se conectem aos pontos finais voltados para dispositivos em um hub IoT. O código do cliente em um dispositivo usa a seqüência de conexões para estabelecer uma conexão segura com um hub IoT.
- As strings de conexão IoT Hub permitem que soluções e ferramentas back-end se conectem com segurança aos pontos finais voltados para o serviço em um hub de IoT. Essas soluções e ferramentas gerenciam o hub de IoT e os dispositivos conectados a ele.
- As seqüências de conexão de repositório de modelos da empresa permitem que soluções e ferramentas back-end se conectem com segurança a um [repositório de modelos da empresa.](#company-model-repository) Essas soluções e ferramentas consomem ou gerenciam os [modelos](#device-capability-model) e [interfaces](#interface) de capacidade do dispositivo no repositório.

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Um modelo de capacidade do dispositivo descreve um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) e define o conjunto de [interfaces implementadas](#interface) pelo dispositivo. Um modelo de capacidade do dispositivo normalmente corresponde a um dispositivo físico, produto ou SKU. Você usa a [Linguagem de Definição Dupla Digital](#digital-twin-definition-language) para definir um modelo de capacidade do dispositivo.

## <a name="device-certification"></a>Certificação de dispositivos

A certificação do dispositivo é o processo de certificação de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para que ele possa ser adicionado ao catálogo de dispositivos [Certified for IoT](#certified-for-iot-device-catalog) e seu [modelo de capacidade](#device-capability-model) do dispositivo e [interfaces adicionadas](#interface) ao [repositório de modelos públicos.](#public-model-repository)

## <a name="device-developer"></a>Desenvolvedor de dispositivos

Um desenvolvedor de dispositivos usa um [modelo de capacidade do dispositivo,](#device-capability-model) [interfaces](#interface)e um [SDK de dispositivo Azure IoT](#azure-iot-device-sdk) para implementar código para ser executado em um [dispositivo IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelagem de dispositivos

Um [desenvolvedor de dispositivos](#device-developer) usa a Linguagem de Definição Dupla [Digital](#digital-twin-definition-language) para modelar os recursos de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). O modelo pode ser compartilhado usando um repositório de modelos. Um desenvolvedor de dispositivos pode gerar código de dispositivo esqueleto a partir do modelo. Um [desenvolvedor de soluções](#solution-developer) pode configurar uma solução IoT a partir do modelo.

## <a name="device-provisioning-service"></a>Serviço de provisionamento de dispositivos

[A Central de IoT do Azure](#azure-iot-central) usa o Serviço de Provisionamento de Dispositivos para gerenciar todo o registro e conexão do dispositivo. Para obter mais informações, consulte [a conectividade do dispositivo na Central De IoT Do Zure IoT](../iot-central/core/concepts-get-connected.md). Você também pode usar o Serviço de Provisionamento de Dispositivos para gerenciar o registro e a conexão do dispositivo com sua solução IoT baseada em IoT Hub. Para obter mais informações, consulte [dispositivos de provisionamento com o Serviço de Provisionamento de Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registro de dispositivos

Antes que um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) possa se conectar a uma solução IoT, ele deve ser registrado com a solução. [A Central de IoT do Azure](#azure-iot-central) usa o [Serviço de Provisionamento de Dispositivos](#device-provisioning-service) para gerenciar o registro do dispositivo. Em uma solução de IoT personalizada, você pode registrar dispositivos com seu hub de IoT no portal Azure ou programáticamente.

## <a name="device-first"></a>Primeiro dispositivo

[O Azure IoT Central](#azure-iot-central) suporta um cenário de registro e conexão em primeiro dispositivo. Neste cenário, um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) pode se conectar a um aplicativo IoT Central sem ser registrado com antecedência. O registro acontece automaticamente quando um dispositivo se conecta pela primeira vez ao aplicativo.

## <a name="digital-twin"></a>Gêmeo digital

Um gêmeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Um gêmeo digital é modelado usando a [Linguagem de Definição De Gêmeo Digital](#digital-twin-definition-language). Você pode usar os [SDKs do dispositivo Azure IoT](#azure-iot-device-sdk) para interagir com gêmeos digitais em tempo de execução. Por exemplo, você pode definir um valor de propriedade em um gêmeo digital em um dispositivo e o SDK comunica essa alteração para sua solução IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos de dupla mudança digital

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) está conectado a um hub de [IoT,](#azure-iot-hub)o hub pode usar sua capacidade de roteamento para enviar notificações de alterações duplas digitais. Por exemplo, sempre que um valor [de propriedade](#properties) muda em um dispositivo, o IoT Hub pode enviar uma notificação para um ponto final, como uma fila de Ônibus de Serviço.

## <a name="digital-twin-definition-language"></a>Linguagem de definição de gêmeos digitais

Uma linguagem para descrever modelos e interfaces para [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Use a [Linguagem de Definição Dupla Digital](https://aka.ms/DTDL) para descrever as capacidades de um gêmeo [digital](#digital-twin) e habilitar a plataforma DeI e soluções de IoT para aproveitar a semântica da entidade.

## <a name="digital-twin-route"></a>Rota dupla digital

Uma rota configurada em um [hub de IoT](#azure-iot-hub) para fornecer [eventos de mudança dupla digital](#digital-twin-change-events) para e ponto final, como uma fila de Ônibus de Serviço.

## <a name="interface"></a>Interface

Uma interface descreve recursos relacionados que são implementados por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou um gêmeo [digital](#digital-twin). Você pode reutilizar interfaces em diferentes [modelos de capacidade do dispositivo.](#device-capability-model)

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A linguagem de consulta do IoT Hub é usada para vários propósitos. Por exemplo, você pode usar o idioma para procurar [dispositivos registrados](#device-registration) no seu hub de IoT ou refinar o comportamento [de roteamento de gêmeos digitais.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo IoT Plug and Play é tipicamente um dispositivo de computação autônomo de pequena escala que coleta dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de capacidade do dispositivo](#device-capability-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação de agricultura inteligente. Você pode escrever uma solução IoT hospedada na nuvem para comandar, controlar e receber dados de dispositivos IoT Plug and Play. O [catálogo de dispositivos Azure Certified for IoT](#certified-for-iot-device-catalog) lista dispositivos IoT Plug and Play disponíveis. Cada dispositivo IoT Plug and Play no catálogo foi validado e possui um [modelo de capacidade do dispositivo.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Microsoft Partner Center

[O Microsoft Partner Center](https://docs.microsoft.com/partner-center/) é onde sua organização gerencia seu relacionamento de ponta a ponta com a Microsoft. Você precisa de uma conta do Microsoft Partner Center antes de certificar seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device) no portal [Azure Certified for IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Descoberta de modelos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a uma solução IoT, a solução pode descobrir as capacidades do dispositivo encontrando o modelo de capacidade do [dispositivo](#device-capability-model). Um dispositivo pode enviar seu modelo de capacidade para a solução, ou a solução pode encontrar um modelo de capacidade do dispositivo em um [repositório de modelos](#model-repository).

## <a name="model-repository"></a>Repositório de modelos

Um repositório de modelos armazena modelos e [interfaces](#interface)de capacidade do [dispositivo.](#device-capability-model) Há um único [repositório de modelos públicos.](#public-model-repository) As organizações podem criar seus próprios repositórios de modelos organizacionais.

## <a name="model-repository-rest-api"></a>API de repositório de modelos REST

Uma API para gerenciar e interagir com repositórios de modelos. Por exemplo, você pode usar a API para adicionar [modelos de capacidade do dispositivo](#device-capability-model) e procurar modelos de capacidade.

## <a name="properties"></a>Propriedades

Propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de um gêmeo digital. Você pode declarar as propriedades como somente leitura ou gravável. As propriedades somente leitura, como o número de série, são definidas por código em execução no [próprio dispositivo IoT Plug and Play.](#iot-plug-and-play-device)  As propriedades graváveis, como um limiar de alarme, são tipicamente definidas a partir da solução IoT baseada em nuvem.

## <a name="public-model-repository"></a>Repositório de modelos públicos

Existe um único repositório de modelos públicos que armazena [modelos](#device-capability-model) de capacidade de dispositivos e [interfaces](#interface) para [dispositivos certificados.](#device-certification) O repositório de modelos públicos também armazena definições [comuns de interface.](#common-interface)

## <a name="registration-id"></a>ID de registro

Um ID de registro identifica exclusivamente um dispositivo no [Serviço de Provisionamento de Dispositivos](#device-provisioning-service). Este ID não é o mesmo que o ID do dispositivo que é um identificador único para um dispositivo em um [hub ioT](#azure-iot-hub).

## <a name="scope-id"></a>ID de escopo

O escopo de ID do Escopo identifica exclusivamente uma instância [de serviço de provisionamento de dispositivos.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As Assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. A autenticação de assinatura de acesso compartilhado tem dois componentes: uma política de acesso compartilhado e uma assinatura de acesso compartilhada (muitas vezes chamada de token). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) usa uma assinatura de acesso compartilhado para autenticar com um hub [IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Desenvolvedor de soluções

Um desenvolvedor de soluções cria o back-end da solução. Um desenvolvedor de soluções normalmente trabalha com recursos do Azure, como [IoT Hub](#azure-iot-hub) e [repositórios de modelos,](#model-repository)ou funciona com [ioT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são tipicamente valores como leituras de sensores que são enviadas pelo [dispositivo IoT Plug and Play](#iot-plug-and-play-device) como um fluxo de dados.

## <a name="visual-studio-code"></a>Código visual studio

Visual Studio code é um editor de código moderno disponível para várias plataformas. As extensões, como as do pacote [Azure IoT Tools,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) permitem personalizar o editor para suportar uma ampla gama de cenários de desenvolvimento.
