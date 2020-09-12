---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para aplicativos Azure IoT Central | Microsoft Docs
description: Iterar modelos de dispositivo criando novas versões e sem afetar os dispositivos conectados em tempo real
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 2025b127a428afa478cfe839c7619df2d7d688d3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015952"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo de dispositivo

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Um modelo de dispositivo inclui um esquema que descreve como um dispositivo interage com IoT Central. Essas interações incluem telemetria, propriedades e comandos. O dispositivo e o aplicativo IoT Central dependem de um entendimento compartilhado desse esquema para trocar informações. Você só pode fazer alterações limitadas no esquema sem quebrar o contrato, é por isso que a maioria das alterações de esquema requer uma nova versão do modelo de dispositivo. O controle de versão do modelo de dispositivo permite que dispositivos mais antigos continuem com a versão do esquema que eles entendem, enquanto dispositivos mais recentes ou atualizados usam uma versão de esquema posterior.

O esquema em um modelo de dispositivo é definido no DCM (modelo de funcionalidade de dispositivo) e em suas interfaces. Os modelos de dispositivo incluem outras informações, como propriedades de nuvem, personalizações de exibição e exibições. Se você fizer alterações a essas partes do modelo de dispositivo que não definem como o dispositivo troca dados com IoT Central, você não precisará fazer a versão do modelo.

Você deve publicar qualquer alteração de modelo de dispositivo, quer ela precise ou não de uma atualização de versão, antes que um operador possa usá-la. IoT Central impede que você publique alterações significativas em um modelo de dispositivo sem primeiro o controle de versão do modelo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo [, consulte Configurar e gerenciar um modelo de dispositivo](howto-set-up-template.md)

## <a name="versioning-rules"></a>Regras de controle de versão

Esta seção resume as regras de controle de versão que se aplicam aos modelos de dispositivo. DCMs e interfaces têm números de versão. O trecho a seguir mostra o DCM para um dispositivo de sensor ambiental. O DCM tem duas interfaces: **DeviceInformation** e **EnvironmentalSensor**. Você pode ver os números de versão no final dos `@id` campos. Para exibir essas informações na interface do usuário do IoT Central, selecione **Exibir identidade** no editor de modelos de dispositivo.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Depois que um DCM é publicado, você não pode remover nenhuma interface, mesmo em uma nova versão do modelo de dispositivo.
* Depois que um DCM for publicado, você poderá adicionar uma interface se criar uma nova versão do modelo de dispositivo.
* Depois que um DCM for publicado, você poderá substituir uma interface por uma versão mais nova se criar uma nova versão do modelo de dispositivo. Por exemplo, se o modelo de dispositivo do sensor v1 usar a interface EnvironmentalSensor v1, você poderá criar um modelo de dispositivo do sensor v2 que usa a interface EnvironmentalSensor v2.
* Depois que uma interface é publicada, não é possível remover nenhum conteúdo da interface, mesmo em uma nova versão do modelo de dispositivo.
* Depois que uma interface for publicada, você poderá adicionar itens ao conteúdo de uma interface se criar uma nova versão da interface e do modelo de dispositivo. Os itens que você pode adicionar à interface incluem telemetria, propriedades e comandos.
* Depois que uma interface for publicada, você poderá fazer alterações de não-esquema em itens existentes na interface se criar uma nova versão do modelo de interface e dispositivo. Partes sem esquema de um item de interface incluem o nome de exibição e o tipo semântico. As partes de esquema de um item de interface que você não pode alterar são nome, tipo de recurso e esquema.

As seções a seguir orientam você por alguns exemplos de como modificar modelos de dispositivo no IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalizar o modelo de dispositivo sem controle de versão

Determinados elementos de seus recursos de dispositivo podem ser editados sem a necessidade de versão do modelo e das interfaces do dispositivo. Por exemplo, alguns desses campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentário e descrição. Para adicionar uma dessas personalizações:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você deseja personalizar.
1. Escolha a guia **Personalizar** .
1. Todos os recursos definidos no modelo de funcionalidade do dispositivo estão listados aqui. Você pode editar, salvar e usar todos esses campos sem a necessidade de versão do seu modelo de dispositivo. Se houver campos que você deseja editar que sejam somente leitura, você deverá fazer a versão do modelo de dispositivo para alterá-los. Selecione um campo que você deseja editar e insira um novo valor.
1. Clique em **Salvar**. Agora, esses valores substituem tudo o que foi salvo inicialmente em seu modelo de dispositivo e são usados em todo o aplicativo.

## <a name="version-a-device-template"></a>Versão um modelo de dispositivo

A criação de uma nova versão do modelo de dispositivo cria uma versão de rascunho do modelo em que o modelo de capacidade do dispositivo pode ser editado. Todas as interfaces publicadas permanecem publicadas até que sejam com controle de versão individual. Para modificar uma interface publicada, primeiro crie uma nova versão de modelo de dispositivo.

Somente versione o modelo de dispositivo quando você estiver tentando editar uma parte do modelo de funcionalidade do dispositivo que não pode ser editada na seção personalizações.

Para a versão de um modelo de dispositivo:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você está tentando executar na versão.
1. Clique no botão **versão** na parte superior da página e dê um novo nome ao modelo. IoT Central sugere um novo nome, que você pode editar.
1. Clique em **Criar**.
1. Agora seu modelo de dispositivo está no modo de rascunho. Você pode ver que suas interfaces ainda estão bloqueadas. Versão todas as interfaces que você deseja modificar.

## <a name="version-an-interface"></a>Versão de uma interface

O controle de versão de uma interface permite que você adicione, atualize e remova os recursos dentro da interface que você já criou.

Para a versão de uma interface:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você tem em um modo de rascunho.
1. Selecione a interface que está no modo publicado que você deseja para a versão e edição.
1. Clique no botão **versão** na parte superior da página da interface.
1. Clique em **Criar**.
1. Agora sua interface está no modo de rascunho. Você pode adicionar ou editar recursos à sua interface sem interromper as exibições e visualizações existentes.

## <a name="migrate-a-device-across-versions"></a>Migrar um dispositivo entre versões

É possível criar várias versões do modelo de dispositivo. Ao longo do tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Vá para a página de **Device Explorer** .
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **migrar**.
1. Selecione o modelo de dispositivo com o número de versão para o qual você deseja migrar o dispositivo e escolha **migrar**.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Próximas etapas

Se você for um operador ou um construtor de soluções, uma próxima etapa sugerida é aprender a [gerenciar seus dispositivos](./howto-manage-devices.md).

Se você for um desenvolvedor de dispositivos, uma próxima etapa sugerida será ler sobre [Azure IOT Edge dispositivos e IOT central do Azure](./concepts-iot-edge.md).
