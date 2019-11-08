---
title: Notas de versão do vendedor insights
description: Fornece informações sobre alterações no recurso do Seller Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808953"
---
# <a name="seller-insights-release-notes"></a>Notas de versão do Seller Insights 

(Data de lançamento: 1º de março de 2019)

Este artigo fornece informações sobre as alterações no recurso do Seller Insight no [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Destaques da versão de 1º de março de 2019

* *Tendência do cliente* adicionada ao Resumo
* Os *cinco principais clientes* em resumo refletem todas as assinaturas do Azure que um cliente tem
* *Tendência de uso normalizado & tendência de pedidos ativos* em resumo movido sob *pedidos mensais em um relance*
* *Relatório de reconciliação de pagamento* atualizado
* Os *cinco principais clientes* no pagamento refletem todas as assinaturas do Azure que um cliente tem
* *Relatório de uso* atualizado com a ID do cliente
* A *gestão do cliente* em pedidos & uso reflete todas as assinaturas do Azure que um cliente tem


(Data de lançamento: 28 de julho de 2018)

## <a name="release-highlights-for-july-28-2018"></a>Destaques da versão de 28 de julho de 2018


-   *Preços estimados* fornecem uma exibição dos encargos do cliente com implicações na conversão cambial.
-   *Pagamentos previstos* fornecem uma exibição anterior nos pagamentos potenciais.
-  *Identificadores de referência de uso* fornecem fidelidade de dados entre o uso do cliente e as ordens com pagamentos
-   *Uso em uma granulação diária* fornece mais granularidade e melhores insights sobre o uso do cliente.


### <a name="changes-to-data-structure-and-taxonomy"></a>Alterações na estrutura de dados e taxonomia

A tabela a seguir lista as métricas que foram adicionadas ou substancialmente alteradas com esta versão. 

| **Novo termo**                   |    **Definição**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preço (CC)                     | Preço de uma unidade de uso para um determinada SKU (na moeda do cliente).       |
| Custo estendido estimado (CC) | Encargo estendido estimado para a quantidade de unidades de uso de um determinado SKU (na moeda do cliente). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| PC (Moeda do editor)        | Moeda preferida pelo publicador para o pagamento.                               |
| Preço estimado (PC)           | O preço estimado para uma unidade de uso para uma determinada SKU com base na conversão de troca estrangeira no uso da data é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Custo estendido estimado (PC) | O encargo estendido estimado para a quantidade de unidades de uso de uma determinada SKU com base na conversão de troca de dados no uso da data é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento. |
| Pagamento estimado (PC)          | Pagamento estimado para a quantidade de unidades de uso de uma determinada SKU com base na conversão de troca estrangeira na data em que o uso é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Referência do uso                | O identificador para um ou mais dias de uso do cliente para determinada SKU associada a uma entrada no relatório de pagamento. |
|  |  |
