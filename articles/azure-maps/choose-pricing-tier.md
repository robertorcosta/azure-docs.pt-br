---
title: Escolha o nível de preços certo | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os níveis de preços oferecidos pelo Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335670"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha a camada de preços certa no Azure Mapas

O Azure Maps oferece dois níveis de preços, S0 e S1. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para escolher o nível de preços certo, faça-se as duas perguntas a seguir.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte? 
As camadas de preços S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. A camada de preços S0 lida com até **50 consultas por segundo**. Considerando que o nível S1 lida com **mais de 50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?
O nível de preços S0 é certo para você, se as APIs geoespaciais principais atenderem aos seus requisitos de serviço. Se quiser recursos mais avançados para seu aplicativo, considere a camada de preços S1. Os recursos avançados incluem: imagens aéreas e híbridas, obtendo alcance de rota e geocodificação em lote. Revise a tabela **de recursos do nível de preços** para selecionar o nível de preços mais adequado para sua aplicação.

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Recurso                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização do mapa                              | ✓                   | ✓       |
| Imagens de satélite                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Pesquisa em lote                            |                     | ✓        |
| Rota                                   | ✓                    |✓        |
| Roteamento em lote                            |                    | ✓        |
| Roteamento de Matriz                          |                     | ✓        |
| Faixa de Rota (Isochrones)                |                     | ✓        |
| Tráfego                                |✓                    |✓        |
| Fuso horário                               |✓                    |✓        |
| Geolocalização (Visualização)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Delimitação geográfica                                |                    |✓        |
| Dados do Azure Maps (Visualização)                |                     | ✓        |
| Mobilidade (Visualização)                       |                     | ✓        |
| Tempo (Prévia)                        |✓                    |✓        |

Considere estes pontos adicionais:
* Que tipo de empresa você tem?
* Quão crítica é sua aplicação?

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

Consulte a tabela com **Clientes da camada de preços** para obter uma noção melhor das camadas de preços S1 e S0. Para saber mais, consulte [Preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Tipo de preço  |     Clientes de destino                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O nível de precificação S0 funciona para aplicações em todas as etapas de produção: desde o desenvolvimento da prova de conceito e os testes em estágio inicial até a produção e implantação de aplicativos. No entanto, este nível foi projetado para o desenvolvimento em pequena escala, ou clientes com usuários simultâneos baixos, ou ambos. <p>|
| S1            |    <p>O nível de preços S1 é para clientes com aplicativos corporativos de grande escala, aplicativos de missão crítica ou grandes volumes de usuários simultâneos. Vale também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como exibir e alterar a camada de preços:

> [!div class="nextstepaction"] 
> [Gerenciar uma camada de preços](how-to-manage-pricing-tier.md)
