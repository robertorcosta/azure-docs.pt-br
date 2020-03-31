---
title: Instalar e usar o explorador Azure IoT | Microsoft Docs
description: Instale a ferramenta explorador Azure IoT e use-a para interagir com os dispositivos IoT Plug e Play Preview conectados ao meu hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159176"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instale e use o explorador Azure IoT

O explorador Azure IoT é uma ferramenta gráfica para interagir e testar seus dispositivos IoT Plug e Play Preview. Depois de instalar a ferramenta em sua máquina local, você pode usá-la para se conectar a um dispositivo. Você pode usar a ferramenta para visualizar a telemetria que o dispositivo está enviando, trabalhar com propriedades do dispositivo e comandos de chamada.

Este artigo mostra como:

- Instale e configure a ferramenta explorador Azure IoT.
- Use a ferramenta para interagir e testar seus dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a ferramenta explorador Azure IoT, você precisa:

- Um Hub IoT do Azure. Existem muitas maneiras de adicionar um hub de IoT à sua assinatura do Azure, como [criar um hub de IoT usando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da seqüência de conexão de hub IoT para executar a ferramenta explorador azure IoT. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registrado em seu hub de IoT. Você pode usar o seguinte comando Azure CLI para registrar um dispositivo. Certifique-se de `{YourIoTHubName}` `{YourDeviceID}` substituir os espaços reservados e espaços reservados com seus valores:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instale o explorador Azure IoT

Vá para [as versões do explorador Azure IoT](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para o lançamento mais recente. Baixe e instale a versão mais recente do aplicativo.

## <a name="use-azure-iot-explorer"></a>Use o explorador Azure IoT

Para um dispositivo, você pode conectar seu próprio dispositivo ou usar um de nossos dispositivos simulados de amostra. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) para executar a amostra do dispositivo simulado.

### <a name="connect-to-your-hub"></a>Conecte-se ao seu hub

A primeira vez que você executa o explorador De IoT do Azure, você é solicitado para a seqüência de conexões do seu hub IoT. Depois de adicionar a seqüência de conexões, selecione **Conectar**. Você pode usar as configurações da ferramenta para mudar para outro hub de IoT atualizando a seqüência de conexões.

A definição do modelo para um dispositivo IoT Plug and Play é armazenada no repositório público, em um repositório da empresa ou no dispositivo conectado. Por padrão, a ferramenta procura a definição do modelo no repositório de modelos públicos e no dispositivo conectado. Você pode adicionar e remover fontes ou configurar a prioridade das fontes em **Configurações:**

Para adicionar uma fonte:

1. Vá para **Configurações**.
1. Selecione **Novo** e escolha sua fonte.
1. Se você estiver adicionando o repositório do modelo da empresa, forneça a seqüência de conexões.

Para remover uma fonte:

1. Vá para **Configurações**.
1. Encontre a fonte que deseja remover.
1. Selecione **X** para removê-lo. Você não pode remover o repositório de modelos públicos porque as definições comuns de interface vêm deste repositório.

Alterar as prioridades de origem:

Você pode arrastar e soltar uma das fontes de definição do modelo para um ranking diferente na lista. Se houver um conflito, fontes de definição com rankings mais altos anulam fontes com rankings mais baixos.

### <a name="view-devices"></a>Exibir dispositivos

Depois que a ferramenta se conecta ao seu hub IoT, ela exibe a página de lista **dispositivos** que lista as identidades do dispositivo registradas no seu hub IoT. Você pode expandir qualquer entrada na lista para ver mais informações.

Na página da lista **dispositivos** você pode:

- Selecione **Adicionar** para registrar um novo dispositivo com seu hub. Em seguida, digite um id do dispositivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e habilitar a conexão ao seu hub.
- Selecione um dispositivo e, em seguida, **selecione Excluir** para excluir uma identidade do dispositivo. Revise os detalhes do dispositivo antes de concluir esta ação para ter certeza de que está excluindo a identidade certa do dispositivo.
- Consulta por `capabilityID` `interfaceID`e . Adicione o `capabilityID` `interfaceID` seu ou como parâmetro para consultar seus dispositivos.

## <a name="interact-with-a-device"></a>Interaja com um dispositivo

Na página de lista Dispositivos, selecione um valor na coluna **ID** do dispositivo para exibir a página de **detalhes** do dispositivo registrado. Para cada dispositivo há duas seções: **Dispositivo** e **Gêmeo Digital**.

### <a name="device"></a>Dispositivo

Esta seção inclui as guias **de identidade do dispositivo,** **dispositivo gêmeo,** **telemetria,** **método direto** e guias de **mensagens nuvem-para-dispositivo.**

- Você pode visualizar e atualizar as informações [de identidade](../iot-hub/iot-hub-devguide-identity-registry.md) do dispositivo na guia de identidade **do dispositivo.**
- Você pode acessar as informações duplas do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) na guia **Dispositivo Gêmeo.**
- Se um dispositivo estiver conectado e enviar ativamente dados, você poderá visualizar a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na guia **Telemetria.**
- Você pode chamar um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) no dispositivo na guia **Método Direto.**
- Você pode enviar uma [mensagem nuvem-para-dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) na guia **mensagens Nuvem-para-dispositivo.**

### <a name="digital-twin"></a>Gêmeo digital

Você pode usar a ferramenta para visualizar a instância digital do dispositivo. Para um dispositivo IoT Plug and Play, todas as interfaces associadas ao modelo de capacidade do dispositivo são exibidas nesta seção da ferramenta. Selecione uma interface para expandir seus [primitivos ioT plug and play correspondentes](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Interface

Na página **Interface,** você pode visualizar a definição JSON da interface.

#### <a name="properties"></a>Propriedades

Você pode visualizar as propriedades somente leitura definidas em uma interface na **página Propriedades Não graváveis.** Você pode atualizar as propriedades graváveis definidas em uma interface na página **propriedades graváveis:**

1. Vá para a página **de propriedades writable.**
1. Clique na propriedade que deseja atualizar.
1. Digite o novo valor para a propriedade.
1. Visualize a carga a ser enviada ao dispositivo.
1. Envie a mudança.

Depois de enviar uma alteração, você pode acompanhar o status da atualização: **sincronização,** **sucesso**ou **erro**. Quando a sincronização estiver concluída, você verá o novo valor de sua propriedade na coluna **Propriedade Relatada.** Se você navegar para outras páginas antes que o sincronizador seja concluído, a ferramenta ainda o notificará quando a atualização estiver concluída. Você também pode usar a central de notificação da ferramenta para ver o histórico de notificação.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, vá para a página **Comandos:**

1. Na lista de comandos, expanda o comando que deseja acionar.
1. Digite todos os valores necessários para o comando.
1. Visualize a carga a ser enviada ao dispositivo.
1. Envie o comando.

#### <a name="telemetry"></a>Telemetria

Para ver a telemetria da interface selecionada, acesse a página **de Telemetria.**

## <a name="next-steps"></a>Próximas etapas

Neste artigo de como fazer, você aprendeu como instalar e usar o explorador Azure IoT para interagir com seus dispositivos IoT Plug and Play. Um próximo passo sugerido é aprender como instalar e usar a [extensão Azure CLI](./howto-install-pnp-cli.md).
