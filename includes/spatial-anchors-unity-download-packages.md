---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719932"
---
Para baixar os pacotes exigidos, será necessário ter o <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> instalado.

Execute o seguinte comando substituindo `<version_number>` pela versão das Âncoras Espaciais do Azure que você deseja baixar na pasta atual:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Para listar as versões disponíveis do pacote das Âncoras Espaciais do Azure, execute o seguinte:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> O SDK 2.7.0 do ASA é a versão mínima compatível. Se você está usando o Unity 2020, o SDK 2.9.0 do ASA é a versão mínima compatível.

O pacote principal das Âncoras Espaciais do Azure será baixado para a pasta em que você executou o comando.

Repita esta etapa para baixar o pacote para cada plataforma (Android/iOS/HoloLens) que você gostaria que fosse compatível com seu projeto.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<número_de_versão> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<número_de_versão>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<número_de_versão> |