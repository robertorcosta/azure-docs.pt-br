---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550365"
---
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

O pacote principal das Âncoras Espaciais do Azure será baixado para a pasta em que você executou o comando.

Repita esta etapa para baixar o pacote para cada plataforma (Android/iOS/HoloLens) que você gostaria que fosse compatível com seu projeto.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<número_de_versão> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<número_de_versão>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<número_de_versão> |