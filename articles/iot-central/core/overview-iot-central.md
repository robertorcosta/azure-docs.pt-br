---
title: O que é Azure IoT Central | Microsoft Docs
description: Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções IoT, bem como ajuda a reduzir a carga e o custo das operações de gerenciamento e desenvolvimento da IoT. Este artigo fornece uma visão geral dos recursos do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 71d595ecb3a6900da3df79651ee2dddda594b9de
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589396"
---
# <a name="what-is-azure-iot-central"></a>O que é Azure IoT Central?

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

- A exportação de dados contínua não é compatível com o formato Avro.
- Atualmente, não há compatibilidade com GeoJSON.
- Atualmente, não há compatibilidade com peças de mapa.
- Os trabalhos não são compatíveis com tipos complexos.
- Não há compatibilidade com tipos de esquema de matriz.
- Somente o SDK do dispositivo C e os SDKs do serviço e do dispositivo Node.js são compatíveis.
- Só estão disponíveis nos locais Estados Unidos, Europa, Pacífico Asiático e Austrália.
- Os modelos de funcionalidade do dispositivo precisam ter todas as interfaces definidas embutidas no mesmo arquivo.
- O suporte para [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) está em versão prévia e só tem suporte em regiões selecionadas.

    > [!NOTE]
    > Entre em contato com o suporte técnico para obter acesso para criar um aplicativo de versão prévia com suporte para dispositivos IoT Plug and Play.

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

É possível implantar rapidamente um novo aplicativo do IoT Central e personalizá-lo de acordo com os requisitos específicos de seu navegador. Comece com um _modelo de aplicativo_ genérico ou um dos modelos de aplicativo voltados para o setor para [Varejo](../retail/overview-iot-central-retail-pnp.md), [Energia](../energy/overview-iot-central-energy.md), [Governo](../government/overview-iot-central-government.md) ou [Serviços de saúde](../healthcare/overview-iot-central-healthcare.md).

Como construtor de soluções, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é um blueprint que define as características e o comportamento de um tipo de dispositivo, como:

- A telemetria que ele envia.
- As propriedades de negócios que um operador pode modificar.
- As propriedades do dispositivo que são definidas por um dispositivo e são somente leitura no aplicativo.
- Propriedades, definidas por um operador, que determinam o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um _modelo de funcionalidade de dispositivo_ que descreve as funcionalidades que um dispositivo deve implementar, tais como a telemetria que ele envia e as propriedades que ele relata.
- Propriedades de nuvem que não são armazenadas no dispositivo.
- Personalizações, painéis e formulários que fazem parte do aplicativo do IoT Central.


### <a name="pricing"></a>Preços

Você pode criar um aplicativo do IoT Central usando uma avaliação gratuita de 7 dias ou usar um plano de preços Standard.

- Os aplicativos criados com o plano *gratuito* são gratuitos por sete dias e dão suporte a até cinco dispositivos. Você pode convertê-los para usar um plano de preços Standard a qualquer momento antes que eles expirem.
- Os aplicativos criados com um plano *Standard* são cobrados por dispositivo; você pode escolher o plano de preços **Standard 1** ou **Standard 2** com os dois primeiros dispositivos sendo gratuitos. Saiba mais sobre os planos de preços Gratuito e Standard na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Criar modelos de dispositivo

O [IoT Plug and Play (versão prévia)](../../iot-pnp/overview-iot-plug-and-play.md) permite ao IoT Central integrar dispositivos sem que você escreva nenhum código de dispositivo inserido. No núcleo do IoT Plug and Play (versão prévia) está um esquema de modelo de funcionalidade do dispositivo que descreve as funcionalidades do dispositivo. Em um aplicativo do IoT Central, os modelos de dispositivo usam esses modelos de funcionalidade do dispositivo do IoT Plug and Play (versão prévia).

Como construtor de solução, você tem várias opções para criar modelos de dispositivo:

- Importe um modelo de funcionalidade do dispositivo do [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/iotdevcat) e adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente o respectivo modelo de funcionalidade do dispositivo no código do dispositivo.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code e publique o modelo em um repositório. Implemente o código do dispositivo do modelo e conecte o dispositivo ao aplicativo do IoT Central. O IoT Central encontra o modelo de funcionalidade do dispositivo no repositório e cria um modelo de dispositivo simples para você.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de funcionalidade do dispositivo para o aplicativo do IoT Central e, em seguida, adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT central precisa.

Como um construtor de soluções, você pode usar o IoT Central a fim de gerar um código para testar dispositivos e validar seus modelos de dispositivo.

### <a name="customize-the-ui"></a>Personalizar a interface do usuário

Como construtor de soluções, você também pode personalizar a interface do usuário do aplicativo IoT Central para os operadores responsáveis pelo uso diário do aplicativo. As personalizações possíveis para um construtor de soluções incluem:

- A definição do layout de propriedades e configurações em um modelo do dispositivo.
- A configuração de painéis personalizados para ajudar os operadores a descobrirem insights e resolverem problemas mais rapidamente.
- A configuração da análise personalizada para explorar os dados de série temporal de seus dispositivos conectados.

## <a name="pricing"></a>Preços

Você pode criar um aplicativo do IoT Central usando uma avaliação gratuita de 7 dias ou usar um plano de preços Standard.

- Os aplicativos criados com o plano *gratuito* são gratuitos por sete dias e dão suporte a até cinco dispositivos. Você pode convertê-los para usar um plano de preços Standard a qualquer momento antes que eles expirem.
- Os aplicativos criados com o plano *Standard* são cobrados por dispositivo; você pode escolher o plano de preços **Standard 1** ou **Standard 2** com os dois primeiros dispositivos sendo gratuitos. Saiba mais sobre os [preços do IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Conecte seus dispositivos

O Azure IoT Central usa o [DPS (Serviço de Provisionamento de Dispositivos no Hub IoT do Azure)](../../iot-dps/about-iot-dps.md) para gerenciar todo o registro e a conexão do dispositivo.

O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Os dispositivos se conectem usando Assinaturas de Acesso Compartilhado.
- Os dispositivos se conectem usando certificados X.509 padrão do setor.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para saber mais, confira [Conectar-se ao Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Dispositivos do Azure IoT Edge

Além dos dispositivos criados usando os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks), você também pode conectar [dispositivos com Azure IoT Edge](../../iot-edge/about-iot-edge.md) a um aplicativo IoT Central. O Azure IoT Edge permite que você execute lógica de inteligência em nuvem e personalizada diretamente em dispositivos IoT gerenciados pelo IoT Central. Com o tempo de execução do IoT Edge você:

- Instala e atualiza as cargas de trabalho no dispositivo.
- Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
- Garante que os módulos do IoT Edge sempre estejam em execução.
- Relata a integridade do módulo à nuvem para monitoramento remoto.
- Gerencia a comunicação entre dispositivos folha downstream e um dispositivo do IoT Edge, entre módulos em um dispositivo do IoT Edge e entre um dispositivo do IoT Edge e a nuvem.

Para saber mais, confira [Dispositivos com Azure IoT Edge e IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Permanecer conectado

Aplicativos do IoT Central são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como operador, você pode usar o aplicativo IoT Central para gerenciar os dispositivos em sua solução de IoT Central. Operadores realizam tarefas como:

- Monitorar os dispositivos conectados ao aplicativo.
- Solucionar e corrigir problemas com dispositivos.
- Provisionar novos dispositivos.

Como construtor de soluções, você pode definir regras e ações personalizadas que operem em um fluxo de dados dos dispositivos conectados. Um operador pode habilitar ou desabilitar essas regras no nível do dispositivo para controlar e automatizar tarefas dentro do aplicativo.

Os administradores gerenciam o acesso ao seu aplicativo [com funções de usuário e permissões](howto-administer.md).

Com qualquer solução de IoT projetada para operar em escala, é importante ter uma abordagem estruturada para o gerenciamento de dispositivo. Não é suficiente apenas conectar seus dispositivos à nuvem; você precisa mantê-los conectados e íntegros. Um operador pode usar as seguintes funcionalidades do IoT Central para gerenciar os dispositivos ao longo do ciclo de vida do aplicativo:

### <a name="dashboards"></a>Painéis 

Os [painéis](./howto-set-up-template.md#generate-default-views) internos fornecem uma interface do usuário personalizável para monitorar a integridade e a telemetria do dispositivo. Comece com um painel predefinido em um [modelo de aplicativo](howto-use-app-templates.md) ou crie os próprios painéis adaptados às necessidades de seus operadores. Você pode compartilhar painéis com todos os usuários em seu aplicativo ou mantê-los particulares.

### <a name="rules-and-actions"></a>Regras e ações 

Crie [regras personalizadas](tutorial-create-telemetry-rules.md) com base no estado e na telemetria do dispositivo para identificar os dispositivos que precisam de atenção. Configure ações para notificar as pessoas certas e garanta que as medidas corretivas sejam executadas em tempo hábil.

### <a name="jobs"></a>Trabalhos 

Os [trabalhos](howto-run-a-job.md) permitem que você aplique atualizações individuais ou em massa aos dispositivos definindo propriedades ou chamando comandos. 

### <a name="user-roles-and-permissions"></a>Funções de usuário e permissões

[As funções e as permissões](howto-manage-users-roles.md) possibilitam que um administrador personalize a experiência de cada usuário. Um administrador usa a interface do usuário da Web para criar funções e atribuir permissões.

## <a name="transform-your-iot-data"></a>Transformar seus dados de IoT

Como uma plataforma de aplicativos, o IoT Central permite transformar seus dados de IoT em informações de negócios que geram resultados práticos. As [regras](./tutorial-create-telemetry-rules.md), a [exportação de dados](./howto-export-data.md) e a [API REST pública](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) são exemplos de como você pode integrar o IoT Central aos aplicativos de linha de negócios:

![Como o IoT Central pode transformar seus dados de IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorar a integridade e as operações do dispositivo usando regras

Quando seus dispositivos estão conectados e enviando dados, as regras podem identificar os dispositivos que estão tendo problemas ou enviando mensagens de erro, de modo que você possa corrigi-los com um tempo de inatividade mínimo. Crie regras no aplicativo do IoT Central para monitorar a telemetria de seus dispositivos e alertar um operador quando uma métrica ultrapassar um limite ou um dispositivo enviar uma mensagem específica. Envie ações e webhooks por email, de modo que as regras notifiquem as pessoas certas e os sistemas downstream certos.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Executar análise e processamento personalizados nos dados exportados

Você pode gerar informações de negócios, como determinar tendências de eficiência dos computadores ou prever o uso futuro de energia em um chão de fábrica, criando pipelines de análise personalizados para processar a telemetria de seus dispositivos e armazenar os resultados. Configure exportações de dados no aplicativo do IoT Central para exportar telemetria, alterações de propriedade do dispositivo e alterações de modelo do dispositivo para outros serviços, nos quais você poderá analisar, armazenar e visualizar os dados com as suas ferramentas preferidas.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Criar soluções de IoT e integrações personalizadas com as APIs REST

Crie soluções de IoT, como:

- Aplicativos do Complemento Móvel que possam configurar e controlar dispositivos remotamente.
- Integrações personalizadas que permitam que os aplicativos de linha de negócios existentes interajam com os dados e os dispositivos de IoT.
- Aplicativos de gerenciamento de dispositivo para modelagem de dispositivos, integração, gerenciamento e acesso a dados.

## <a name="quotas"></a>Cotas

Cada assinatura do Azure tem cota padrão que podem afetar o escopo de sua solução de IoT. No momento, o IoT Central limita o número de aplicativos que você pode implantar em uma assinatura a 10. Se precisar aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do IoT Central, estas são as próximas etapas sugeridas:

- Entenda as [tecnologias e os serviços disponíveis do Azure para criar soluções de IoT](../../iot-fundamentals/iot-services-and-technologies.md) disponíveis.
- Familiarizar-se com a [interface do usuário do Azure IoT Central](overview-iot-central-tour.md).
- Comece com a [criação de um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre o [IoT Plug and Play (versão prévia)](../../iot-pnp/overview-iot-plug-and-play.md).
- Saiba como [Conectar um dispositivo Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Saiba mais sobre [as tecnologias e os serviços de IoT do Azure](../../iot-fundamentals/iot-services-and-technologies.md).
