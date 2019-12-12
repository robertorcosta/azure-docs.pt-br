---
title: Definir um novo tipo de dispositivo IoT no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como construtor, as etapas de criação de um modelo de dispositivo IoT do Azure em seu aplicativo do Azure IoT Central. Você define a telemetria, estado, propriedades e comandos para o seu tipo.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2127bec4d5fdf0d3bf76fb31c548eab98f910d42
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979012"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central (versão prévia dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado que tenha as seguintes características:

- Envia a telemetria de temperatura
- Envia a propriedade de localização
- Envia os eventos de erro do motor do ventilador
- Envia o estado operacional do ventilador
- Fornece uma propriedade gravável de velocidade do ventilador
- Fornece um comando para reiniciar o dispositivo
- Oferece uma visão geral do dispositivo por meio do painel

Nesse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais. Todos esses ventiladores têm medidas, propriedades e comandos que os operadores usam para monitorá-los e gerenciá-los. Os operadores usam os dashboards e os formulários do dispositivo para interagir com os dispositivos de ventilador.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Dispositivos** com base nos modelos de dispositivos existentes.

O [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) permite que o IoT Central integre dispositivos sem que você escreva nenhum código de dispositivo inserido. No núcleo do IoT Plug and Play está um esquema de modelo de funcionalidade do dispositivo que descreve os recursos do dispositivo. Em um aplicativo do IoT Central Versão prévia, os modelos de dispositivo usam esses modelos de funcionalidade do dispositivo de IoT Plug and Play.

Como um criador, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente o respectivo modelo de funcionalidade do dispositivo no código do dispositivo.
- Importe um modelo de capacidade de dispositivo do [catálogo de dispositivos Certificado para IoT do Azure](https://aka.ms/iotdevcat). Então adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de funcionalidade do dispositivo para o aplicativo do IoT Central e, em seguida, adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo por meio do modelo e conecte o dispositivo real ao aplicativo do IoT Central usando uma conexão device-first. O IoT Central localiza e importa o modelo de funcionalidade do dispositivo do repositório público para você. Em seguida, você pode adicionar propriedades de nuvem, personalizações e dashboards necessários para o aplicativo do IoT Central ao modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa [criar um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo com base no catálogo de dispositivos

Como um construtor, você pode começar rapidamente a criar sua solução usando um dispositivo IoT Plug and Play certificado. Confira a lista no [Catálogo de Dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se ao catálogo de dispositivos para que você possa importar um modelo de funcionalidade do dispositivo de um desses dispositivos IoT Plug and Play certificados. Para criar um modelo de dispositivo com base em um desses dispositivos no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** e selecione um dos dispositivos IoT Plug and Play certificados no catálogo. O IoT Central criará um modelo de dispositivo com base nesse modelo de funcionalidade do dispositivo.
1. Adicione propriedades de nuvem, personalizações ou exibições ao modelo de dispositivo.
1. Selecione **Publicar** para disponibilizar o modelo para os operadores para exibir e conectar dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um _modelo de funcionalidade do dispositivo_ que especifica a telemetria, as propriedades e os comandos implementados pelo dispositivo. Essas funcionalidades são organizadas em uma ou mais interfaces.
- _Propriedades de nuvem_ que definem as informações que o aplicativo do IoT Central armazena sobre seus dispositivos. Por exemplo, uma propriedade de nuvem pode registrar a data da última manutenção de um dispositivo. Essas informações nunca são compartilhadas com o dispositivo.
- As _personalizações_ permitem que o construtor substitua algumas das definições no modelo de funcionalidade do dispositivo. Por exemplo, o construtor pode substituir o nome de uma propriedade de dispositivo. Os nomes de propriedades aparecem em dashboards e formulários do IoT Central.
- Os _dashboards e os formulários_ permitem que o construtor crie uma interface do usuário que permita aos operadores monitorar e gerenciar os dispositivos conectados ao seu aplicativo.

Para criar um modelo de dispositivo no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** > **Personalizado**.
1. Insira um nome para o modelo, como **Sensor Ambiental**.
1. Pressione **Enter**. O IoT Central criará um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerenciar um modelo de dispositivo

Renomeie ou exclua um modelo na página inicial do modelo.

Depois de adicionar um modelo de funcionalidade do dispositivo ao modelo, você poderá publicá-lo. Antes de publicar o modelo, você não pode conectar um dispositivo com base nesse modelo para que os operadores o vejam na página **Dispositivos**.

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
    - Escolher uma das interfaces padrão, como a interface **Informações do Dispositivo**. As interfaces padrão especificam as funcionalidades comuns a muitos dispositivos. Essas interfaces padrão são publicadas pelo Azure IoT e não podem ter controle de versão nem ser editadas.

1. Depois de criar uma interface, escolha **Editar Identidade** para alterar o nome de exibição da interface.

1. Se você optar por criar uma interface personalizada do zero, poderá adicionar as funcionalidades do dispositivo. As funcionalidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviado do dispositivo, normalmente de um sensor. Por exemplo, um sensor pode relatar a temperatura ambiente.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de telemetria:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor de telemetria usado em dashboards e formulários. |
| NOME | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Telemetria. |
| Tipo Semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados telemétricos, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Severity | Disponível somente para o tipo semântico de evento. As severidades são **Erro**, **Informação** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor de telemetria, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em dashboards e formulários. |
| Comentário | Comentários sobre a funcionalidade de telemetria. |
| DESCRIÇÃO | Uma descrição da funcionalidade de telemetria. |

### <a name="properties"></a>propriedades

As propriedades representam valores de ponto no tempo. Por exemplo, um dispositivo pode usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. Você pode definir propriedades graváveis no IoT Central.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de propriedade:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade usado em dashboards e formulários. |
| NOME | O nome da propriedade. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Propriedade. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Gravável | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central, e o IoT Central poderá enviar atualizações de propriedade para o dispositivo.
| Severity | Disponível somente para o tipo semântico de evento. As severidades são **Erro**, **Informação** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em dashboards e formulários. |
| Comentário | Comentários sobre a funcionalidade de propriedade. |
| DESCRIÇÃO | Uma descrição da funcionalidade de propriedade. |

### <a name="commands"></a>Comandos

Você pode chamar comandos do dispositivo no IoT Central. Opcionalmente, os comandos passam parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, você pode chamar um comando para reinicializar um dispositivo em 10 segundos.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de comando:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do comando usado em dashboards e formulários. |
| NOME | O nome do comando. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Comando. |
| Comando | `SynchronousExecutionType`. |
| Comentário | Comentários sobre a funcionalidade de comando. |
| DESCRIÇÃO | Uma descrição da funcionalidade de comando. |
| Solicitação | Se habilitada, uma definição do parâmetro de solicitação, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Response | Se habilitada, uma definição da resposta do comando, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |

## <a name="manage-an-interface"></a>Gerenciar uma interface

Se você não tiver publicado a interface, poderá editar as funcionalidades definidas por ela. Depois de publicar a interface, se quiser fazer alterações, você precisará criar uma versão do modelo de dispositivo e realizar o controle de versão da interface. Você pode fazer alterações que não exigem controle de versão, como nomes de exibição ou unidades, na seção **Personalizar**.

Exporte também a interface como um arquivo JSON caso deseje reutilizá-la em outro modelo de funcionalidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Use propriedades de nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades de nuvem nunca são enviadas a um dispositivo. Por exemplo, você pode usar propriedades de nuvem para armazenar o nome do cliente que instalou o dispositivo ou a data do último serviço do dispositivo.

A seguinte tabela mostra as definições de configuração para uma propriedade de nuvem:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade de nuvem usado em dashboards e formulários. |
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

Gerar modos de exibição padrão é uma maneira rápida de visualizar as informações importantes do dispositivo. Você tem até três modos de exibição padrão geradas para seu modelo de dispositivo:

- **Comandos** fornecem uma exibição com os comandos do dispositivo e permitem a expedição deles pelo operador ao dispositivo.
- **Visão geral** oferece uma exibição com a telemetria do dispositivo, exibindo gráficos e métricas.
- **Sobre** oferece uma exibição com informações sobre o dispositivo, exibindo as propriedades do dispositivo.

Depois de selecionar **Gerar exibições padrão**, você vê que elas foram adicionadas automaticamente à seção **Exibições** do modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar dashboards

Adicione painéis a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter vários dashboards para um modelo de dispositivo.

Para adicionar um painel a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Escolha **Como visualizar o Dispositivo**.
1. Insira um nome para o painel em **Nome do Painel**.
1. Adicione blocos ao painel na lista de blocos estáticos, de propriedade, de propriedade de nuvem, de telemetria e de comando. Arraste e solte os blocos que você deseja adicionar ao painel.
1. Para plotar vários valores de telemetria em um só bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **Combinar**.
1. Configure cada bloco que você adicionar para personalizar a forma como ele exibe os dados. Você pode fazer isso selecionando o ícone de engrenagem ou selecionando **Alterar configuração** no bloco do gráfico.
1. Organize e redimensione os blocos no painel.
1. Salve as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurar o dispositivo de visualização para exibir o painel

Para exibir e testar seu painel, selecione **Configurar o dispositivo de visualização**. Isso permite que você veja o painel como o operador o vê depois de ser publicado. Use essa opção para validar se as exibições mostram os dados corretos. É possível escolher um das seguintes opções:

- Nenhum dispositivo de visualização.
- O dispositivo de teste real que você configurou para seu modelo de dispositivo.
- Um dispositivo existente em seu aplicativo usando a ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerenciem um dispositivo exibindo e definindo propriedades. Os operadores só podem editar propriedades de nuvem e propriedades de dispositivo graváveis. Você pode ter vários formulários para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Escolha **Como editar dados de Dispositivo e de Nuvem**.
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

Depois que você publicar um modelo de dispositivo, um operador poderá acessar a página **Dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar a modificar e salvar o modelo de dispositivo enquanto estiver fazendo alterações. Quando desejar enviar essas alterações por push para o operador visualizar na página **Dispositivos**, selecione **Publicar** a cada vez.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definir um novo tipo de dispositivo de gateway IoT (versão prévia dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra a você, como construtor, as etapas de uso de um modelo de dispositivo de gateway para definir um novo tipo de dispositivo IoT em seu aplicativo do IoT Central. 

Nesta seção, você criará um modelo de dispositivo de **Edifício Inteligente**. Um dispositivo de gateway de Edifício Inteligente:

* Envia a telemetria como temperatura e ocupação.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.
* Permite relações com outros modelos de funcionalidade do dispositivo.

### <a name="create-iot-device-templates"></a>Criar modelos de dispositivo IoT

Veja como criar modelos de dispositivo IoT: 

1. No painel de navegação esquerdo, selecione **Modelos de Dispositivo**. Em seguida, selecione **+ Novo** e selecione o bloco **Dispositivo IoT** e o bloco do sensor de ocupação. Selecione **Avançar: Personalizar**.

   ![Captura de tela de opções e página de Modelos de Dispositivo](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

1. Na página **Examinar**, selecione **Criar**. 

   ![Captura de tela da página Examinar](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

1. Um novo modelo de dispositivo é criado. 

   ![Captura de tela do novo modelo de dispositivo](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

É assim que se cria um modelo de dispositivo para o Sensor S1:

1. No painel de navegação esquerdo, selecione **Modelos de Dispositivo**. Em seguida, selecione **+ Novo** e selecione o bloco **Dispositivo IoT** e selecione o bloco do sensor de ocupação. Selecione **Avançar: Personalizar**.

   ![Captura de tela de opções e página de Modelos de Dispositivo](./media/tutorial-define-iot-device-type/s1-sensor.png)

1. Na página **Examinar**, selecione **Criar**. 

   ![Captura de tela da página Examinar](./media/tutorial-define-iot-device-type/s1-review.png)

1. Um novo modelo de dispositivo é criado. 

   ![Captura de tela do novo modelo de dispositivo](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Criar um modelo de dispositivo de gateway IoT

Você pode optar por criar um modelo de dispositivo de gateway IoT. O dispositivo de gateway terá relacionamentos com dispositivos downstream que se conectam ao IoT Central por meio do dispositivo de gateway. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Relações de dispositivo downstream com dispositivo de gateway

Os dispositivos IoT podem se conectar a um dispositivo de gateway IoT.

![Diagrama de relação entre o dispositivo de gateway e os dispositivos downstream](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Como construtor, você pode criar e editar os modelos de dispositivo de gateway IoT no seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-a-device-template-type"></a>Selecione um tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo a seu aplicativo:

1. No painel esquerdo, selecione a guia **Modelos de Dispositivo**.

   ![Captura de tela da página de Modelos de dispositivo](./media/tutorial-define-iot-device-type/devicetemplate.png)

1. Selecione **+ Novo** para começar a criar um modelo de dispositivo.

   ![Captura de tela da página Modelos de dispositivo com Novo realçado](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

   ![Captura de tela da página Personalizar dispositivo](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Na página **Selecionar tipo de modelo**, selecione **Azure IoT** e selecione **Avançar: Personalizar**.

   ![Captura de tela da página Selecionar tipo de modelo](./media/tutorial-define-iot-device-type/gateway-customize.png)

1. Marque a caixa de seleção do gateway e selecione **Criar**.

   ![Captura de tela da página Personalizar dispositivo, com o gateway realçado](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Na página Examinar, selecione **Criar**. 

1. Insira o nome do modelo de gateway **Modelo de Gateway de Edifício Inteligente**. Selecione o bloco **Personalizado**.

1. Adicione uma interface padrão **Informações do Dispositivo**.

### <a name="add-relationships"></a>Adicionar relações

Você pode adicionar relações de downstream aos modelos de funcionalidade do dispositivo para os dispositivos conectados a um dispositivo de gateway.

Crie relações com modelos de funcionalidade do dispositivo downstream. Clique em **Salvar**.

![Captura de tela do Modelo de Gateway de Criação Inteligente, com várias opções realçadas](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas nem recebidas de um dispositivo.

1. Selecione **Propriedades de Nuvem** >  **+ Adicionar Propriedade de Nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao seu modelo de dispositivo.

    | Nome de exibição      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data do Último Serviço | Nenhum          | Data   |
    | Nome do cliente     | Nenhum          | Cadeia de caracteres |

2. Clique em **Salvar**.

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações para modificar uma interface ou para adicionar recursos específicos do IoT Central a uma funcionalidade que não exige o controle de versão de seu modelo de funcionalidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, você pode:

- Personalizar o nome de exibição e as unidades de uma funcionalidade.
- Adicionar uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especificar os valores iniciais, mínimos e máximos, para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade.

Quando tiver terminado de personalizar, selecione **Salvar**.

### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo de sensor ambiental para um operador. Suas personalizações habilitam o operador a gerenciar os dispositivos de sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="generate-default-views"></a>Gerar modos de exibição padrão

Se você selecionar **Gerar exibições padrão**, poderá gerar os painéis **Visão geral** e **Sobre**. 

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para seu modelo de dispositivo na página **Modelos de Dispositivo**.

2. Selecione **Publicar**.

3. Na caixa de diálogo **Publicar um Modelo de Dispositivo**, escolha **Publicar**.

Depois que um modelo de dispositivo é publicado, ele fica visível na página **Dispositivos** e para o operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de funcionalidade do dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, nas personalizações e nos modos de exibição, em um modelo de dispositivo publicado. Essas atualizações não fazem com que uma nova versão seja criada. Depois de fazer qualquer alteração, selecione **Publicar** para enviar por push essas alterações para o operador.

## <a name="create-a-gateway-simulated-device"></a>Criar um dispositivo simulado de gateway

No Gerenciador de Dispositivos, crie um gateway de edifício inteligente simulado. 

![Captura de tela da caixa de diálogo Criar dispositivo](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Criar dispositivos simulados downstream

No Gerenciador de Dispositivos, crie um sensor de ocupação simulado. 

![Captura de tela da caixa de diálogo Criar dispositivo](./media/tutorial-define-iot-device-type/occupancydevice.png)

No Gerenciador de Dispositivos, crie um sensor S1 simulado. 

![Captura de tela da caixa de diálogo Criar dispositivo](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-a-gateway-device"></a>Adicionar relações de dispositivos downstream a um dispositivo de gateway

Selecione Sensor de Ocupação e Sensor S1 e selecione **Conectar ao gateway**. 

![Captura de tela do sensor de ocupação, com Conectar ao gateway realçado](./media/tutorial-define-iot-device-type/connecttogateway.png)

Selecione um modelo de dispositivo de gateway e uma instância de dispositivo de gateway e selecione **Unir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

* Criar um gateway IoT como um modelo de dispositivo.
* Criar propriedades da nuvem.
* Criar personalizações.
* Definir uma visualização para a telemetria do dispositivo.
* Adicionar relacionamentos.
* Publicar seu modelo de dispositivo.

Em seguida, você pode:

> [!div class="nextstepaction"]
> [Conectar um dispositivo](tutorial-connect-pnp-device.md)
