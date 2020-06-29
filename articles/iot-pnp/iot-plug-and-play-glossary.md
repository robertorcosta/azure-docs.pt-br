---
title: Glossário de termos – IoT Plug and Play Preview | Microsoft Docs
description: Conceitos – um glossário de termos comuns relacionados ao IoT Plug and Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767077"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossário de termos para o IoT Plug and Play Preview

Definições de termos comuns, conforme usado nos artigos do IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Portal do Azure Certified para IoT

É possível usar o site do [Portal do Azure Certified para IoT](https://aka.ms/ACFI) para:

- Concluir o [processo de certificação](#device-certification) para o dispositivo [IoT Plug and Play](#iot-plug-and-play-device).
- Localize os [modelos de funcionalidade do dispositivo](#device-capability-model).
- Publique um modelo de funcionalidade do dispositivo no [repositório de modelos públicos](#public-model-repository).

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure é uma ferramenta de linha de comando multiplataforma para gerenciar os recursos do Azure. A extensão do Azure IoT para a CLI do Azure é uma ferramenta de linha de comando para interagir com [dispositivos IoT Plug and Play](#iot-plug-and-play-device) e testá-los. É possível usar a extensão para:

- Conectar-se a um dispositivo IoT Plug and Play.
- Exibir a [telemetria](#telemetry) enviada pelo dispositivo.
- Trabalhar com [propriedades](#properties) do dispositivo.
- Chamar os comandos do [dispositivo](#commands).
- Gerencie os [repositórios de modelo](#model-repository), as [interfaces](#interface) e os [modelos de funcionalidade do dispositivo](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central é uma solução de software como serviço totalmente gerenciada que facilita a conexão, o monitoramento e o gerenciamento de seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). É possível usar [modelos de funcionalidade do dispositivo](#device-capability-model) para configurar automaticamente um aplicativo IoT Central para monitorar e gerenciar seus dispositivos.

## <a name="azure-iot-certification-service"></a>Serviço de certificação do Azure IoT

O serviço de certificação do Azure IoT executa um conjunto de testes de certificação quando você envia um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para a certificação por meio do [portal do Azure Certified para IoT](#azure-certified-for-iot-portal). Para adicionar um dispositivo ao [catálogo de dispositivos do Certified for IoT](#certified-for-iot-device-catalog), ele deverá ser certificado.

## <a name="azure-iot-tools-extension"></a>Extensão do Azure IoT Tools

O Azure IoT Tools é uma coleção de extensões no [Visual Studio Code](#visual-studio-code) que ajudam você a interagir com o Hub IoT e a desenvolver dispositivos IoT. Para o desenvolvimento de dispositivos IoT Plug and Play, ele ajuda você a:

- Criar [modelos de funcionalidade do dispositivo](#device-capability-model) e [interfaces](#interface).
- Publicar em [repositórios de modelos](#model-repository).
- Gerar o código esqueleto para implementar o aplicativo do dispositivo.

## <a name="azure-iot-explorer-tool"></a>Ferramenta do explorador do Azure IoT

O explorador do Azure IoT é uma ferramenta gráfica que você pode usar para interagir com seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device) e testá-los. Depois de instalar a ferramenta no computador local, você pode usá-la para:

- Exibir os dispositivos conectados ao seu [hub IoT](#azure-iot-hub).
- Conectar-se a um dispositivo IoT Plug and Play.
- Exibir a [telemetria](#telemetry) enviada pelo dispositivo.
- Trabalhar com [propriedades](#properties) do dispositivo.
- Chamar os comandos do [dispositivo](#commands).

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT é um serviço gerenciado e hospedado na nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos que ele gerencia. [Dispositivos IoT Plug and Play](#iot-plug-and-play-device) podem se conectar a um hub IoT. Uma solução de IoT usa um hub IoT para permitir que:

- Dispositivos enviem telemetria a uma solução baseada em nuvem.
- Uma solução baseada em nuvem gerencie dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>SDK do dispositivo IoT do Azure

Há SDKs de dispositivo para vários idiomas que você pode usar para criar aplicativos cliente de dispositivos IoT Plug and Play. Um dos requisitos para a [certificação do dispositivo](#device-certification) é que o código do cliente do dispositivo use um dos SDKs do dispositivo IoT do Azure.

## <a name="certified-for-iot-device-catalog"></a>Catálogo de dispositivos do Certified para IoT

O [catálogo de dispositivos do Certified para IoT](https://catalog.azureiotsolutions.com/) lista [dispositivos IoT Plug and Play](#iot-plug-and-play-device) que foram aprovados em testes de [certificação de dispositivo](#device-certification). Os [modelos de funcionalidade do dispositivo](#device-capability-model) para os dispositivos IoT Plug and Play no catálogo e publicados no repositório público de modelos.

## <a name="commands"></a>Comandos

Os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [gêmeo digital](#digital-twin). Por exemplo, um comando para reinicializar um dispositivo.

## <a name="common-interface"></a>Interface comum

Todos os [dispositivos IoT Plug and Play](#iot-plug-and-play-device) devem implementar algumas [interfaces](#interface) comuns. Por exemplo, a interface de informações do dispositivo define informações de hardware e do sistema operacional sobre o dispositivo. A [certificação do dispositivo](#device-certification) exige que seu dispositivo implemente várias interfaces comuns. É possível recuperar definições de interface comuns do repositório público de modelos.

## <a name="company-model-repository"></a>Repositório de modelos da empresa

Uma organização pode usar um [repositório de modelos](#model-repository) da empresa como um armazenamento privado para [modelos de funcionalidade do dispositivo](#device-capability-model) e [interfaces](#interface).

## <a name="connection-string"></a>Cadeia de conexão

Uma cadeia de conexão encapsula as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço das informações de segurança e de ponto de extremidade, mas os formatos da cadeia de conexão variam de acordo com os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- As cadeias de conexão do dispositivo permitem que os [dispositivos IoT Plug and Play](#iot-plug-and-play-device) se conectem aos pontos de extremidade voltados para o dispositivo em um hub IoT. O código do cliente em um dispositivo usa a cadeia de conexão para estabelecer uma conexão segura com um hub IoT.
- Cadeias de conexão do Hub IoT permitem que as soluções e ferramentas de back-end se conectem com segurança aos pontos de extremidade voltados para o dispositivo em um hub IoT. Essas soluções e ferramentas gerenciam o hub IoT e os dispositivos conectados a ele.
- As cadeias de conexão do repositório de modelos da empresa permitem que soluções e ferramentas de back-end se conectem seguramente a um [Repositório de modelos da empresa](#company-model-repository). Essas soluções e ferramentas consomem ou gerenciam os [modelos de funcionalidade do dispositivo](#device-capability-model) e [interfaces](#interface) no repositório.

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Um modelo de funcionalidade do dispositivo descreve um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) e define o conjunto de [interfaces](#interface) implementadas pelo dispositivo. Um modelo de funcionalidade do dispositivo geralmente corresponde a um dispositivo físico, produto ou SKU. Use a [Linguagem de Definição de Gêmeo Digital](#digital-twin-definition-language) para definir um modelo de funcionalidade do dispositivo.

## <a name="device-certification"></a>Certificado de dispositivo

A certificação de dispositivo é o processo de certificar um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para que ele possa ser adicionado ao [catálogo de dispositivos do Certified for IoT](#certified-for-iot-device-catalog) e ao [modelo de funcionalidade do dispositivo](#device-capability-model) e as [interfaces](#interface) adicionadas ao [repositório público de modelos](#public-model-repository).

## <a name="device-developer"></a>Desenvolvedor de dispositivos

Um desenvolvedor de dispositivos usa um [modelo de funcionalidade do dispositivo](#device-capability-model), [interfaces](#interface) e um [SDK do dispositivo IoT do Azure](#azure-iot-device-sdk) para implementar código a ser executado em um [dispositivo IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelagem de dispositivo

Um [desenvolvedor de dispositivos](#device-developer) usa a [Linguagem de Definição de Gêmeo Digital](#digital-twin-definition-language) para modelar as funcionalidades de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). O modelo pode ser compartilhado usando um repositório de modelo. Um desenvolvedor de dispositivos pode gerar o código de esqueleto do dispositivo do modelo. Um [desenvolvedor de solução](#solution-developer) pode configurar uma solução de IoT com base no modelo.

## <a name="device-provisioning-service"></a>Serviço de provisionamento de dispositivos

O [Azure IoT Central](#azure-iot-central) usa o Serviço de Provisionamento de Dispositivos para gerenciar todo o registro e a conexão do dispositivo. Para obter mais informações, confira [Conectividade de dados no Azure IoT Central](../iot-central/core/concepts-get-connected.md). Também é possível usar o Serviço de Provisionamento de Dispositivos para gerenciar o registro e a conexão do dispositivo para sua solução de IoT baseada em Hub IoT. Para obter mais informações, confira [Provisionar dispositivos com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registro de dispositivos

Para que um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) possa se conectar a uma solução de IoT, ele deve ser registrado com a solução. O [Azure IoT Central](#azure-iot-central) usa o [Serviço de Provisionamento de Dispositivos](#device-provisioning-service) para gerenciar o registro do dispositivo. Em uma solução de IoT personalizada, é possível registrar dispositivos com seu hub IoT no portal do Azure ou programaticamente.

## <a name="device-first"></a>Device-first

O [Azure IoT Central](#azure-iot-central) dá suporte a um cenário de registro e conexão device-first. Nesse cenário, um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) pode se conectar a um aplicativo IoT Central sem ser registrado com antecedência. O registro ocorre automaticamente quando um dispositivo se conecta pela primeira vez ao aplicativo.

## <a name="digital-twin"></a>Gêmeo digital

Um gêmeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Um gêmeo digital é modelado usando a [Linguagem de Definição de Gêmeo Digital](#digital-twin-definition-language). É possível usar os [SDKs do dispositivo IoT do Azure](#azure-iot-device-sdk) para interagir com gêmeos digitais em tempo de execução. Por exemplo, é possível definir um valor da propriedade em um gêmeo digital em um dispositivo e o SDK comunica essa alteração à sua solução de IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos de alteração de gêmeo digital

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) é conectado a um [hub IoT](#azure-iot-hub), o hub pode usar a funcionalidade de roteamento para enviar notificações de alterações de gêmeo digital. Por exemplo, sempre que um valor [property](#properties) é alterado em um dispositivo, o Hub IoT pode enviar uma notificação para um ponto de extremidade, como uma fila do Barramento de Serviço.

## <a name="digital-twin-definition-language"></a>Linguagem de Definição de Gêmeo Digital

Uma linguagem para descrever modelos e interfaces para [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Use a [Linguagem de Definição de Gêmeo Digital](https://aka.ms/DTDL) para descrever as funcionalidades de um [gêmeo digital](#digital-twin) e permitir que a plataforma IoT e as soluções de IoT utilizem a semântica da entidade.

## <a name="digital-twin-route"></a>Rota do gêmeo digital

Uma rota configurada em um [hub IoT](#azure-iot-hub) para fornecer [eventos de alteração de gêmeo digital](#digital-twin-change-events) a um ponto de extremidade como uma fila do Barramento de Serviço.

## <a name="interface"></a>Interface

Uma interface descreve as funcionalidades relacionadas implementadas por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou pelo [gêmeo digital](#digital-twin). É possível reutilizar interfaces em diferentes [modelos de funcionalidade do dispositivo](#device-capability-model).

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A linguagem de consulta do Hub IoT é usada para várias finalidades. Por exemplo, é possível usar a linguagem para pesquisar [dispositivos registrados](#device-registration) no Hub IoT ou refinar o comportamento do [roteamento de gêmeo digital](#digital-twin-route).

## <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo IoT Plug and Play normalmente é um dispositivo de computação autônomo de pequena escala que coleta dados ou controla outros dispositivos e que executa software e firmware que implementa um [modelo de funcionalidade do dispositivo](#device-capability-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação de agricultura inteligente. É possível escrever uma solução de IoT hospedada na nuvem para comandar, controlar e receber dados de dispositivos IoT Plug and Play. O [catálogo de dispositivos Azure Certified para IoT](#certified-for-iot-device-catalog) lista os dispositivos IoT Plug and Play disponíveis. Cada dispositivo IoT Plug and Play no catálogo foi validado e tem um [modelo de funcionalidade do dispositivo](#device-capability-model).

## <a name="microsoft-partner-center"></a>Microsoft Partner Center

É no [Microsoft Partner Center](https://docs.microsoft.com/partner-center/) que sua organização gerencia a relação de ponta a ponta dela com a Microsoft. É necessário ter uma conta do Microsoft Partner Center para certificar seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device) no [portal do Azure Certified for IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Descoberta de modelos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a uma solução de IoT, a solução pode descobrir as funcionalidades do dispositivo encontrando o [modelo de funcionalidade do dispositivo](#device-capability-model). Um dispositivo pode enviar o modelo de funcionalidade para a solução ou a solução pode encontrar um modelo de funcionalidade do dispositivo em um [repositório de modelos](#model-repository).

## <a name="model-repository"></a>Repositório de modelos

Um repositório de modelos armazena [modelos de funcionalidade do dispositivo](#device-capability-model) e [interfaces](#interface). Há um único [repositório público de modelos](#public-model-repository). As organizações podem criar os próprios repositórios de modelos organizacionais.

## <a name="model-repository-rest-api"></a>API REST do repositório de modelos

Uma API para gerenciar e interagir com repositórios de modelos. Por exemplo, é possível usar a API para adicionar [modelos de funcionalidade do dispositivo](#device-capability-model) e pesquisar modelos de funcionalidade.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de uma gêmeo digital. É possível declarar propriedades como somente leitura ou graváveis. Propriedades somente leitura, como número de série, são definidas pelo código em execução no próprio dispositivo [IoT Plug and Play](#iot-plug-and-play-device).  As propriedades graváveis, como um limite de alarme, normalmente são definidas com base na solução de IoT baseada em nuvem.

## <a name="public-model-repository"></a>Repositório público de modelos

Há um único repositório público de modelos que armazena [modelos de funcionalidade do dispositivo](#device-capability-model) e [interfaces](#interface) para [dispositivos certificados](#device-certification). O repositório público de modelo também armazena definições de [interface comum](#common-interface).

## <a name="registration-id"></a>ID de registro

A ID de registro identifica exclusivamente um dispositivo no [Serviço de Provisionamento de Dispositivos](#device-provisioning-service). Essa ID não é igual à ID do dispositivo que é um identificador exclusivo para um dispositivo em um [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID do Escopo

O escopo da ID do Escopo identifica exclusivamente uma instância do [Serviço de Provisionamento de Dispositivos](#device-provisioning-service).

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As Assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. A autenticação da assinatura de acesso compartilhado tem dois componentes: uma política de acesso compartilhado e uma assinatura de acesso compartilhado (normalmente chamada de token). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) usa uma assinatura de acesso compartilhado para se autenticar com um [hub IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Desenvolvedor de soluções

Um desenvolvedor de soluções cria o back-end da solução. Um desenvolvedor de soluções normalmente funciona com recursos do Azure, como [Hub IoT](#azure-iot-hub) e [repositórios de modelos](#model-repository) ou funciona com o [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor enviadas pelo [dispositivo IoT Plug and Play](#iot-plug-and-play-device) como um fluxo de dados.

## <a name="visual-studio-code"></a>Visual Studio Code

O Visual Studio Code é um editor de código moderno disponível para várias plataformas. As extensões, como as do pacote do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), permitem que você personalize o editor para dar suporte a uma ampla gama de cenários de desenvolvimento.
