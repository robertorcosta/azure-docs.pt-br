---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001985"
---
## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

Crie uma pasta chamada `environmental-sensor` no computador local.

Baixe o arquivo JSON [Modelo de capacidade do sensor ambiental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) e salve-o na pasta `environmental-sensor`.

Use um editor de texto para substituir as duas instâncias de `{YOUR_COMPANY_NAME_HERE}` pelo nome da sua empresa no arquivo `EnvironmentalSensorInline.capabilitymodel.json` baixado. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado.

No aplicativo do Azure IoT Central, crie um modelo de dispositivo chamado *Sensor ambiental* importando o arquivo de modelo de funcionalidade do dispositivo `EnvironmentalSensorInline.capabilitymodel.json`:

![Modelo de dispositivo com o modelo de funcionalidade do dispositivo importado](./media/iot-central-add-environmental-sensor/device-template.png)

O modelo de funcionalidade do dispositivo inclui duas interfaces: a interface padrão **Informações do Dispositivo** e a interface personalizada **Sensor Ambiental**. A interface **Sensor Ambiental** define as seguintes funcionalidades:

| Type | Nome de exibição | Descrição |
| ---- | ------------ | ----------- |
| Propriedade | Estado do dispositivo     | O estado do dispositivo. Dois estados online/offline estão disponíveis. |
| Propriedade (gravável) | Nome do Cliente    | O nome do cliente que está operando o dispositivo no momento. |
| Propriedade (gravável) | Nível de brilho | O nível de brilho da luz no dispositivo. Pode ser especificado como 1 (alto), 2 (médio) ou 3 (baixo). |
| Telemetria | Temperatura | Temperatura atual detectada pelo dispositivo. |
| Telemetria | Umidade    | Umidade atual detectada pelo dispositivo. |
| Comando | blink          | Comece fazendo piscar o LED no dispositivo para um intervalo de tempo especificado. |
| Comando | turnon         | Ligue o LED no dispositivo. |
| Comando | turnoff        | Desligue o LED no dispositivo. |
| Comando | rundiagnostics | Esse comando assíncrono inicia uma execução de diagnóstico no dispositivo. |

Para personalizar como a propriedade **Estado do Dispositivo** é exibida no aplicativo do IoT Central, selecione **Personalizar** no modelo de dispositivo. Expanda a entrada **Estado do Dispositivo**, insira _Online_ como o **Nome verdadeiro** e _Offline_ como o **Nome falso**. Em seguida, salve as alterações:

![Personalizar o modelo de dispositivo](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Criar exibições

As exibições permitem que você interaja com os dispositivos conectados ao seu aplicativo do IoT Central. Por exemplo, você pode ter exibições que mostrem a telemetria, exibições que mostrem as propriedades e exibições que permitam editar propriedades graváveis e de nuvem. As exibições fazem parte de um modelo de dispositivo.

Para adicionar algumas exibições padrão ao modelo de dispositivo **Sensor ambiental**, navegue até o modelo de dispositivo, selecione **Exibições** e o bloco **Gerar Exibições Padrão**. Verifique se as opções **Visão Geral** e **Sobre** estão **Ativadas** e, em seguida, selecione **Gerar exibições de painel padrão**. Agora você tem duas exibições padrão definidas no modelo.

A interface **Sensor Ambiental** inclui duas propriedades graváveis: **Nome do Cliente** e **Nível de Brilho**. Para criar uma exibição, use para editar estas propriedades:

1. Selecione **Exibições** e, em seguida, o bloco **Editando dados do dispositivo e da nuvem**.

1. Insira _Propriedades_ como o nome do formulário.

1. Selecione as propriedades **Nível de Brilho** e **Nome do Cliente**. Em seguida, selecione **Adicionar seção**.

1. Salve suas alterações.

![Adicionar uma exibição para habilitar a edição de propriedade](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publicar o modelo

Antes de adicionar um dispositivo que usa o modelo de dispositivo do **Sensor ambiental**, você deve publicá-lo.

No modelo de dispositivo, selecione **Publicar**. No painel **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

Para verificar se o modelo está pronto para uso, navegue até a página **Dispositivos** no aplicativo do IoT Central. A seção **Dispositivos** mostra uma lista dos dispositivos publicados no aplicativo:

![Modelos publicados na página dos dispositivos](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo do Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo criado na seção anterior:

1. Na página **Dispositivos**, selecione o modelo de dispositivo **Sensor ambiental**.

1. Selecione **+ Novo**.

1. Na caixa de diálogo **Criar um dispositivo**, verifique se o **Sensor Ambiental** é o tipo de modelo e se **Simular este dispositivo?** está definido como **Não**.

1. Em seguida, selecione **Criar**.

Clique no nome do dispositivo e, em seguida, selecione **Conectar**. Anote as informações de conexão do dispositivo na página **Conexão do Dispositivo**: **Escopo da ID**, **Identificação do Dispositivo** e **Chave primária**. Você precisará desses valores ao criar o código do dispositivo:

![Informações de conexão do dispositivo](./media/iot-central-add-environmental-sensor/device-connection.png)
