---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c4df07174a5d8826acd7682fa3035fcfd201c5c9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953090"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado com as seguintes características:

- Medição de telemetria de temperatura
- Medida de localização
- Medição de evento de erro de motor da ventoinha
- Medição do estado operacional da ventoinha
- Ajuste de velocidade da ventoinha
- Regras que enviam alertas
- Painel que oferece uma visão geral do dispositivo

A partir desse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventoinhas reais com nomes como **ventoinha-1** e **ventoinha-2**. Todos esses fãs possuem medidas, configurações, propriedades, regras e um painel que os usuários do seu aplicativo podem monitorar e gerenciar.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Device Explorer** a partir de modelos de dispositivos existentes.

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

1. Navegue para a página **Modelos de Dispositivo**.

2. Para criar um modelo, comece selecionando **+ novo**.

3. Para começar rapidamente, escolha entre os modelos predefinidos existentes. Caso contrário, selecione **personalizado**, insira um nome e clique em **criar** para criar seu próprio modelo do zero.

   ![Biblioteca de modelos de dispositivo](./media/howto-set-up-template/newtemplate.png)

4. Ao criar um modelo personalizado, você verá a página de **detalhes do dispositivo** para o novo modelo de dispositivo. O IoT Central cria automaticamente um dispositivo simulado quando você cria um modelo de dispositivo. Um dispositivo simulado permite testar o comportamento do aplicativo antes de você conectar um dispositivo real.

As seções a seguir descrevem cada uma das guias da página **Modelo de Dispositivo**.

## <a name="measurements"></a>Medidas

Medidas são os dados que vêm do seu dispositivo. É possível adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do dispositivo.

- **Telemetria** são os pontos de dados numéricos que o seu dispositivo coleta ao longo do tempo. Eles são representados como um fluxo contínuo. Um exemplo é a temperatura.
- **As medições do evento** são dados point-in-time que representam algo significativo no dispositivo. Um nível de severidade representa a importância de um evento. Um exemplo é um erro no motor do ventilador.
- Medidas de **Estado** representam o estado do dispositivo ou os respectivos componentes durante um período de tempo. Por exemplo, um modo de ventilador pode ser definido como tendo **Operating** e **Stopped** como os dois estados possíveis.
- As medidas de **localização** são as coordenadas de longitude e latitude do dispositivo durante um período de tempo no. Por exemplo, um ventilador pode ser movido de um local para outro.

### <a name="create-a-telemetry-measurement"></a>Criar uma medição de telemetria

Para adicionar uma nova medição de telemetria, selecione **+ nova medida**, escolha **telemetria** como o tipo de medida e insira os detalhes no formulário.

> [!NOTE]
> Os nomes de campo no modelo de dispositivo precisarão corresponder aos nomes de propriedade no código do dispositivo correspondente para que a medida de telemetria seja exibida no aplicativo quando um dispositivo real estiver conectado. Faça o mesmo ao definir configurações, propriedades do dispositivo e comandos enquanto você continua a definir o modelo de dispositivo nas seções a seguir.

Por exemplo, é possível adicionar uma nova medida de telemetria de temperatura:

| Nome de exibição        | Nome do campo    |  Unidades    | Min   |Máx.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formulário "Criar Telemetria" com detalhes para medição de temperatura](./media/howto-set-up-template/measurementsform.png)

Depois de selecionar **Salvar**, a medição **Temperatura** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de temperatura do dispositivo simulado.

Ao exibir a telemetria, você pode escolher entre as seguintes opções de agregação: média, mínimo, máximo, soma e contagem. A **média** é selecionada como a agregação padrão no gráfico.

> [!NOTE]
> O tipo de dados da medida de telemetria é número de ponto flutuante.

### <a name="create-an-event-measurement"></a>Crie uma medição de evento

Para adicionar uma nova medição de evento, selecione **+ nova medição** e selecione **evento** como o tipo de medida. Insira os detalhes no formulário **Criar Evento**.

Forneça os detalhes **Nome para exibição**, **Nome do campo** e **Gravidade** para o evento. Você pode escolher entre os três níveis de gravidade disponíveis: **Erro**, **Aviso** e **Informação**.

Por exemplo, você pode adicionar um novo evento **Fan Motor Error**.

| Nome de exibição        | Nome do campo    |  Gravidade padrão |
| --------------------| ------------- |-----------|
| Erro do motor do ventilador     | fanmotorerror |  Erro    |

![Formulário "Criar Evento" com detalhes para um evento de motor de ventilador](./media/howto-set-up-template/eventmeasurementsform.png)

Depois de selecionar **Gravar**, a medição **Erro do Motor do Ventilador** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de evento do dispositivo simulado.

Para exibir mais detalhes sobre um evento, selecione o ícone de evento no gráfico:

![Detalhes para o evento "Fan Motor Error"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="create-a-state-measurement"></a>Crie uma medida de estado

Para adicionar uma nova medida de estado, selecione o botão de **medida + nova** e selecione **estado** como o tipo de medida. Insira os detalhes no formulário **Criar Estado**.

Forneça os detalhes para **Nome de exibição**, **Nome do campo** e **Valores** do estado. Cada valor também pode ter um nome de exibição que será usado quando o valor aparecer em gráficos e tabelas.

Por exemplo, você pode adicionar um novo **estado do Modo de fãs** que tenha dois valores possíveis que o dispositivo pode enviar, **Em funcionamento** e **Parado**.

| Nome de exibição | Nome do campo    |  Valor 1   | Nome de exibição | Valor 2    |Nome de exibição  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo ventilação     | fanmode       |  1         | Operacional    |     0      | Parado      |

![Formulário "Editar estado" com detalhes para o modo de ventilador](./media/howto-set-up-template/statemeasurementsform.png)

Depois de selecionar **Gravar**, a **medição do estado do Fan Mode** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de estado do dispositivo simulado.

Se o dispositivo enviar muitos pontos de dados durante um curto período, a medida de estado será exibida com um visual diferente. Selecione o gráfico para exibir todos os pontos de dados dentro desse período de tempo em ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

### <a name="create-a-location-measurement"></a>Criar uma medição de local

Para adicionar uma nova medida de local, selecione **+ nova medida**, escolha **local** como o tipo de medida e insira os detalhes no formulário **criar medição** .

Por exemplo, você pode adicionar uma nova medida de telemetria de local:

| Nome de exibição        | Nome do campo    |
| --------------------| ------------- |
| Local do ativo      |  assetloc     |

![Formulário "criar local" com detalhes para a medida de localização](./media/howto-set-up-template/locationmeasurementsform.png)

Depois de selecionar **salvar**, a medida de **localização** aparece na lista de medições. Em breve, você verá a visualização dos dados do local do dispositivo simulado.

Ao exibir o local, você pode escolher entre as seguintes opções: o local mais recente e o histórico de localização. O **histórico de localização** só é aplicado no intervalo de tempo selecionado.

O tipo de dados da medida de localização é um objeto que contém a longitude, a latitude e uma altitude opcional. O trecho a seguir mostra a estrutura JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Quando o dispositivo real estiver conectado, o local que você adicionou como uma medida será atualizado com o valor enviado pelo dispositivo. Depois de configurar sua medição de local, você pode [Adicionar um mapa para visualizar o local no painel do dispositivo](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Configurações

As configurações controlam um dispositivo. Elas permitem que os operadores forneçam entradas ao dispositivo. É possível adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **Configurações** para os operadores usarem. Você pode adicionar vários tipos de configurações: número, texto, data, alternância e rótulo de seção.

As configurações podem estar em um dos três estados. O dispositivo informa esses estados.

- **Sincronizado**: o dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: o dispositivo está mudando para o valor de configuração.

- **Erro**: o dispositivo retornou um erro.

Por exemplo, você pode adicionar uma nova configuração de velocidade do ventilador selecionando **configurações** e inserindo na nova configuração de **número** :

| Nome de exibição  | Nome do campo    |  Unidades  | Decimais |Inicial|
| --------------| ------------- |---------| ---------|---- |
| Velocidade da ventoinha     | fanSpeed      | RPM     | 2        | 0   |

![Formulário "Configurar Número" com detalhes para configurações de velocidade](./media/howto-set-up-template/settingsform.png)

Depois que você selecionar **Salvar**, a configuração **Velocidade do Ventilador** será exibida como um bloco. Um operador pode usar a configuração na página **Device Explorer** para alterar a velocidade do ventilador do dispositivo.

## <a name="properties"></a>propriedades

As propriedades são metadados associados ao dispositivo, como um local do dispositivo fixo e um número de série. Adicione várias propriedades ao modelo de dispositivo que aparecem como blocos na guia **Propriedades** . Uma propriedade tem um tipo como número, texto, data, alternância, Propriedade do dispositivo, rótulo ou um local fixo. Um operador especifica os valores para propriedades quando eles criam um dispositivo e podem editar esses valores a qualquer momento. As propriedades de dispositivo são somente leitura e são enviadas do dispositivo para o aplicativo. Um operador não pode alterar as propriedades do dispositivo. Quando um dispositivo real se conecta, o bloco de propriedades do dispositivo é atualizado no aplicativo.

Existem duas categorias de propriedades:

- _Propriedades de dispositivo_ que o dispositivo relata ao aplicativo do IoT Central. As propriedades de dispositivo são valores somente leitura relatados pelo dispositivo e são atualizadas no aplicativo quando um dispositivo real é conectado.
- _Propriedades do aplicativo_ que são armazenadas no aplicativo e que podem ser editadas pelo operador. As propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.

Por exemplo, adicione a última data de manutenção do dispositivo como uma nova propriedade **Data** (uma propriedade do aplicativo) à guia **Propriedades**:

| Nome de exibição  | Nome do campo | Valor inicial   |
| --------------| -----------|-----------------|
| Atendido por último      | lastServiced        | 29/01/2019     |

![Formulário "Configurar a Última Manutenção" na guia "Propriedades"](./media/howto-set-up-template/propertiesform.png)

Depois que você selecionar **Salvar**, a última data de manutenção do dispositivo será exibida como um bloco.

Depois de criar o bloco, você poderá alterar o valor da propriedade do aplicativo no **Device Explorer**.

### <a name="create-a-location-property"></a>Criar uma propriedade de local

Você pode fornecer um contexto geográfico aos dados de localização no Azure IoT Central e mapear quaisquer coordenadas de latitude e longitude ou um endereço de rua. O Azure Maps habilita esse recurso na IoT Central.

Você pode adicionar dois tipos de propriedades de localização:

- **Localização como uma propriedade do aplicativo**, que é armazenada no aplicativo. As propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.
- **Localização como uma propriedade de dispositivo**, relatada pelo dispositivo ao aplicativo. Esse tipo de propriedade é melhor usado para um local estático.

> [!NOTE]
> O local como uma propriedade não registra um histórico. Se o histórico for desejado, use uma medida de local.

#### <a name="add-location-as-an-application-property"></a>Adicionar local como uma propriedade do aplicativo

Você pode criar uma propriedade de local como uma propriedade de aplicativo usando o Azure Maps no seu aplicativo IoT Central. Por exemplo, adicione o endereço de instalação do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Na biblioteca, selecione **Local**.

3. Configure **Nome de exibição**, **Nome do campo** e (opcionalmente) **Valor inicial** para o local.

    | Nome de exibição  | Nome do campo | Valor inicial |
    | --------------| -----------|---------|
    | Endereço de instalação | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário "Configurar localização" com detalhes para localização](./media/howto-set-up-template/locationcloudproperty2.png)

   Há dois formatos com suporte para adicionar um local:
   - **Local como um endereço**
   - **Local como coordenadas**

4. Clique em **Salvar**. Um operador pode atualizar o valor de localização no **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Adicionar local como uma propriedade de dispositivo

Você pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo reporta. Por exemplo, se você quiser rastrear a localização do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Selecione **Propriedade do Dispositivo** na biblioteca.

3. Configure o nome de exibição e o nome de campo e selecione **Localização** como o tipo de dados:

    | Nome de exibição  | Nome do campo | Tipo de Dados |
    | --------------| -----------|-----------|
    | Localização do dispositivo | deviceLocation | location  |

   > [!NOTE]
   > Os nomes de campo devem corresponder aos nomes de propriedade no código do dispositivo correspondente

   ![Formulário "Configurar propriedades do dispositivo" com detalhes para localização](./media/howto-set-up-template/locationdeviceproperty2.png)

Quando o dispositivo real estiver conectado, o local que você adicionou como uma propriedade de dispositivo será atualizado com o valor enviado pelo dispositivo. Depois de configurar sua propriedade Location, você pode [Adicionar um mapa para visualizar o local no painel do dispositivo](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Comandos

Os comandos são usados para gerenciar remotamente um dispositivo. Eles permitem que os operadores executem comandos no dispositivo. Você pode adicionar vários comandos ao seu modelo de dispositivo que aparecem como blocos na guia **Comandos** para os operadores usarem. Como construtor do dispositivo, você tem a flexibilidade de definir comandos de acordo com seus requisitos.

Como um comando é diferente de um cenário?

- **Configuração**: uma configuração é uma configuração que você deseja aplicar a um dispositivo. Você deseja que o dispositivo persista essa configuração até você alterá-la. Por exemplo, você deseja definir a temperatura do seu freezer e desejar essa configuração mesmo quando o freezer reiniciar.

- **Comando**: Você usa comandos para executar instantaneamente um comando no dispositivo remotamente a partir do IoT Central. Se um dispositivo não estiver conectado, o comando atingirá o tempo limite e falhará. Por exemplo, você deseja reiniciar um dispositivo.

Por exemplo, você pode adicionar um novo comando de **eco** selecionando a guia **comandos** , selecionando **+ novo comando**e inserindo os detalhes do novo comando:

| Nome de exibição  | Nome do campo | Tempo limite padrão | Tipo de Dados |
| --------------| -----------|---------------- | --------- |
| Comando echo  | echo       |  30             | text      |

![Formulário "Configurar Comando" com detalhes para o echo](./media/howto-set-up-template/commandsecho1.png)

Depois que você selecionar **Salvar**, o comando **Eco** será exibido como um bloco e estará pronto para ser usado no **Device Explorer** quando o dispositivo real for conectado. Os nomes de campo do comando devem corresponder aos nomes de propriedade no código de dispositivo correspondente para que os comandos sejam executados com êxito.

[Aqui está o link para o código de dispositivo C de exemplo.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem os dispositivos quase em tempo real. As regras invocam automaticamente ações como o envio de um email quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado. [Saiba mais sobre as regras de telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>painel

O painel é onde um operador vai para ver informações sobre um dispositivo. Como um construtor, você adiciona blocos a esta página para ajudar os operadores a entender como o dispositivo está se comportando. Você pode adicionar muitos tipos de blocos do painel, como imagem, gráfico de linhas, gráfico de barras, KPI (indicador chave de desempenho), configurações, propriedades e rótulo.

Por exemplo, adicione um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades selecionando a guia **Painel** e o bloco na Biblioteca:

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Agora, quando um operador exibir o painel no **Device Explorer**, ele poderá ver o bloco.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Adicionar uma medida de local no painel

Se você configurou uma medida de local, poderá visualizar o local com um mapa no painel do seu dispositivo. Para as medidas de localização, você tem a opção de plotar o histórico de localização.

1. Navegue para a guia **Painel**.

1. No painel do dispositivo, selecione **Mapa** da biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem o título **local atual do dispositivo**. Em seguida, escolha a medida local que você configurou anteriormente na guia **medidas** . No exemplo a seguir, a medida **local do ativo** é selecionada:

   ![Formulário "Configurar Mapa" com detalhes para título e propriedades](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Clique em **Salvar**. O bloco do mapa agora exibe o local selecionado.

Você pode redimensionar o bloco mapa. Quando um operador exibe o painel no **Device Explorer**, todos os blocos de painel que você configurou, incluindo um mapa de local, são visíveis.

### <a name="add-a-location-property-in-the-dashboard"></a>Adicionar uma propriedade local no painel

Se você configurou uma propriedade Location, poderá visualizar o local com um mapa no painel do seu dispositivo.

1. Navegue para a guia **Painel**.

1. No painel do dispositivo, selecione **Mapa** da biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem o título **local atual do dispositivo**. Em seguida, escolha a propriedade local que você configurou anteriormente na guia **Propriedades** . No exemplo a seguir, a medição do **local do dispositivo** é selecionada:

   ![Configurar o formulário de mapa com detalhes para título e propriedades](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Clique em **Salvar**. O bloco do mapa agora exibe o local selecionado.

Você pode redimensionar o bloco mapa. Quando um operador exibe o painel no **Device Explorer**, todos os blocos de painel que você configurou, incluindo um mapa de local, são visíveis.

Para saber mais sobre como usar blocos no Azure IoT Central, confira [usar blocos do Dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a configurar um modelo de dispositivo no aplicativo Central do IoT do Azure, é possível:

- [Criar uma nova versão do modelo de dispositivo](howto-version-device-template.md)
- [Conectar um dispositivo MXChip IoT DevKit ao seu aplicativo Azure IoT Central](howto-connect-devkit.md)
- [Conectar um aplicativo cliente genérico ao seu aplicativo de IoT Central do Azure (Node. js)](howto-connect-nodejs.md)
