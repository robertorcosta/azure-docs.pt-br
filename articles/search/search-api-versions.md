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
ms.openlocfilehash: 03dbb679c25ea692d2c52f80b9493889e367823d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112155"
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
| [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível |

Para as APIs REST, é necessário incluir a `api-version` em cada chamada. Usar `api-version` facilita o direcionamento de uma versão específica, como uma API de versão prévia. O seguinte exemplo ilustra como o parâmetro `api-version` é especificado:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso ocorre especificamente quando novas versões de API introduzem atributos ou operações que não são reconhecidos por versões anteriores. A combinação de versões de API pode trazer consequências indesejadas e deve ser evitada.
>
> A API REST do Serviço e a API REST de Gerenciamento têm controle de versão que não dependem entre si. Qualquer semelhança nos números de versão é uma coincidência.

APIs GA (ou Disponíveis) podem ser usadas na produção e estão sujeitas aos SLAs do Azure. Versões prévias têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável evitar APIs de versão prévia em aplicativos de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões Prévias e Disponíveis para o Público em Geral
O Azure Pesquisa Cognitiva sempre libera recursos experimentais por meio da API REST primeiro e, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários sobre o design e a implementação de recursos. Por esse motivo, os recursos de versão prévia podem ser alterados ao longo do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso é diferente de recursos em uma versão de GA, que são estáveis e têm pouca probabilidade de serem alterados, com a exceção de aprimoramentos e pequenas correções de compatibilidade com versões anteriores. Além disso, os recursos de versão prévia nem sempre são liberados para GA.

Por esses motivos, não recomendamos escrever um código de produção que dependa de versões prévias. Caso esteja usando uma versão prévia mais antiga, é recomendável migrar para a versão GA (disponível).

Para o SDK do .NET: diretrizes para a migração de código podem ser encontradas em [Atualizar o SDK do .NET](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que o Azure Pesquisa Cognitiva está agora sob o SLA (contrato de nível de serviço). O SLA pode ser encontrado em [contratos de nível de serviço do Azure pesquisa cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
