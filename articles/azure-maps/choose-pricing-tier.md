---
title: Escolher o tipo de preço certo | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os tipos de preço oferecidos pelo Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335670"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha a camada de preços certa no Azure Mapas

O mapas do Azure oferece dois tipos de preço, S0 e S1. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para escolher o tipo de preço certo, faça a si mesmo as duas perguntas a seguir.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte? 
As camadas de preços S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. A camada de preços S0 lida com até **50 consultas por segundo**. Enquanto a camada S1 lida com **mais de 50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?
O tipo de preço S0 é ideal para você, se as APIs geoespaciais principais atenderem aos seus requisitos de serviço. Se quiser recursos mais avançados para seu aplicativo, considere a camada de preços S1. Os recursos avançados incluem: imagens aéreas e híbridas, obter intervalo de rotas e geocodificação em lote. Examine a tabela de **capacidades do tipo de preço** para selecionar o tipo de preço mais adequado para seu aplicativo.

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Recurso                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização de mapa                              | ✓                   | ✓       |
| Imagens satélite                       |                     | ✓        |
| Pesquisar                                  | ✓                    | ✓        |
| Pesquisa em lotes                            |                     | ✓        |
| Rota                                   | ✓                    |✓        |
| Roteamento do lote                            |                    | ✓        |
| Roteamento de matriz                          |                     | ✓        |
| Intervalo de rotas (isócronas)                |                     | ✓        |
| Tráfego                                |✓                    |✓        |
| Fuso horário                               |✓                    |✓        |
| Localização geográfica (versão prévia)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Delimitação geográfica                                |                    |✓        |
| Dados do Azure Maps (versão prévia)                |                     | ✓        |
| Mobilidade (visualização)                       |                     | ✓        |
| Clima (visualização)                        |✓                    |✓        |

Considere estes pontos adicionais:
* Que tipo de empresa você tem?
* Quão crítico é seu aplicativo?

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

Consulte a tabela com **Clientes da camada de preços** para obter uma noção melhor das camadas de preços S1 e S0. Para saber mais, consulte [Preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Tipo de preço  |     Clientes de destino                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 funciona para aplicativos em todos os estágios de produção: desde desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos. No entanto, essa camada foi projetada para desenvolvimento de pequena escala ou para clientes com poucos usuários simultâneos, ou ambos. <p>|
| S1            |    <p>O tipo de preço S1 é para clientes com aplicativos empresariais de grande escala, aplicativos de missão crítica ou grandes volumes de usuários simultâneos. Vale também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como exibir e alterar a camada de preços:

> [!div class="nextstepaction"] 
> [Gerenciar uma camada de preços](how-to-manage-pricing-tier.md)
