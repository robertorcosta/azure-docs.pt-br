---
title: Recursos FHIR com suporte no Azure – API do Azure para FHIR
description: Este artigo explica quais recursos da especificação FHIR são implementados na API do Azure para FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: bdf328222fef1f763776bd26c47f5cd4d65e487e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999998"
---
# <a name="features"></a>Recursos

A API do Azure para FHIR fornece uma implantação totalmente gerenciada do Microsoft FHIR Server para o Azure. O servidor é uma implementação do padrão [FHIR](https://hl7.org/fhir) . Este documento lista os principais recursos do servidor FHIR.

## <a name="fhir-version"></a>Versão do FHIR

Versão mais recente com suporte: `4.0.1`

As versões anteriores também têm suporte no momento incluem: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| read                           | Sim       | Sim       | Sim       |                                                     |
| vread                          | Sim       | Sim       | Sim       |                                                     |
| atualizar                         | Sim       | Sim       | Sim       |                                                     |
| atualizar com bloqueio otimista | Sim       | Sim       | Sim       |                                                     |
| atualizar (condicional)           | Sim       | Sim       | Sim       |                                                     |
| distribuído                          | Não        | Não        | Não        |                                                     |
| excluir                         | Sim       | Sim       | Sim       |                                                     |
| excluir (condicional)           | Não        | Não        | Não        |                                                     |
| create                         | Sim       | Sim       | Sim       | Suporte para POST/PUT                               |
| criar (condicional)           | Sim       | Sim       | Sim       |                                                     |
| pequisa                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa encadeada                 | Não        | Sim       | Não        |                                           |
| pesquisa encadeada inversa         | Não        | Não        | Não        |                                            |
| funcionalidades                   | Sim       | Sim       | Sim       |                                                     |
| lote                          | Sim       | Sim       | Sim       |                                                     |
| transaction                    | Não        | Sim       | Não        |                                                     |
| history                        | Sim       | Sim       | Sim       |                                                     |
| paginação                         | Parcial   | Parcial   | Parcial   | `self` e `next` têm suporte                     |
| intermediários                 | Não        | Não        | Não        |                                                     |

## <a name="search"></a>Search

Todos os tipos de parâmetro de pesquisa têm suporte. 

| Tipo de parâmetro de pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| Número                | Sim       | Sim       | Sim       |         |
| Data/Data e Hora         | Sim       | Sim       | Sim       |         |
| String                | Sim       | Sim       | Sim       |         |
| Token                 | Sim       | Sim       | Sim       |         |
| Referência             | Sim       | Sim       | Sim       |         |
| Composição             | Sim       | Sim       | Sim       |         |
| Quantidade              | Sim       | Sim       | Sim       |         |
| URI                   | Sim       | Sim       | Sim       |         |
| Especial               | Não        | Não        | Não        |         |


| Modificadores             | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sim       | Sim       | Sim       |         |
|`:exact`               | Sim       | Sim       | Sim       |         |
|`:contains`            | Sim       | Sim       | Sim       |         |
|`:text`                | Sim       | Sim       | Sim       |         |
|`:in` token          | Não        | Não        | Não        |         |
|`:below` token       | Não        | Não        | Não        |         |
|`:above` token       | Não        | Não        | Não        |         |
|`:not-in` token      | Não        | Não        | Não        |         |
|`:[type]` referência  | Não        | Não        | Não        |         |
|`:below` URI         | Sim       | Sim       | Sim       |         |
|`:not`                 | Não        | Não        | Não        |         |
|`:above` URI         | Não        | Não        | Não        | Problema [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Parâmetro de pesquisa comum | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sim       | Sim       | Sim       |         |
| `_lastUpdated`          | Sim       | Sim       | Sim       |         |
| `_tag`                  | Sim       | Sim       | Sim       |         |
| `_profile`              | Sim       | Sim       | Sim       |         |
| `_security`             | Sim       | Sim       | Sim       |         |
| `_text`                 | Não        | Não        | Não        |         |
| `_content`              | Não        | Não        | Não        |         |
| `_list`                 | Sim       | Sim       | Sim       |         |
| `_has`                  | Não        | Não        | Não        |         |
| `_type`                 | Sim       | Sim       | Sim       |         |
| `_query`                | Não        | Não        | Não        |         |

| Operações de pesquisa       | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Não        | Não        | Não        |         |
| `_sort`                 | Não        | Parcial   | Parcial        |   `_sort=_lastUpdated` é compatível       |
| `_score`                | Não        | Não        | Não        |         |
| `_count`                | Sim       | Sim       | Sim       |         |
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é compatível |
| `_include`              | Não        | Sim       | Não        |         |
| `_revinclude`           | Não        | Sim       | Não        | Os itens incluídos são limitados a 100. |
| `_contained`            | Não        | Não        | Não        |         |
| `_elements`             | Não        | Não        | Não        |         |

## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência conectável (consulte [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código-fonte aberto do servidor FHIR inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é um banco de dados multimodelo distribuído globalmente (API do SQL, API do MongoDB, etc.). Ele dá suporte a diferentes [níveis de consistência](../cosmos-db/consistency-levels.md). O modelo de implantação padrão configura o servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) em uma solicitação pela base da solicitação usando o `x-ms-consistency-level` cabeçalho de solicitação.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

O servidor FHIR usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para controle de acesso. Especificamente, o RBAC (controle de acesso baseado em função) é imposto, se o `FhirServer:Security:Enabled` parâmetro de configuração for definido como `true` , e todas as solicitações (exceto `/metadata` ) para o servidor FHIR devem ter o `Authorization` cabeçalho de solicitação definido como `Bearer <TOKEN>` . O token deve conter uma ou mais funções, conforme definido na `roles` declaração. Uma solicitação será permitida se o token contiver uma função que permita a ação especificada no recurso especificado.

Atualmente, as ações permitidas para uma determinada função são aplicadas *globalmente* na API.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu sobre os recursos de FHIR com suporte na API do Azure para FHIR. Em seguida, implante a API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)
