---
title: Controle de simultaneidade | Azure Marketplace
description: Estratégias de controle de simultaneidade para as APIs de publicação do Portal do Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256392"
---
# <a name="concurrency-control"></a>Controle de simultaneidade

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que seu código continue a funcionar após a migração para o Partner Center.

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
