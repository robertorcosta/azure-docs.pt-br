---
title: Interfaces comuns - IoT Plug and Play Preview | Microsoft Docs
description: Descrição de interfaces comuns para desenvolvedores de IoT Plug e Play
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c6ac90f917b9afc37b3a39d8da679fbcad091778
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234701"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play Preview interfaces comuns

Espera-se que todos os dispositivos IoT Plug and Play implementem algumas interfaces comuns. Interfaces comuns beneficiam soluções de IoT porque fornecem funcionalidade consistente. [A certificação](tutorial-build-device-certification.md) exige que seu dispositivo implemente várias interfaces comuns. Você pode recuperar definições comuns de interface do repositório de modelos públicos.

## <a name="summary-of-common-interfaces"></a>Resumo de interfaces comuns

| Nome | ID | Descrição | Implementado pelo Azure IoT SDK | Deve ser declarado em modelo de capacidade |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informações sobre modelos | urn:azureiot:ModelDiscovery:ModelInformation:1 | Para que os dispositivos declarem o ID do modelo de capacidade e as interfaces. Necessário para todos os dispositivos IoT Plug and Play. | Sim | Não |
| Informações do SDK do cliente gêmeo digital | urn:azureiot:Client:SDKInformation:1 | Cliente SDK para conectar o dispositivo com o Azure. Necessário para [certificação](tutorial-build-device-certification.md) | Sim | Não |
| Informações do dispositivo | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informações de hardware e sistema operacional sobre o dispositivo. Necessário para [certificação](tutorial-build-device-certification.md) | Não | Sim |
| Definição do modelo | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Para que os dispositivos declarem a definição completa para seu modelo de capacidade e interfaces. Deve ser implementado quando as definições do modelo não estão hospedadas em um repositório de modelos. | Não | Sim |
| Gêmeo Digital | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Para desenvolvedores de soluções para recuperar o iD do modelo de capacidade e iDs de interface para um gêmeo digital. Esta interface não é declarada ou implementada por um dispositivo IoT Plug and Play. | Não | Não |

- Implementado pelo Azure IoT SDK - Se o Azure IoT SDK implementa os recursos declarados nas interfaces. Os dispositivos IoT Plug and Play que usam o Azure IoT SDK não precisam implementar essa interface.
- Deve ser declarado no modelo de capacidade - Se 'sim', esta interface deve ser declarada dentro da `"implements":` seção do modelo de capacidade do dispositivo para este dispositivo IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperar definições de interface do repositório público

### <a name="cli"></a>CLI

Você pode usar a extensão Azure IoT para Azure CLI para recuperar as interfaces comuns do repositório de modelos públicos.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Código VS

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Digite **Plug and Play** e selecione o comando **IoT Plug and Play: Open Model Repository.** Escolha **o repositório público**. O repositório de modelos público é aberto no VS Code.

1. No repositório de modelo público, digite o nome da interface no campo de pesquisa.

1. Para criar uma cópia local da interface, selecione-a nos resultados da pesquisa e selecione **Download**.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre interfaces comuns, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gêmeos digitais (DTDL)](https://aka.ms/DTDL)
- [SDK do Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
