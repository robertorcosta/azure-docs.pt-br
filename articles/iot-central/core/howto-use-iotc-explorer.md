---
title: Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer
description: Monitore as mensagens do dispositivo e observe as alterações do dispositivo gêmeo por meio da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949606"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer

*Este tópico aplica-se a construtores e administradores.*

Use a CLI do IoT Central Explorer para ver as mensagens que os dispositivos estão enviando para o IoT Central e observe as alterações no dispositivo gêmeo do Hub IoT. É possível usar essa ferramenta de software livre para obter informações mais detalhadas sobre o estado da conectividade de dispositivo e diagnosticar problemas de mensagens do dispositivo que não alcançam a nuvem ou dispositivos que não estão respondendo a alterações do dispositivo gêmeo.

[Visite o repositório IOTC-Explorer no GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos

+ Node.js versão 8.x ou superior - https://nodejs.org
+ Um administrador do seu aplicativo deve gerar um token de acesso para você usar no IOTC-Explorer

## <a name="install-iotc-explorer"></a>Instalar o IOTC-Explorer

Para instalar, execute o comando a seguir na linha de comando:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, você precisa executar o comando install com `sudo` em ambientes semelhantes ao Unix.

## <a name="run-iotc-explorer"></a>Executar o IOTC-Explorer

As seções a seguir descrevem comandos e opções comuns que você pode usar ao executar o `iotc-explorer`. Para exibir o conjunto completo de comandos e opções, passe `--help` para `iotc-explorer` ou qualquer um de seus subcomandos.

### <a name="login"></a>Logon

Antes de começar, é necessário ter um administrador do aplicativo IoT Central para obter um token de acesso para você usar. O administrador executa as etapas a seguir:

1. Navegue até **Administração** e, em seguida, **acesse tokens**.
1. Selecione **gerar token**.
    ![Captura de tela da página de token de acesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Insira um nome de token, selecione **Avançar**e, em seguida, **copiar**.
    > [!NOTE]
    > O valor do token é mostrado apenas uma vez, portanto, ele deve ser copiado antes de fechar a caixa de diálogo. Depois de fechar a caixa de diálogo, ela nunca é mostrada novamente.

    ![Copiar captura de tela do token de acesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

Você pode usar o token para fazer logon na CLI da seguinte maneira:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se preferir não ter o token persistido no histórico do Shell, você poderá deixar o token fora e, em vez disso, fornecê-lo quando solicitado:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorar mensagens de dispositivo

É possível inspecionar as mensagens provenientes de um dispositivo específico ou de todos os dispositivos no aplicativo usando o comando `monitor-messages`. Esse comando inicia um observador que gera continuamente novas mensagens à medida que elas chegam:

Para inspecionar todos os dispositivos no aplicativo, execute o comando a seguir:

```cmd/sh
iotc-explorer monitor-messages
```

Saída:

![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.png)

Para assistir a um dispositivo específico, basta adicionar a ID do dispositivo ao final do comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Você também pode gerar um formato mais amigável adicionando a opção `--raw` ao comando:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter dispositivo gêmeo

É possível usar o comando `get-twin` para obter o conteúdo do dispositivo gêmeo de um dispositivo IoT Central. Para fazer isso, execute o comando a seguir:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Saída:

![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.png)

Tal como em `monitor-messages`, você pode obter uma saída amigável do computador, passando a opção `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a usar o IoT Central Explorer, a próxima etapa sugerida é explorar o [Gerenciamento de dispositivos IOT central](howto-manage-devices.md).
