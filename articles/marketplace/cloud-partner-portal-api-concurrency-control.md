---
title: Controle de simultaneidade-Azure Marketplace
description: Estratégias de controle de simultaneidade para as APIs de publicação do Portal do Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: dec18daea2f4f257e3bb21ee0477e3629b2e630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515846"
---
# <a name="concurrency-control"></a>Controle de simultaneidade

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue a funcionar após a migração para o Partner Center.

Cada chamada às APIs de publicação do Portal do Cloud Partner deve especificar explicitamente qual estratégia de controle de simultaneidade usar. A falha em fornecer o cabeçalho **If-Match** resultará em uma resposta de erro HTTP 400. Duas estratégias para controle de simultaneidade são ofertadas.

-   **Otimista** - O cliente que executa a atualização verifica se os dados foram alterados desde a última vez que leu os dados.
-   **Último vence** - O cliente atualiza os dados diretamente, independentemente de outro aplicativo tê-lo modificado desde o último horário de leitura.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho de simultaneidade otimista
-------------------------------

É recomendável usar a estratégia de simultaneidade otimista com o fluxo de trabalho a seguir para garantir que nenhuma edição inesperada seja feita nos recursos.

1.  Recuperar uma entidade usando as APIs. A resposta inclui um valor de ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  No momento da atualização, inclua esse mesmo valor de ETag no cabeçalho de solicitação **If-Match**.
3.  A API compara o valor de ETag recebido na solicitação com o valor de ETag atual da entidade em uma transação atômica.
    *   Se os valores de ETag forem diferentes, a API retornará uma resposta HTTP `412 Precondition Failed`. Esse erro indica que outro processo atualizou a entidade desde que o cliente a recuperou pela última vez ou que o valor de ETag especificado na solicitação está incorreto.
    *  Se os valores de ETag forem iguais ou o cabeçalho **If-Match** header contiver o caractere asterisco curinga (`*`), a API executará a operação solicitada. A operação da API também atualiza o valor de ETag armazenado da entidade.


> [!NOTE]
> Especificar o caractere curinga (*) no cabeçalho **If-Match** resulta na API usando a estratégia de simultaneidade Último vence. Nesse caso, a comparação de ETag não ocorre e o recurso é atualizado sem nenhuma verificação. 
