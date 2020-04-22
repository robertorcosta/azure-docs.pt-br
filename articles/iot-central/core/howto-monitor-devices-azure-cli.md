---
title: Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer
description: Monitore as mensagens do dispositivo e observe as alterações do dispositivo gêmeo por meio da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756674"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorar a conectividade do dispositivo usando a CLI do Azure

*Este tópico se aplica a desenvolvedores de dispositivos e construtores de soluções.*

Use a extensão Azure CLI IoT para ver as mensagens que seus dispositivos estão enviando para a Central de IoT e observe as alterações no dispositivo gêmeo. Você pode usar esta ferramenta para depurar e observar a conectividade do dispositivo e diagnosticar problemas de mensagens de dispositivos que não chegam à nuvem ou dispositivos que não respondem a alterações duplas.

[Visite a referência de extensões azure CLI para obter mais detalhes](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Pré-requisitos

+ Acliazure CLI instalado e é versão 2.0.7 ou superior. Verifique a versão do seu Azure CLI executando `az --version`. Saiba como instalar e atualizar nos [docs da Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Uma conta de trabalho ou escola no Azure, adicionada como um usuário em um aplicativo IoT Central.

## <a name="install-the-iot-central-extension"></a>Instale a extensão IoT Central

Para instalar, execute o comando a seguir na linha de comando:

```azurecli
az extension add --name azure-iot
```

Verifique a versão da extensão executando:

```azurecli
az --version
```

Você deve ver que a extensão azure-iot é 0.8.1 ou superior. Se não for, corra:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Usar a extensão

As seções a seguir descrevem comandos e opções comuns que você pode usar ao executar `az iot central`. Para ver o conjunto completo de `--help` comandos e opções, passe para `az iot central` ou qualquer um de seus subcomandos.

### <a name="login"></a>Logon

Comece assinando na CLI do Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obtenha o ID de aplicativo do seu aplicativo IoT Central
Em **Configurações de administração/aplicativo,** copie o **ID do aplicativo**. Você usa esse valor em etapas posteriores.

### <a name="monitor-messages"></a>Monitorar mensagens
Monitore as mensagens que estão sendo enviadas para o seu aplicativo IoT Central a partir de seus dispositivos. A saída inclui todos os cabeçalhos e anotações.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Exibir propriedades do dispositivo
Exibir as propriedades atuais do dispositivo de leitura e leitura/gravação para um determinado dispositivo.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre [a conectividade do dispositivo no Azure IoT Central](./concepts-get-connected.md).
