---
title: Conceitos de arquitetura no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à arquitetura do Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271636"
---
# <a name="azure-iot-central-architecture"></a>Arquitetura do Azure IoT Central



Este artigo fornece uma visão geral da arquitetura do Microsoft Azure IoT Central.

![Arquitetura de nível superior](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Dispositivos

Os dispositivos trocam dados com o aplicativo Azure IoT Central. Um dispositivo pode:

- Enviar medidas, como telemetria.
- Sincronizar as configurações com o aplicativo.

No Azure IoT Central, os dados que um dispositivo pode trocar com o aplicativo são especificados em um modelo de dispositivo. Para obter mais informações sobre modelos de dispositivos, consulte [Gerenciamento de metadados](#metadata-management).

Para saber mais sobre como os dispositivos conectam ao aplicativo do Azure IoT Central, consulte [Conectividade de Dispositivo](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Dispositivos do Azure IoT Edge

Além dos dispositivos criados usando os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks), você também pode conectar [dispositivos com Azure IoT Edge](../../iot-edge/about-iot-edge.md) a um aplicativo IoT Central. O IoT Edge permite que você execute inteligência em nuvem e lógica personalizada diretamente em dispositivos IoT gerenciados pela IoT Central. Com o tempo de execução do IoT Edge você:

- Instala e atualiza as cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança IoT Edge no dispositivo.
- Garante que os módulos do IoT Edge sempre estejam em execução.
- Relata a integridade do módulo à nuvem para monitoramento remoto.
- Gerencia a comunicação entre dispositivos folha downstream e um dispositivo do IoT Edge, entre módulos em um dispositivo do IoT Edge e entre um dispositivo do IoT Edge e a nuvem.

![Azure IoT Central com Azure IoT Edge](./media/concepts-architecture/iotedge.png)

A IoT Central habilita os seguintes recursos para dispositivos IoT Edge:

- Modelos de dispositivos para descrever os recursos de um dispositivo IoT Edge, tais como:
  - Recurso de upload manifesto de implantação, que ajuda você a gerenciar um manifesto para uma frota de dispositivos.
  - Módulos que são executados no dispositivo IoT Edge.
  - A telemetria que cada módulo envia.
  - As propriedades que cada módulo relata.
  - Os comandos a que cada módulo responde.
  - As relações entre um modelo de capacidade de dispositivo gateway IoT Edge e o modelo de capacidade do dispositivo a jusante.
  - Propriedades na nuvem que não estão armazenadas no dispositivo IoT Edge.
  - Personalizações, painéis e formulários que fazem parte do aplicativo do IoT Central.

  Para obter mais informações, consulte os [dispositivos Connect Azure IoT Edge em um artigo de aplicativo Azure IoT Central.](./concepts-iot-edge.md)

- A capacidade de provisionar dispositivos IoT Edge em escala usando o serviço de provisionamento de dispositivos Azure IoT
- Regras e ações.
- Painéis e análises personalizados.
- Exportação contínua de dados de telemetria de dispositivos IoT Edge.

### <a name="iot-edge-device-types"></a>Tipos de dispositivos IoT Edge

A IoT Central classifica os tipos de dispositivos IoT Edge da seguinte forma:

- Dispositivos folha. Um dispositivo IoT Edge pode ter dispositivos de folhas a jusante, mas esses dispositivos não são provisionados na IoT Central.
- Dispositivos gateway com dispositivos a jusante. O dispositivo de gateway e os dispositivos downstream são provisionados no IoT Central

![IoT Central com Visão Geral da Borda ioT](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Padrões de borda IoT

A IoT Central suporta os seguintes padrões de dispositivos IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>Borda IoT como dispositivo de folha

![Borda IoT como dispositivo de folha](./media/concepts-architecture/edgeasleafdevice.png)

O dispositivo IoT Edge é provisionado na IoT Central e quaisquer dispositivos a jusante e sua telemetria é representada como proveniente do dispositivo IoT Edge. Os dispositivos a jusante conectados ao dispositivo IoT Edge não são provisionados na IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Dispositivo gateway IoT Edge conectado a dispositivos a jusante com identidade

![Borda IoT com identidade de dispositivo a jusante](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

O dispositivo IoT Edge é provisionado na IoT Central, juntamente com os dispositivos a jusante conectados ao dispositivo IoT Edge. O suporte em tempo de execução para o provisionamento de dispositivos a jusante através do gateway não é suportado no momento.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Dispositivo gateway IoT Edge conectado a dispositivos a jusante com identidade fornecida pelo gateway IoT Edge

![IoT Edge com dispositivo a jusante sem identidade](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

O dispositivo IoT Edge é provisionado na IoT Central, juntamente com os dispositivos a jusante conectados ao dispositivo IoT Edge. O suporte em tempo de execução do gateway que fornece identidade para dispositivos a jusante e o provisionamento de dispositivos a jusante não é suportado no momento. Se você trouxer seu próprio módulo de tradução de identidade, a IoT Central pode suportar esse padrão.

## <a name="cloud-gateway"></a>Gateway de nuvem

O Azure IoT Central usa o Hub IoT como um gateway de nuvem que permite a conectividade de dispositivo. O Hub IoT permite:

- Ingestão de dados em escala na nuvem.
- Gerenciamento de dispositivo.
- Conectividade de dispositivo segura.

Para saber mais sobre Hub IoT, consulte [Hub IoT](https://docs.microsoft.com/azure/iot-hub/).

Para saber mais sobre a conectividade de dispositivos no Azure IoT Central, consulte [Conectividade de dispositivo](concepts-get-connected.md).

## <a name="data-stores"></a>Armazenamentos de dados

O Azure IoT Central armazena dados de aplicativos na nuvem. Os dados armazenados de aplicativo armazenados incluem:

- Modelos de dispositivos.
- Identidades de dispositivos.
- Metadados do dispositivo.
- Dados de usuário e função.

O Azure IoT Central usa um armazenamento de série temporal para os dados de medida enviados dos dispositivos. Os dados da série temporal dos dispositivos usados pelo serviço analítico.

## <a name="analytics"></a>Análise

O serviço analítico é responsável por gerar os dados de relatório personalizados que o aplicativo exibe. Um operador pode [personalizar a análise](howto-create-analytics.md) exibida no aplicativo. O serviço analítico é compilado no [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) e processa os dados de medida enviados a partir dos dispositivos.

## <a name="rules-and-actions"></a>Regras e ações

[Regras e ações](tutorial-create-telemetry-rules.md) trabalham em conjunto para automatizar tarefas no aplicativo. Um construtor pode definir regras baseadas na telemetria de dispositivo, como a temperatura que excede um limite definido. O Azure IoT Central usa um processador de fluxo para determinar quando as condições da regra são atendidas. Quando uma condição de regra é atendida, ela dispara uma ação definida pelo construtor. Por exemplo, uma ação pode enviar um email para notificar um engenheiro de que a temperatura em um dispositivo está muito alta.

## <a name="metadata-management"></a>Gerenciamento de metadados

Em um aplicativo do Azure IoT Central, os modelos de dispositivo definem o comportamento e a capacidade dos tipos de dispositivo. Por exemplo, um modelo de dispositivo de refrigerador especifica a telemetria que um refrigerador envia ao aplicativo.

![Arquitetura de modelo](media/concepts-architecture/template-architecture.png)

Em um modelo de dispositivo de aplicativo IoT Central contém:

- **Os modelos** de capacidade do dispositivo especificam os recursos de um dispositivo, como a telemetria que ele envia, as propriedades que definem o estado do dispositivo e os comandos aos quais o dispositivo responde. Os recursos do dispositivo são organizados em uma ou mais interfaces. Para obter mais informações sobre os modelos de capacidade do dispositivo, consulte a documentação [ioT Plug and Play (preview).](../../iot-pnp/overview-iot-plug-and-play.md)
- **As propriedades** na nuvem especificam as propriedades que a IoT Central armazena para um dispositivo. Essas propriedades são armazenadas apenas na IoT Central e nunca são enviadas para um dispositivo.
- **As exibições** especificam os dashboards e os formulários que o construtor cria para permitir que o operador monitore e gerencie os dispositivos.
- **As personalizações** permitem que o construtor sobreponha algumas das definições no modelo de capacidade do dispositivo para torná-las mais relevantes para o aplicativo IoT Central.

Um aplicativo pode ter um ou mais dispositivos simulados e reais com base em cada modelo de dispositivo.

## <a name="data-export"></a>Exportação de dados

Em um aplicativo Azure IoT Central, você pode [exportar continuamente seus dados](howto-export-data.md) para seus próprios hubs de eventos azure e instâncias de ônibus de serviço do Azure. Você também pode exportar periodicamente seus dados para sua conta de armazenamento Azure Blob. A IoT Central pode exportar medidas, dispositivos e modelos de dispositivos.

## <a name="batch-device-updates"></a>Atualizações de dispositivos em lote

Em um aplicativo Azure IoT Central, você pode [criar e executar trabalhos](howto-run-a-job.md) para gerenciar dispositivos conectados. Esses trabalhos permitem que você faça atualizações em massa em propriedades ou configurações do dispositivo ou execute comandos. Por exemplo, você pode criar um trabalho para aumentar a velocidade do ventilador para várias máquinas de venda automática refrigeradas.

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)

Um [administrador pode definir regras](howto-manage-users-roles.md) de acesso para um aplicativo Azure IoT Central usando uma das funções predefinidas ou criando uma função personalizada. As funções determinam quais áreas do aplicativo um usuário tem acesso e quais ações podem realizar.

## <a name="security"></a>Segurança

Os recursos de segurança do Azure IoT Central incluem:

- Os dados são criptografados em trânsito e em repouso.
- A autenticação é fornecida pelo Azure Active Directory ou pela Conta Microsoft. A autenticação de dois fatores tem suporte.
- Isolamento de locatário completo.
- Segurança em nível de dispositivo.

## <a name="ui-shell"></a>Shell de interface do usuário

O shell de interface do usuário é um aplicativo dinâmico e moderno com base no navegador HTML5.
Um administrador pode personalizar a interface do usuário do aplicativo aplicando temas personalizados e modificando os links de ajuda para apontar para seus próprios recursos de ajuda personalizados. Para saber mais sobre a personalização da UI, consulte Personalizar o artigo [da IoT Central UI do Azure.](howto-customize-ui.md)

Um operador pode criar painéis de aplicativos personalizados. Você pode ter vários dashboards que exibem dados diferentes e alternam entre eles.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a arquitetura do Azure IoT Central, o próximo passo sugerido é aprender sobre [conectividade de dispositivos](concepts-get-connected.md) no Azure IoT Central.
