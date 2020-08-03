---
title: Idioma de consulta
titleSuffix: Azure Digital Twins
description: Entenda os conceitos básicos da linguagem de Repositório de Consultas gêmeos do Azure digital.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 960fff073353375cd50b31bc7284134ca733f142
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488016"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para o gêmeos digital do Azure

Lembre-se de que o centro do Azure digital gêmeos é o [**gráfico de entrelaçamento**](concepts-twins-graph.md), construído de gêmeos e **relações** **digitais** . Esse grafo pode ser consultado para obter informações sobre o gêmeos digital e as relações que ele contém. Essas consultas são gravadas em uma linguagem de consulta do tipo SQL personalizada chamada **linguagem de repositório de consultas do Azure digital gêmeos**.

Para enviar uma consulta ao serviço de um aplicativo cliente, você usará a [**API de consulta**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)do gêmeos digital do Azure. Isso permite que os desenvolvedores gravem consultas e apliquem filtros para localizar conjuntos de gêmeos digitais no grafo de entrelaçamento e outras informações sobre o cenário do gêmeos digital do Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever consultas e ver exemplos de código de cliente em [*como: consultar o grafo de entrelaçamento*](how-to-query-graph.md).
