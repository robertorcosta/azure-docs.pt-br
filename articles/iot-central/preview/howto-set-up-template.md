---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 648d93b996cf8d4ef6a8beb323bb1ebce9d4c9c0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974369"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Configurar e gerenciar um modelo de dispositivo (recursos de visualização)

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

Nesse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais. Todos esses ventiladores têm medidas, propriedades e comandos que os operadores usam para monitorá-los e gerenciá-los. Os operadores usam os dashboards e os formulários do dispositivo para interagir com os dispositivos de ventilador.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Dispositivos** com base nos modelos de dispositivos existentes.

O [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) permite que o IoT Central integre dispositivos sem que você escreva nenhum código de dispositivo inserido. No núcleo do IoT Plug and Play está um esquema de modelo de funcionalidade do dispositivo que descreve os recursos do dispositivo. Em um aplicativo do IoT Central Versão prévia, os modelos de dispositivo usam esses modelos de funcionalidade do dispositivo de IoT Plug and Play.

Como um criador, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente o respectivo modelo de funcionalidade do dispositivo no código do dispositivo.
- Importe um modelo de funcionalidade do dispositivo do [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/iotdevcat) e adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo do IoT Central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de funcionalidade do dispositivo para o aplicativo do IoT Central e, em seguida, adicione todas as propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT central precisa.
- Crie um modelo de funcionalidade do dispositivo usando o Visual Studio Code. Implemente o código do dispositivo por meio do modelo e conecte o dispositivo real ao aplicativo do IoT Central usando uma conexão device-first. O IoT Central localiza e importa o modelo de funcionalidade do dispositivo do repositório público para você. Em seguida, você pode adicionar propriedades de nuvem, personalizações e dashboards necessários para o aplicativo do IoT Central ao modelo de dispositivo.

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo com base no catálogo de dispositivos

Como construtor, você pode começar rapidamente a criar sua solução usando um dispositivo IoT Plug and Play certificado listado no [catálogo de dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se ao catálogo de dispositivos para permitir que você importe um modelo de funcionalidade do dispositivo de um desses dispositivos IoT Plug and Play certificados. Para criar um modelo de dispositivo com base em um desses dispositivos no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** e, em seguida, selecione um dos dispositivos IoT Plug and Play certificados no catálogo listado abaixo. O IoT Central criará um modelo de dispositivo com base nesse modelo de funcionalidade do dispositivo.
1. Adicione propriedades de nuvem, personalizações ou exibições ao modelo de dispositivo.
1. Selecione **Publicar** para publicar esse modelo de dispositivo e disponibilizá-lo para os operadores exibirem e conectarem dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um _modelo de funcionalidade do dispositivo_ que especifica a telemetria, as propriedades e os comandos implementados pelo dispositivo. Essas funcionalidades são organizadas em uma ou mais interfaces.
- _Propriedades de nuvem_ que definem as informações que o aplicativo do IoT Central armazena sobre seus dispositivos. Por exemplo, uma propriedade de nuvem pode registrar os dados da última manutenção de um dispositivo. Essas informações nunca são compartilhadas com o dispositivo.
- As _personalizações_ permitem que o construtor substitua algumas das definições no modelo de funcionalidade do dispositivo. Por exemplo, o construtor pode substituir o nome de uma propriedade de dispositivo. Os nomes de propriedades aparecem em dashboards e formulários do IoT Central.
- Os _dashboards e os formulários_ permitem que o construtor crie uma interface do usuário que permita aos operadores monitorar e gerenciar os dispositivos conectados ao seu aplicativo.

Para criar um modelo de dispositivo no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ Novo** e, em seguida, **Personalizado**.
1. Insira um nome para o modelo, como **Sensor Ambiental**.
1. Pressione **Enter**. O IoT Central criará um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerenciar um modelo de dispositivo

Renomeie ou exclua um modelo na página inicial do modelo.

Depois de adicionar um modelo de funcionalidade do dispositivo ao modelo, você poderá publicá-lo. Você não poderá conectar um dispositivo com base nesse modelo para que os operadores o vejam na página **Dispositivos** até publicar o modelo.

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

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor de telemetria usado em dashboards e formulários. |
| name | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Telemetria. |
| Tipo Semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados telemétricos, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Gravidade | Disponível somente para o tipo semântico de evento. **Erro**, **Informações** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor de telemetria, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em dashboards e formulários. |
| Comentário | Comentários sobre a funcionalidade de telemetria. |
| Descrição | Uma descrição da funcionalidade de telemetria. |

### <a name="properties"></a>propriedades

As propriedades representam valores de ponto no tempo. Por exemplo, um dispositivo pode usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. Você pode definir propriedades graváveis no IoT Central.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade usado em dashboards e formulários. |
| name | O nome da propriedade. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Propriedade. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Gravável | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para o IoT Central, e o IoT Central poderá enviar atualizações de propriedade para o dispositivo.
| Gravidade | Disponível somente para o tipo semântico de evento. **Erro**, **Informações** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em dashboards e formulários. |
| Comentário | Comentários sobre a funcionalidade de propriedade. |
| Descrição | Uma descrição da funcionalidade de propriedade. |

### <a name="commands"></a>Comandos

Você pode chamar comandos do dispositivo no IoT Central. Opcionalmente, os comandos passam parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, você pode chamar um comando para reinicializar um dispositivo em 10 segundos.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do comando usado em dashboards e formulários. |
| name | O nome do comando. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de Funcionalidade | Command |
| Command | SynchronousExecutionType. |
| Comentário | Comentários sobre a funcionalidade de comando. |
| Descrição | Uma descrição da funcionalidade de comando. |
| Solicitação | Se habilitada, uma definição do parâmetro de solicitação, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Resposta | Se habilitada, uma definição da resposta do comando, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |

## <a name="manage-an-interface"></a>Gerenciar uma interface

Desde que não tenha publicado a interface, você pode editar as funcionalidades definidas por ela. Depois que a interface for publicada, você precisará criar uma versão do modelo de dispositivo e realizar o controle de versão da interface para fazer alterações. As alterações que não exigem controle de versão, como nomes de exibição ou unidades, podem ser feitas na seção **Personalizar**.

Exporte também a interface como um arquivo JSON caso deseje reutilizá-la em outro modelo de funcionalidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Use propriedades de nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades de nuvem nunca são enviadas a um dispositivo. Por exemplo, você pode usar propriedades de nuvem para armazenar o nome do cliente que instalou o dispositivo ou a data do último serviço do dispositivo.

A seguinte tabela mostra as definições de configuração para uma propriedade de nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade de nuvem usado em dashboards e formulários. |
| name | O nome da propriedade de nuvem. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
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

1. **Comandos** fornecerá uma exibição com os comandos do dispositivo e permitirão a expedição deles pelo operador ao dispositivo.
1. **Visão geral** oferecerá uma exibição com a telemetria do dispositivo, exibindo gráficos e métricas.
1. **Sobre** fornecerá uma exibição com informações sobre o dispositivo, exibindo as propriedades dele.

Depois de selecionar **Gerar exibições padrão**, você verá que elas foram adicionadas automaticamente à seção **Exibições** do modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar dashboards

Adicione dashboards a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter vários dashboards para um modelo de dispositivo.

Para adicionar um painel a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Em seguida, escolha **Visualizando o Dispositivo**.
1. Insira um nome para o painel em **Nome do Painel**.
1. Adicione blocos ao painel na lista de blocos estáticos, de propriedade, de propriedade de nuvem, de telemetria e de comando. Arraste e solte os blocos que deseja adicionar ao painel.
1. Para plotar vários valores de telemetria em um só bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **Combinar**.
1. Configure cada bloco adicionado para personalizar como ele exibirá os dados selecionando o ícone de engrenagem ou o botão **Alterar configuração** no bloco do gráfico.
1. Organize e redimensione os blocos no painel.
1. Salve as alterações.

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

Depois que você publicar um modelo de dispositivo, um operador poderá acessar a página **Dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar modificando e salvando o modelo de dispositivo enquanto estiver fazendo alterações; no entanto, quando desejar efetuar push dessas alterações para serem vistas pelo operador na página **Dispositivos**, você precisará selecionar **Publicar** todas as vezes.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a configurar um modelo de dispositivo no aplicativo Central do IoT do Azure, é possível:

> [!div class="nextstepaction"]
> [Criar uma nova versão de modelo de dispositivo](howto-version-device-template.md)
> [conectar um dispositivo MXChip IOT devkit ao aplicativo de IOT central do Azure](howto-connect-devkit.md)
