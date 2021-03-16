---
author: baanders
description: arquivo de inclusão dos Gêmeos Digitais do Azure – instruções de modelo para o tutorial de linha de comando
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463694"
---
1. **Atualizar o número de versão**, de modo a indicar que você está fornecendo uma versão mais atualizada do modelo. Faça isso alterando o *1* no final do valor `@id` para *2*. Qualquer número maior que o da versão atual também funcionará.
1. **Editar uma propriedade**. Altere o nome da propriedade `Humidity` para *HumidityLevel* (ou algo diferente, se desejar. Se usar algo diferente de *HumidityLevel*, lembre-se do que você usou e continue usando o mesmo nome em vez de *HumidityLevel* ao longo do tutorial).
1. **Adicionar uma propriedade**. Abaixo da propriedade `HumidityLevel` que termina na linha 15, cole o código a seguir para adicionar uma propriedade `RoomName` à sala:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Adicionar uma relação**. Abaixo da propriedade `RoomName` que você acabou de adicionar, cole o código a seguir para adicionar a capacidade desse tipo de gêmeo de gerar relações do tipo *contains* com outros gêmeos:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Quando você terminar, o modelo atualizado deverá corresponder a:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Salve o arquivo antes de continuar.