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
ms.openlocfilehash: 09209c21fe1b2b115c1ba6d6e00fcd0ee59a9393
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365420"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorar a conectividade do dispositivo usando a CLI do Azure

*Este tópico aplica-se a construtores e administradores.*

Use a extensão Azure CLI IoT para ver as mensagens que seus dispositivos estão enviando para a Central de IoT e observe as alterações no dispositivo gêmeo. Você pode usar esta ferramenta para depurar e observar a conectividade do dispositivo e diagnosticar problemas de mensagens de dispositivos que não chegam à nuvem ou dispositivos que não respondem a alterações duplas.

[Visite a referência de extensões azure CLI para obter mais detalhes](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

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

Agora que você aprendeu a usar o IoT Central Explorer, o próximo passo sugerido é explorar dispositivos [de gerenciamento IoT Central](howto-manage-devices.md).
