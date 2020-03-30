---
title: Notas de lançamento do Seller Insights
description: Fornece informações sobre alterações no recurso do Seller Insights.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285394"
---
# <a name="seller-insights-release-notes"></a>Notas de versão do Seller Insights 

(Data de lançamento: 1 de março de 2019)

Este artigo fornece informações sobre as alterações no recurso do Seller Insight no [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Destaques de lançamento para 1º de março de 2019

* *Tendência do Cliente* adicionada ao Resumo
* *Os cinco principais clientes* no resumo refletem todas as assinaturas do Azure que um cliente tem
* *Tendência de uso normalizado & tendência de pedidos ativos* no resumo movido sumário movido ordens *mensais em um olhar*
* *Relatório de Reconciliação de Pagamentos* atualizado
* *Os cinco principais clientes* em pagamento refletem todas as assinaturas do Azure que um cliente tem
* *Relatório de uso* atualizado com ID do cliente
* *A posse do cliente* em pedidos & uso reflete todas as assinaturas do Azure que um cliente tem


(Data de lançamento: 28 de julho de 2018)

## <a name="release-highlights-for-july-28-2018"></a>Destaques de lançamento para 28 de julho de 2018


-   *Preços estimados* fornecem uma exibição dos encargos do cliente com implicações na conversão cambial.
-   *Pagamentos previstos* fornecem uma exibição anterior nos pagamentos potenciais.
-  *Identificadores de referência de uso* fornecem fidelidade de dados entre o uso do cliente e as ordens com pagamentos
-   *Uso em uma granulação diária* fornece mais granularidade e melhores insights sobre o uso do cliente.


### <a name="changes-to-data-structure-and-taxonomy"></a>Alterações na estrutura de dados e taxonomia

A tabela a seguir lista as métricas que foram adicionadas ou substancialmente alteradas com esta versão. 

| **Novo termo**                   |    **Definição**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preço (CC)                     | Preço de uma unidade de uso para um determinada SKU (na moeda do cliente).       |
| Custo estendido estimado (CC) | Custo estendido estimado para a quantidade de unidades de uso para um determinado SKU (na moeda do cliente). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Moeda do publicador (PC)        | Moeda preferida pelo publicador para o pagamento.                               |
| Preço estimado (PC)           | O preço estimado para uma unidade de uso para um dado SKU com base na conversão cambial na data de uso é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Custo estendido estimado (PC) | A taxa estendida estimada para a quantidade de unidades de uso para um determinado SKU com base na conversão cambial na data de uso é calculada (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento. |
| Pagamento estimado (PC)          | Pagamento estimado para a quantidade de unidades de uso para um determinado SKU com base na conversão cambial na data em que o uso é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Referência do uso                | O identificador para um ou mais dias de uso do cliente para determinada SKU associada a uma entrada no relatório de pagamento. |
|  |  |
