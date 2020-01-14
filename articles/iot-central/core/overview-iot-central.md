---
title: O que é Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral dos recursos do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: a1aa2f12f62a95ac14750c821079df2bac46e8ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434965"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>O que é Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo associado ao desenvolvimento, ao gerenciamento e à manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar seu tempo, dinheiro e energia na transformação de seus negócios com os dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em evolução.

A interface fácil de usar facilita o monitoramento das condições do dispositivo, a criação de regras e o gerenciamento de milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, permite que você atue com base em insights do dispositivo estendendo a inteligência da IoT em aplicativos de linha de negócios.

Este artigo descreve os seguintes tópicos do Azure IoT Central:

- As personas típicas associadas a um projeto.
- Como criar seu aplicativo.
- Como conectar seus dispositivos ao seu aplicativo
- Como gerenciar seu aplicativo.

## <a name="personas"></a>Personas

A documentação do Azure IoT Central refere-se a quatro personas que interagem com um aplicativo do Azure IoT Central:

- Um _construtor_ é responsável pela definição dos tipos de dispositivos que se conectam ao aplicativo e pela personalização do aplicativo para o operador.
- Um _operador_ gerencia os dispositivos conectados ao aplicativo.
- Um _administrador_ é responsável por tarefas como gerenciar [funções de usuários e permissões](howto-administer.md) dentro do aplicativo.
- Um _desenvolvedor de dispositivo_ cria o código que é executado em um dispositivo conectado ao seu aplicativo.

## <a name="create-your-azure-iot-central-application"></a>Criar seu aplicativo do Azure IoT Central

Como um construtor, você usa o Azure IoT Central para criar uma solução de IoT personalizada e hospedada na nuvem para sua organização. Uma solução de IoT personalizada normalmente consiste em:

- Um aplicativo baseado em nuvem que recebe telemetria de seus dispositivos e permite o gerenciamento dos dispositivos.
- Vários dispositivos executando o código personalizado conectado ao seu aplicativo baseado em nuvem.

É possível implantar rapidamente um novo aplicativo do Azure IoT Central e personalizá-lo no navegador de acordo com seus requisitos específicos. Como um construtor, você usa as ferramentas baseadas na web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é um blueprint que define as características e o comportamento de um tipo de dispositivo, como:

- A telemetria que ele envia.
- As propriedades de negócios que um operador pode modificar.
- As propriedades do dispositivo que são definidas por um dispositivo e são somente leitura no aplicativo.
- Os limites aos quais o aplicativo responde.
- Configurações que determinam o comportamento do dispositivo.

É possível testar imediatamente seus modelos de dispositivo e o aplicativo com dados simulados que o Azure IoT Central gera para você.

Como um construtor, você também pode personalizar o aplicativo do Azure IoT Central da interface do usuário para os operadores responsáveis pelo uso diário do aplicativo. As personalizações que um construtor pode fazer incluem:

- A definição do layout de propriedades e configurações em um modelo do dispositivo.
- A configuração de painéis personalizados para ajudar os operadores a descobrirem insights e resolverem problemas mais rapidamente.
- A configuração da análise personalizada para explorar os dados de série temporal de seus dispositivos conectados.

## <a name="connect-your-devices"></a>Conecte seus dispositivos

Depois que o construtor define os tipos de dispositivos que podem se conectar ao aplicativo, um desenvolvedor de dispositivo cria o código para executar os dispositivos. Como um desenvolvedor de dispositivo, você usa os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) de código aberto da Microsoft para criar seu código de dispositivo. Esses SDKs têm amplo suporte a linguagem, plataforma e protocolo para atender às suas necessidades de conexão de dispositivos ao seu aplicativo do Azure IoT Central. Os SDKs ajudam a implementar os seguintes recursos do dispositivo:

- Criar uma conexão segura.
- Enviar telemetria.
- Relatar o status.
- Receber atualizações periódicas.

Para obter mais informações, consulte a postagem de blog [Os benefícios de usar os SDKs do Azure IoT e as armadilhas a serem evitadas caso não os use](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gerenciar seu aplicativo

Aplicativos do Azure IoT Central são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como um operador, você pode usar o aplicativo do Azure IoT Central para gerenciar os dispositivos em sua solução do Azure IoT Central. Operadores realizam tarefas como:

- Monitorar os dispositivos conectados ao aplicativo.
- Solucionar e corrigir problemas com dispositivos.
- Provisionar novos dispositivos.

Como desenvolvedor, você pode definir regras e ações personalizadas que operem em um fluxo de dados dos dispositivos conectados. Um operador pode habilitar ou desabilitar essas regras no nível do dispositivo para controlar e automatizar tarefas dentro do aplicativo.

Os administradores gerenciam o acesso ao seu aplicativo [com funções de usuário e permissões](howto-administer.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Azure IoT Central, aqui estão as próximas etapas sugeridas:

- Entenda as [tecnologias e os serviços disponíveis do Azure para criar soluções de IoT](../../iot-fundamentals/iot-services-and-technologies.md) disponíveis.
- Familiarizar-se com a [interface do usuário do Azure IoT Central](overview-iot-central-tour.md).
- Comece com a [criação de um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).
- Siga uma sequência de tutoriais que mostram:
  - [Como um construtor, como criar um modelo de dispositivo](tutorial-define-device-type.md)
  - [Como um construtor, como adicionar regras para automatizar a sua solução](tutorial-configure-rules.md)
  - [Como um construtor, como personalizar o aplicativo para seus operadores](tutorial-customize-operator.md)
  - [Como um operador, como monitorar seus dispositivos](tutorial-monitor-devices.md)
  - [Como um operador, como adicionar um dispositivo real à sua solução](tutorial-add-device.md)
  - [Como um desenvolvedor do dispositivo, como criar código para seus dispositivos](tutorial-add-device.md#prepare-the-client-code)
