---
title: Solucionar problemas com os módulos de áudio e fala do Azure Percept
description: Obtenha dicas de solução para alguns dos problemas mais comuns encontrados durante a experiência de integração
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678996"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Solução de problemas do módulo de áudio e fala do Azure Percept

Use as diretrizes abaixo para solucionar problemas do aplicativo assistente de voz.

## <a name="collecting-speech-module-logs"></a>Coletando logs do módulo de fala

Para executar esses comandos, [Conecte-se ao ponto de acesso do Azure PERCEPT DK Wi-Fi e conecte-se ao kit de desenvolvimento por SSH](./how-to-ssh-into-percept-dk.md) e insira os comandos no terminal SSH.

```console
 iotedge logs azureearspeechclientmodule
```

Para redirecionar qualquer saída para um arquivo. txt para análise adicional, use a seguinte sintaxe:

```console
[command] > [file name].txt
```

Depois de redirecionar a saída para um arquivo. txt, copie o arquivo para o computador host por meio do SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[caminho do arquivo de host local] refere-se ao local no computador host para o qual você gostaria de copiar o arquivo. txt. [nome de usuário remoto] é o nome de usuário SSH escolhido durante a [experiência de instalação](./quickstart-percept-dk-set-up.md). Se você não configurou um logon SSH durante o OOBE, seu nome de usuário remoto é raiz.

## <a name="checking-runtime-status-of-the-speech-module"></a>Verificando o status do tempo de execução do módulo de fala

Verifique se o status de tempo de execução de **azureearspeechclientmodule** é mostrado como **em execução**. Para localizar o status de tempo de execução dos seus módulos de dispositivo, abra o [portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e navegue até **todos os recursos**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Clique na guia **módulos** para ver o status de tempo de execução de todos os módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página de dispositivo do Edge no portal do Azure.":::

Se o status de tempo de execução de **azureearspeechclientmodule** não estiver listado como **em execução**, clique em **definir módulos**  ->  **azureearspeechclientmodule**. Na página **configurações do módulo** , defina **o status desejado** como **em execução** e clique em **Atualizar**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Tela definir módulos no portal do Azure.":::

## <a name="understanding-ear-som-led-indicators"></a>Entendendo indicadores de LED de SoM Ear

Você pode usar indicadores de LED para entender em qual estado seu dispositivo está. Normalmente, leva cerca de 2 minutos para que o módulo seja inicializado completamente após a *ativação*. Como ele percorre as etapas de inicialização, você verá:

1. 1 luz verde esquerda-o dispositivo está ligado. 
2. 1 luz verde esquerda e LED central piscando em verde-a autenticação está em andamento. 
3. Todos os três LEDs serão alterados para azul quando o dispositivo for autenticado e estiver pronto para uso.

|Estado do LED                  |Status de SoM Ear            |
|----------------------------|---------------------------|
|1x verde (LED esquerdo)         |ligar |
|1x verde (LED esquerdo) <br> 1x piscando verde (LED central) |autenticação em andamento |
|3x                      |Inicialização concluída |
|azul-3x                     |Pronto para uso |
|azul piscando triplo            |palavra-chave reconhecida |
|azul de corrida em 3x              |processando |
|vermelho-3x                      |Muta |

## <a name="next-steps"></a>Próximas etapas

Consulte o [Guia de solução de problemas geral](./troubleshoot-dev-kit.md) para obter mais informações sobre como solucionar problemas do Azure Percept DK.