---
title: Gerenciamento de versão de API para .NET e REST
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137281"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se alguns aspectos da API tiverem sido alterados de uma forma que interrompa a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

A mesma regra se aplica às atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK será liberada somente no caso de alterações que interrompam a compatibilidade com versões anteriores. Para atualizações contínuas de recursos, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!NOTE]
> Sua instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

## <a name="snapshot-of-current-versions"></a>Instantâneo das versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação para o Azure Pesquisa Cognitiva.


| Interfaces | Versão principal mais recente | Status |
| --- | --- | --- |
| [SDK .NET](https://aka.ms/search-sdk) |9.0 |Disponibilidade geral, lançada em maio de 2019 |
| [Preview do SDK do .NET](https://aka.ms/search-sdk-preview) |8,0-visualização |Versão prévia, lançada em abril de 2019 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Disponível |
| [API REST do serviço 2019-05-06-Preview](search-api-preview.md) |2019-05-06-visualização |Visualização |
| [SDK do Gerenciamento do .NET](https://aka.ms/search-mgmt-sdk) |3.0 |Disponível |
| [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Disponível |

Para as APIs REST, é necessário incluir a `api-version` em cada chamada. Usar `api-version` facilita o direcionamento de uma versão específica, como uma API de versão prévia. O seguinte exemplo ilustra como o parâmetro `api-version` é especificado:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso ocorre especificamente quando novas versões de API introduzem atributos ou operações que não são reconhecidos por versões anteriores. A combinação de versões de API pode trazer consequências indesejadas e deve ser evitada.
>
> A API REST do Serviço e a API REST de Gerenciamento têm controle de versão que não dependem entre si. Qualquer semelhança nos números de versão é uma coincidência.

APIs GA (ou Disponíveis) podem ser usadas na produção e estão sujeitas aos SLAs do Azure. Versões prévias têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável evitar APIs de versão prévia em aplicativos de produção.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Atualize para a versão mais recente da API REST até 15 de outubro de 2020
As seguintes versões da API REST do Azure Pesquisa Cognitiva serão desativadas e não terão mais suporte a partir de 15 de outubro de 2020: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**e **2015-02-28**. Além disso, as versões do SDK do .NET do Azure Pesquisa Cognitiva **com mais de 3.0.0-RC** também serão desativadas, pois destinam-se a uma dessas versões da API REST. Após essa data, os aplicativos que usam qualquer uma das versões preteridas da API REST ou do SDK deixarão de funcionar e deverão ser atualizados. Assim como acontece com qualquer alteração desse tipo, estamos dando um aviso de 12 meses, portanto, você tem tempo adequado para se ajustar.  Para continuar usando o Azure Pesquisa Cognitiva, migre o código existente que tem como alvo a [API](search-api-migration.md) REST para a [api REST versão 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) ou mais recente ou o SDK do .net para a [versão 3,0](search-dotnet-sdk-migration.md) ou mais recente em 15 de outubro de 2020.  Se você tiver alguma dúvida sobre a atualização para a versão mais recente, envie um azuresearch_contact@microsoft.com email para 15 de maio de 2020 para garantir que você tenha tempo suficiente para atualizar seu código.

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões Prévias e Disponíveis para o Público em Geral
O Azure Pesquisa Cognitiva sempre libera recursos experimentais por meio da API REST primeiro e, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários sobre o design e a implementação de recursos. Por esse motivo, os recursos de versão prévia podem ser alterados ao longo do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso é diferente de recursos em uma versão de GA, que são estáveis e têm pouca probabilidade de serem alterados, com a exceção de aprimoramentos e pequenas correções de compatibilidade com versões anteriores. Além disso, os recursos de versão prévia nem sempre são liberados para GA.

Por esses motivos, não recomendamos escrever um código de produção que dependa de versões prévias. Caso esteja usando uma versão prévia mais antiga, é recomendável migrar para a versão GA (disponível).

Para o SDK do .NET: diretrizes para a migração de código podem ser encontradas em [Atualizar o SDK do .NET](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que o Azure Pesquisa Cognitiva está agora sob o SLA (contrato de nível de serviço). O SLA pode ser encontrado em [contratos de nível de serviço do Azure pesquisa cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
