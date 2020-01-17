---
title: Escolher o tipo de preço certo | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os tipos de preço oferecidos pelo Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155750"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha a camada de preços certa no Azure Mapas

O mapas do Azure oferece dois tipos de preço, S0 e S1. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para ajudar a escolher a melhor camada de preços, pergunte o seguinte a si mesmo.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?
A camada de preços S0 é ideal para você se as APIs geoespaciais principais atendem aos seus requisitos de serviço. Se quiser recursos mais avançados para seu aplicativo, considere a camada de preços S1. Exemplo de recursos avançados: imagens híbridas, como obter intervalo de rotas e geocodificação de lote. A tabela de **capacidades de tipo de preço** pode ajudá-lo a escolher um tipo de preço mais adequado para seu aplicativo.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte? 
As camadas de preços S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. O tipo de preço S0 lida com até **50 consultas por segundo**, enquanto a camada S1 lida com **mais de 50 consultas por segundo**.

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Capacidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização de mapa                              | ✓                   | ✓       |
| Imagens satélite                       |                     | ✓        |
| Pesquisa                                  | ✓                    | ✓        |
| Pesquisa de Lote                            |                     | ✓        |
| Rota                                   | ✓                    |✓        |
| Roteamento de Lote                            |                    | ✓        |
| Roteamento de Matriz                          |                     | ✓        |
| Intervalo de Rota (Isócronos)                |                     | ✓        |
| Tráfego                                |✓                    |✓        |
| Fuso horário                               |✓                    |✓        |
| Localização geográfica (versão prévia)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Delimitação geográfica                                |                    |✓        |
| Dados do Azure Maps (versão prévia)                |                     | ✓        |
| Mobilidade (visualização)                       |                     | ✓        |
| Clima (visualização)                        |✓                    |✓        |

Vale a pena considerar esses pontos de dados adicionais:
* Que tipo de empresa você tem?
* Quão crítico é seu aplicativo?

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

Consulte a tabela com **Clientes da camada de preços** para obter uma noção melhor das camadas de preços S1 e S0. Para saber mais, consulte [Preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

| tipo de preço  |     Clientes de destino                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 funciona para aplicativos em todos os estágios de produção: desde desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos. No entanto, essa camada foi projetada para desenvolvimento de pequena escala ou para clientes com poucos usuários simultâneos, ou ambos. <p>|
| S1            |    <p>A camada de preços S1 serve clientes que precisam oferecer suporte a uma empresa de grande porte, aplicativos essenciais ou altos volumes de usuários simultâneos. Vale também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como exibir e alterar a camada de preços:

> [!div class="nextstepaction"] 
> [Gerenciar uma camada de preços](how-to-manage-pricing-tier.md)
