---
title: O que é Azure IoT Central | Microsoft Docs
description: Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções IoT, bem como ajuda a reduzir a carga e o custo das operações de gerenciamento e desenvolvimento da IoT. Este artigo fornece uma visão geral dos recursos do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479702"
---
# <a name="what-is-azure-iot-central-preview-features"></a>O que é o Azure IoT Central (versão prévia dos recursos)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> As funcionalidades do [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) no Azure IoT Central estão atualmente em versão prévia pública. Não use um [modelo de aplicativo](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) do IoT Central habilitado para IoT Plug and Play em cargas de trabalho de produção. Para ambientes de produção, use um aplicativo do IoT Central criado com base em um [modelo de aplicativo](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) atual e com disponibilidade geral.

O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo de desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar o tempo, o dinheiro e a energia na transformação de seus negócios com os dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e em evolução contínua.

A interface do usuário da Web permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, permite que você atue com base em insights do dispositivo estendendo a inteligência da IoT em aplicativos de linha de negócios.

Este artigo descreve os seguintes tópicos do Azure IoT Central:

- As personas típicas associadas a um projeto.
- Como criar seu aplicativo.
- Como conectar seus dispositivos ao seu aplicativo
- Como gerenciar seu aplicativo.
- Funcionalidades do Azure IoT Edge no IoT Central.
- Como conectar seus dispositivos capacitados com o runtime do Azure IoT Edge ao seu aplicativo.

## <a name="known-issues"></a>Problemas conhecidos

> [!Note]
> Esses problemas conhecidos só se aplicam ao aplicativo do IoT Central em versão prévia.

- As regras não dão suporte a todas as ações (somente email).
- Para tipos complexos – regras, análise e grupos de dispositivos não são compatíveis.
- A exportação de dados contínua não é compatível com o formato Avro.
- Os dispositivos simulados não são compatíveis com todos os tipos complexos.
- Atualmente, não há compatibilidade com GeoJSON.
- Atualmente, não há compatibilidade com peças de mapa.
- Os trabalhos não são compatíveis com tipos complexos.
- Não há compatibilidade com tipos de esquema de matriz.
- Não há compatibilidade com exportação de modelo de aplicativo nem com cópia de aplicativo.
- Somente o SDK do dispositivo C e os SDKs do serviço e do dispositivo Node.js são compatíveis.
- Está disponível apenas em locais dos Estados Unidos e da Europa.
- Os modelos de funcionalidade do dispositivo precisam ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="personas"></a>Personas

A documentação do Azure IoT Central faz referência a quatro personas que interagem com um aplicativo do Azure IoT Central:

- Um _construtor de soluções_ é responsável pela definição dos tipos de dispositivos que se conectam ao aplicativo e pela personalização do aplicativo para o operador.
- Um _operador_ gerencia os dispositivos conectados ao aplicativo.
- Um _administrador_ é responsável por tarefas administrativas, como gerenciar [funções de usuários e permissões](howto-administer.md) dentro do aplicativo.
- Um _desenvolvedor de dispositivo_ cria o código que é executado em um dispositivo ou no módulo do IoT Edge conectado ao seu aplicativo.

## <a name="create-your-iot-central-application"></a>Criar seu aplicativo do IoT Central

Como construtor se solução, você usa o IoT Central para criar uma solução de IoT personalizada e hospedada na nuvem para sua organização. Uma solução de IoT personalizada normalmente consiste em:

- Um aplicativo baseado em nuvem que recebe telemetria de seus dispositivos e permite o gerenciamento dos dispositivos.
- Vários dispositivos executando o código personalizado conectado ao seu aplicativo baseado em nuvem.

É possível implantar rapidamente um novo aplicativo do IoT Central e personalizá-lo de acordo com os requisitos específicos de seu navegador. Como construtor de soluções, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é um blueprint que define as características e o comportamento de um tipo de dispositivo, como:

- A telemetria que ele envia.
- As propriedades de negócios que um operador pode modificar.
- As propriedades do dispositivo que são definidas por um dispositivo e são somente leitura no aplicativo.
- Propriedades, definidas por um operador, que determinam o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um _modelo de funcionalidade de dispositivo_ que descreve as funcionalidades que um dispositivo deve implementar, tais como a telemetria que ele envia e as propriedades que ele relata.
- Propriedades de nuvem que não são armazenadas no dispositivo.
- Personalizações, painéis e formulários que fazem parte do aplicativo do IoT Central.

### <a name="create-device-templates"></a>Criar modelos de dispositivo

O [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) permite que o IoT Central integre dispositivos sem que você escreva nenhum código de dispositivo inserido. Há um esquema de modelo de funcionalidade do dispositivo no núcleo do IoT Plug and Play que descreve os recursos do dispositivo. Em um aplicativo do IoT Central em versão prévia, os modelos de dispositivo usam esses modelos de funcionalidade do dispositivo de IoT Plug and Play.

Como construtor de solução, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente o respectivo modelo de funcionalidade do dispositivo no código do dispositivo.
- Importe um modelo de funcionalidade do dispositivo do [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/iotdevcat) e adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo e conecte o dispositivo ao aplicativo do IoT Central. O IoT Central localiza o modelo de funcionalidade do dispositivo em um repositório e cria um modelo de dispositivo simples para você.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de funcionalidade do dispositivo para o aplicativo do IoT Central e, em seguida, adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT central precisa.

Como um construtor de soluções, você pode usar o IoT Central a fim de gerar um código para testar dispositivos e validar seus modelos de dispositivo.

### <a name="customize-the-ui"></a>Personalizar a interface do usuário

Como construtor de soluções, você também pode personalizar a interface do usuário do aplicativo IoT Central para os operadores responsáveis pelo uso diário do aplicativo. As personalizações possíveis para um construtor de soluções incluem:

- A definição do layout de propriedades e configurações em um modelo do dispositivo.
- A configuração de painéis personalizados para ajudar os operadores a descobrirem insights e resolverem problemas mais rapidamente.
- A configuração da análise personalizada para explorar os dados de série temporal de seus dispositivos conectados.

## <a name="connect-your-devices"></a>Conecte seus dispositivos

Depois que o construtor define os tipos de dispositivos que podem se conectar ao aplicativo, um desenvolvedor de dispositivo cria o código para executar os dispositivos. Como um desenvolvedor de dispositivo, você usa os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) de código aberto da Microsoft para criar seu código de dispositivo. Esses SDKs têm amplo suporte a linguagem, plataforma e protocolo para atender às necessidades de conexão de seus dispositivos ao aplicativo Azure IoT Central. Os SDKs ajudam a implementar os seguintes recursos do dispositivo:

- Criar uma conexão segura.
- Enviar telemetria.
- Relatar o status.
- Receber atualizações periódicas.

Para obter mais informações, consulte a postagem de blog [Os benefícios de usar os SDKs do Azure IoT e as armadilhas a serem evitadas caso não os use](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Dispositivos do Azure IoT Edge

Além dos dispositivos criados usando os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks), você também pode conectar [dispositivos com Azure IoT Edge](../../iot-edge/about-iot-edge.md) a um aplicativo IoT Central. O Azure IoT Edge permite que você execute lógica de inteligência em nuvem e personalizada diretamente em dispositivos IoT gerenciados pelo IoT Central. Com o tempo de execução do IoT Edge você:

- Instala e atualiza as cargas de trabalho no dispositivo.
- Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
- Garante que os módulos do IoT Edge sempre estejam em execução.
- Relata a integridade do módulo à nuvem para monitoramento remoto.
- Gerencia a comunicação entre dispositivos folha downstream e um dispositivo do IoT Edge, entre módulos em um dispositivo do IoT Edge e entre um dispositivo do IoT Edge e a nuvem.

Para saber mais, confira [Dispositivos com Azure IoT Edge e IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Gerenciar seu aplicativo

Aplicativos do IoT Central são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como operador, você pode usar o aplicativo IoT Central para gerenciar os dispositivos em sua solução de IoT Central. Operadores realizam tarefas como:

- Monitorar os dispositivos conectados ao aplicativo.
- Solucionar e corrigir problemas com dispositivos.
- Provisionar novos dispositivos.

Como construtor de soluções, você pode definir regras e ações personalizadas que operem em um fluxo de dados dos dispositivos conectados. Um operador pode habilitar ou desabilitar essas regras no nível do dispositivo para controlar e automatizar tarefas dentro do aplicativo.

Os administradores gerenciam o acesso ao seu aplicativo [com funções de usuário e permissões](howto-administer.md).

## <a name="quotas"></a>Cotas

Cada assinatura do Azure tem cota padrão que podem afetar o escopo de sua solução de IoT. No momento, o IoT Central limita o número de aplicativos que você pode implantar em uma assinatura a 10. Se precisar aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do IoT Central, estas são as próximas etapas sugeridas:

- Entender as diferenças entre o [IoT Central e aceleradores de solução do Azure IoT](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Familiarizar-se com a [interface do usuário do Azure IoT Central](overview-iot-central-tour.md).
- Comece com a [criação de um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre o [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Saiba como [Criar modelo de dispositivo do Azure IoT Edge](./tutorial-define-edge-device-type.md)
