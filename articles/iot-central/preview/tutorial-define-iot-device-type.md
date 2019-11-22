---
title: Definir um novo tipo de dispositivo IoT no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como construtor, as etapas de criação de um modelo de dispositivo IoT do Azure em seu aplicativo do Azure IoT Central. Você define a telemetria, estado, propriedades e comandos para o seu tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5642ce6065c4b76bdbd6d772c74fed894de0888f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892452"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central (versões prévias dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado que tenha as seguintes características:

- Envia a telemetria de temperatura
- Envia a propriedade de localização
- Envia os eventos de erro do motor do ventilador
- Envia o estado operacional do ventilador
- Propriedade gravável de velocidade do ventilador
- Comando para reiniciar o dispositivo
- Painel que oferece uma visão geral do dispositivo

Nesse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais. Todos esses ventiladores têm medidas, propriedades e comandos que os operadores usam para monitorá-los e gerenciá-los. Os operadores usam os painéis e os formulários do dispositivo para interagir com os dispositivos de ventilador.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Dispositivos** com base nos modelos de dispositivos existentes.

O [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) permite que o IoT Central integre dispositivos sem que você escreva nenhum código de dispositivo inserido. No núcleo do IoT Plug and Play está um esquema de modelo de funcionalidade do dispositivo que descreve os recursos do dispositivo. Em um aplicativo do IoT Central Versão prévia, os modelos de dispositivo usam esses modelos de funcionalidade do dispositivo de IoT Plug and Play.

Como um criador, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente o respectivo modelo de funcionalidade do dispositivo no código do dispositivo.
- Importe um modelo de funcionalidade do dispositivo do [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/iotdevcat) e adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de funcionalidade do dispositivo para o aplicativo do IoT Central e, em seguida, adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo por meio do modelo e conecte o dispositivo real ao aplicativo do IoT Central usando uma conexão device-first. O IoT Central localiza e importa o modelo de funcionalidade do dispositivo do repositório público para você. Em seguida, você pode adicionar propriedades de nuvem, personalizações e painéis necessários para o aplicativo do IoT Central ao modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Siga este início rápido para [Criar um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo com base no catálogo de dispositivos

Como construtor, você pode começar rapidamente a criar sua solução usando um dispositivo IoT Plug and Play certificado listado no [catálogo de dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se ao catálogo de dispositivos para permitir que você importe um modelo de funcionalidade do dispositivo de um desses dispositivos IoT Plug and Play certificados. Para criar um modelo de dispositivo com base em um desses dispositivos no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** e, em seguida, selecione um dos dispositivos IoT Plug and Play certificados no catálogo listado abaixo. O IoT Central criará um modelo de dispositivo com base nesse modelo de funcionalidade do dispositivo.
1. Adicione propriedades de nuvem, personalizações ou exibições ao modelo de dispositivo.
1. Selecione **Publicar** para publicar esse modelo de dispositivo e disponibilizá-lo para os operadores verem e conectarem dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um _modelo de funcionalidade do dispositivo_ que especifica a telemetria, as propriedades e os comandos implementados pelo dispositivo. Essas funcionalidades são organizadas em uma ou mais interfaces.
- _Propriedades de nuvem_ que definem as informações que o aplicativo do IoT Central armazena sobre seus dispositivos. Por exemplo, uma propriedade de nuvem pode registrar os dados da última manutenção de um dispositivo. Essas informações nunca são compartilhadas com o dispositivo.
- As _personalizações_ permitem que o construtor substitua algumas das definições no modelo de funcionalidade do dispositivo. Por exemplo, o construtor pode substituir o nome de uma propriedade de dispositivo. Os nomes de propriedades aparecem em painéis e formulários do IoT Central.
- Os _painéis e os formulários_ permitem que o construtor crie uma interface do usuário que permita aos operadores monitorar e gerenciar os dispositivos conectados ao seu aplicativo.

Para criar um modelo de dispositivo no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** e, em seguida, **Personalizado**.
1. Insira um nome para o modelo, como **Sensor Ambiental**.
1. Pressione **Enter**. O IoT Central criará um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerenciar um modelo de dispositivo

Renomeie ou exclua um modelo na home page do modelo.

Depois de adicionar um modelo de funcionalidade do dispositivo ao modelo, você poderá publicá-lo. Você só poderá conectar um dispositivo com base nesse modelo para que os operadores o vejam na página **Dispositivos** quando publicar o modelo.

## <a name="create-a-capability-model"></a>Criar um modelo de funcionalidade

Para criar um modelo de funcionalidade do dispositivo, você pode:

- Usar o IoT Central para criar um modelo personalizado do zero.
- Importar um modelo de um arquivo JSON. Um construtor de dispositivo pode ter usado o Visual Studio Code para criar um modelo de funcionalidade do dispositivo para seu aplicativo.
- Selecionar um dos dispositivos no catálogo de dispositivos. Essa opção importa o modelo de funcionalidade do dispositivo que o fabricante publicou para esse dispositivo. Um modelo de funcionalidade do dispositivo importado como este é publicado automaticamente.

## <a name="manage-a-capability-model"></a>Gerenciar um modelo de funcionalidade

Depois de criar um modelo de funcionalidade do dispositivo, você pode:

- Adicionar interfaces ao modelo. Um modelo precisa ter, pelo menos, uma interface.
- Editar os metadados do modelo, como ID, namespace e nome.
- Excluir o modelo.

## <a name="create-an-interface"></a>Criar uma interface

Uma funcionalidade do dispositivo precisa ter, pelo menos, uma interface. Uma interface é uma coleção reutilizável de funcionalidades.

Para criar uma interface:

1. Acesse o modelo de funcionalidade do dispositivo e escolha **+ Adicionar Interface**.

1. Na página **Selecionar uma Interface**, você pode:

    - Criar uma interface personalizada do zero.
    - Importar uma interface existente de um arquivo. Um construtor de dispositivo pode ter usado o Visual Studio Code para criar uma interface para seu dispositivo.
    - Escolher uma das interfaces padrão, como a interface **Informações do Dispositivo**. As interfaces padrão especificam as funcionalidades comuns a muitos dispositivos. Essas interfaces padrão são publicadas pelo Microsoft Azure IoT e não podem ter controle de versão nem ser editadas.

1. Depois de criar uma interface, escolha **Editar Identidade** para alterar o nome de exibição da interface.

1. Se você optar por criar uma interface personalizada do zero, poderá adicionar as funcionalidades do dispositivo. As funcionalidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviado do dispositivo, normalmente de um sensor. Por exemplo, um sensor pode relatar a temperatura ambiente.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de telemetria:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor de telemetria usado em painéis e formulários. |
| NOME | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Telemetria. |
| Tipo Semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados telemétricos, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Severity | Disponível somente para o tipo semântico de evento. **Erro**, **Informações** ou **Aviso**. |
| Valores de estado | Disponível somente para o tipo semântico de estado. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor de telemetria, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em painéis e formulários. |
| Comentário | Comentários sobre a funcionalidade de telemetria. |
| DESCRIÇÃO | Uma descrição da funcionalidade de telemetria. |

### <a name="properties"></a>propriedades

As propriedades representam valores de ponto no tempo. Por exemplo, um dispositivo pode usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. Você pode definir propriedades graváveis no IoT Central.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de propriedade:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade usado em painéis e formulários. |
| NOME | O nome da propriedade. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Propriedade. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Gravável | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central, e o IoT Central poderá enviar atualizações de propriedade para o dispositivo.
| Severity | Disponível somente para o tipo semântico de evento. **Erro**, **Informações** ou **Aviso**. |
| Valores de estado | Disponível somente para o tipo semântico de estado. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em painéis e formulários. |
| Comentário | Comentários sobre a funcionalidade de propriedade. |
| DESCRIÇÃO | Uma descrição da funcionalidade de propriedade. |

### <a name="commands"></a>Comandos

Você pode chamar comandos do dispositivo no IoT Central. Opcionalmente, os comandos passam parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, você pode chamar um comando para reinicializar um dispositivo em 10 segundos.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de comando:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do comando usado em painéis e formulários. |
| NOME | O nome do comando. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Comando |
| Comando | SynchronousExecutionType. |
| Comentário | Comentários sobre a funcionalidade de comando. |
| DESCRIÇÃO | Uma descrição da funcionalidade de comando. |
| Solicitação | Se habilitada, uma definição do parâmetro de solicitação, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Response | Se habilitada, uma definição da resposta do comando, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |

## <a name="manage-an-interface"></a>Gerenciar uma interface

Desde que não tenha publicado a interface, você pode editar as funcionalidades definidas por ela. Depois que a interface for publicada, você precisará criar uma versão do modelo de dispositivo e realizar o controle de versão da interface para fazer alterações. As alterações que não exigem controle de versão, como nomes de exibição ou unidades, podem ser feitas na seção **Personalizar**.

Exporte também a interface como um arquivo JSON caso deseje reutilizá-la em outro modelo de funcionalidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Use propriedades de nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades de nuvem nunca são enviadas a um dispositivo. Por exemplo, você pode usar propriedades de nuvem para armazenar o nome do cliente que instalou o dispositivo ou a data do último serviço do dispositivo.

A seguinte tabela mostra as definições de configuração para uma propriedade de nuvem:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade de nuvem usado em painéis e formulários. |
| NOME | O nome da propriedade de nuvem. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade de nuvem, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface importada ou adicionar recursos específicos do IoT Central a uma funcionalidade. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, você pode:

- Personalizar o nome de exibição e as unidades de uma funcionalidade.
- Adicionar uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especificar os valores iniciais, mínimos e máximos, para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Se houver alterações que não podem ser feitas na seção **Personalizar**, você precisará fazer o controle de versão do modelo de dispositivo e da interface para modificar a funcionalidade.

### <a name="generate-default-views"></a>Gerar modos de exibição padrão

Gerar modos de exibição padrão é uma maneira rápida de começar a visualizar as informações importantes do dispositivo. Você terá até três exibições padrão geradas para o modelo de dispositivo:

- **Comandos** fornecerá uma exibição com os comandos do dispositivo e permitirão a expedição deles pelo operador ao dispositivo.
- **Visão geral** oferecerá uma exibição com a telemetria do dispositivo, exibindo gráficos e métricas.
- **Sobre** fornecerá uma exibição com informações sobre o dispositivo, exibindo as propriedades dele.

Depois de selecionar **Gerar exibições padrão**, você verá que elas foram adicionadas automaticamente à seção **Exibições** do modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar painéis

Adicione painéis a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter vários painéis para um modelo de dispositivo.

Para adicionar um painel a um modelo de dispositivo:

- Acesse o modelo de dispositivo e selecione **Exibições**.
- Em seguida, escolha **Visualizando o Dispositivo**.
- Insira um nome para o painel em **Nome do Painel**.
- Adicione blocos ao painel na lista de blocos estáticos, de propriedade, de propriedade de nuvem, de telemetria e de comando. Arraste e solte os blocos que deseja adicionar ao painel.
- Para plotar vários valores de telemetria em um só bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **Combinar**.
- Configure cada bloco adicionado para personalizar como ele exibirá os dados selecionando o ícone de engrenagem ou o botão **Alterar configuração** no bloco do gráfico.
- Organize e redimensione os blocos no painel.
- Salve as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurar o dispositivo de visualização para exibir o painel

Para exibir e testar o painel, selecione **Configurar o dispositivo de visualização**, que permite ver o painel conforme visto pelo operador depois de publicado. Essa opção permite validar se as exibições mostram os dados corretos. Você pode escolher entre um dispositivo de não visualização, o dispositivo de teste real que você configurou para o modelo de dispositivo ou um dispositivo existente no aplicativo usando a identificação do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerenciem um dispositivo exibindo e definindo propriedades. Os operadores só podem editar propriedades de nuvem e propriedades de dispositivo graváveis. Você pode ter vários formulários para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Em seguida, escolha **Editando dados de Dispositivo e de Nuvem**.
1. Insira um nome para o formulário em **Nome do Formulário**.
1. Selecione o número de colunas a ser usado para definir o layout do formulário.
1. Adicione propriedades a uma seção existente no formulário ou selecione propriedades e escolha **Adicionar Seção**. Use seções para agrupar propriedades no formulário. Você pode adicionar um título a uma seção.
1. Configure cada propriedade do formulário para personalizar o comportamento dela.
1. Organize as propriedades no formulário.
1. Salve as alterações.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de conectar um dispositivo que implementa o modelo de funcionalidade do dispositivo, você precisará publicar o modelo de dispositivo.

Depois de publicar um modelo de dispositivo, você só poderá fazer alterações limitadas no modelo de funcionalidade do dispositivo. Para modificar uma interface, você precisará [criar e publicar outra versão](./howto-version-device-template.md).

Para publicar um modelo de dispositivo, acesse o modelo de dispositivo e selecione **Publicar**.

Depois que você publicar um modelo de dispositivo, um operador poderá acessar a página **Dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar modificando e salvando o modelo de dispositivo enquanto estiver fazendo alterações; no entanto, quando desejar efetuar push dessas alterações para que elas sejam vistas pelo operador na página **Dispositivos**, você precisará selecionar **Publicar** todas as vezes.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definir um novo tipo de dispositivo de gateway IoT (versões prévias dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra a você, como construtor, as etapas de uso de um modelo de dispositivo de gateway para definir um novo tipo de dispositivo IoT do Azure em seu aplicativo do Azure IoT Central. 

Nesta seção, você criará um modelo de dispositivo de **Edifício Inteligente**. Um dispositivo de gateway de Edifício Inteligente:

* Envia a telemetria como temperatura e ocupação.
* Responde a propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos, como a redefinição da temperatura.
* Permite relações com outros modelos de funcionalidade do dispositivo

### <a name="create-iot-device-templates"></a>Criar modelos de dispositivo IoT

Você criará modelos de dispositivo IoT. 

Clique em Modelos de Dispositivo no painel de navegação à esquerda, clique em **+ Novo**, selecione o bloco **Dispositivo IoT** e selecione o bloco de sensor de ocupação e clique em **Avançar: Personalizar**

![Dispositivo IoT](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

A página de revisão será apresentada. Clique no botão **Criar**. 

![Dispositivo IoT](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

Um modelo de dispositivo será criado. 

![Dispositivo IoT](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

Você criará um modelo de dispositivo para o Sensor S1. 

Clique em Modelos de Dispositivo no painel de navegação à esquerda, clique em **+ Novo**, selecione o bloco **Dispositivo IoT** e selecione o bloco de sensor de ocupação e clique em **Avançar: Personalizar**

![Dispositivo IoT](./media/tutorial-define-iot-device-type/s1-sensor.png)

A página de revisão será apresentada. Clique no botão **Criar**. 

![Dispositivo downstream](./media/tutorial-define-iot-device-type/s1-review.png)

Um modelo de dispositivo será criado. 

![Dispositivo downstream](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Criar um modelo de dispositivo de gateway IoT

Você pode optar por criar um modelo de dispositivo de gateway IoT. O dispositivo de gateway terá relações com dispositivos downstream que se conectam ao IoT Central por meio do dispositivo de gateway. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Relações de dispositivo downstream com dispositivo de gateway

Os dispositivos IoT podem se conectar ao dispositivo de gateway IoT do Azure 

![Página Aplicativo do Central](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Como construtor, você pode criar e editar os modelos de dispositivo de gateway IoT do Azure no seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-device-template-type"></a>Selecionar um tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá até a página **Modelos de Aplicativo**. Para fazer isso, selecione a guia **Modelos de Dispositivo** no painel esquerdo.

![Página Aplicativo do Central](./media/tutorial-define-iot-device-type/devicetemplate.png)

Clique em **+ Novo** para começar a criar um modelo de dispositivo.

![Modelos de dispositivo – Novo](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

![Seleção de modelos de dispositivo – Gateway](./media/tutorial-define-iot-device-type/gateway-review.png)

Você será direcionado à página de seleção do tipo de modelo de dispositivo. Selecione o bloco **IoT do Azure** e clique no botão **Avançar: Personalizar** na parte inferior

Selecione a caixa de seleção Gateway e clique em **Criar** 

![Seleção de modelos de dispositivo – Gateway](./media/tutorial-define-iot-device-type/gateway-customize.png)

Uma página de revisão será apresentada. Clique em **Criar** 

![Modelo de dispositivo – Gateway](./media/tutorial-define-iot-device-type/gateway-review.png)

Insira o nome do modelo de gateway **Modelo de Gateway de Edifício Inteligente**. Clique em bloco **Personalizado**.

Adicione uma interface padrão **Informações do Dispositivo**.

### <a name="add-relationships"></a>Adicionar relações

Você pode adicionar relações de downstream aos modelos de funcionalidade do dispositivo para os dispositivos que serão conectados ao dispositivo de gateway.

Crie relações com modelos de funcionalidade do dispositivo downstream. Clique em **Salvar**

![Modelo de dispositivo – Gateway](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades da Nuvem** e, em seguida, **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao seu modelo de dispositivo.

    | Nome de exibição      | Tipo Semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data do Último Serviço | Nenhum          | Data   |
    | Nome do cliente     | Nenhum          | Cadeia de caracteres |

2. Selecione **Salvar** para salvar as alterações:

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface ou adicionar recursos específicos de IoT Central a uma funcionalidade que não exige uma nova versão do seu modelo de funcionalidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, você pode:

- Personalizar o nome de exibição e as unidades de uma funcionalidade.
- Adicionar uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especificar os valores iniciais, mínimos e máximos, para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Clique em **Salvar**

### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo de sensor ambiental para um operador. Suas personalizações habilitam o operador a gerenciar os dispositivos de sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="generate-default-views"></a>Gerar modos de exibição padrão

Para este tutorial, clique em Gerar exibições padrão. Os painéis Visão geral e Sobre serão gerados. 

## <a name="publish-device-template"></a>Publicar um modelo de dispositivo

Antes de criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para seu modelo de dispositivo na página **Modelos de Dispositivo**.

2. Selecione **Publicar**.

3. Na caixa de diálogo **Publicar um Modelo de Dispositivo** escolha **Publicar**:

Depois que um modelo de dispositivo é publicado, ele fica visível na página **Dispositivos** e para o operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de funcionalidade do dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, nas personalizações e nos modos de exibição, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer qualquer alteração, selecione **Publicar** para enviar por push essas alterações para o operador.

## <a name="create-gateway-simulated-device"></a>Criar dispositivo simulado de gateway

No Gerenciador de Dispositivos, crie um gateway de edifício inteligente simulado. 

![Modelo de dispositivo – Gateway](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Criar dispositivos simulados downstream

No Gerenciador de Dispositivos, crie um sensor de ocupação simulado. 

![Modelo de dispositivo – ocupação](./media/tutorial-define-iot-device-type/occupancydevice.png)

No Gerenciador de Dispositivos, crie um sensor s1 simulado. 

![Modelo de dispositivo – s1](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Adicionar relações de dispositivos downstream com um dispositivo de gateway

Selecione o Sensor S1 e o Sensor de Ocupação e clique em **Conectar ao gateway**. 

![Modelo de dispositivo – s1](./media/tutorial-define-iot-device-type/connecttogateway.png)

Selecione o modelo de dispositivo de gateway, a instância de dispositivo de gateway e clique em **Ingressar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

* Criar um gateway IoT como um modelo de dispositivo
* Criar propriedades da nuvem.
* Criar personalizações.
* Definir uma visualização para a telemetria do dispositivo.
* Adicionar relações
* Publicar seu modelo de dispositivo.

Esta é uma sugestão para a próxima etapa:

> [!div class="nextstepaction"]
> [Conectar um dispositivo](tutorial-connect-pnp-device.md)
