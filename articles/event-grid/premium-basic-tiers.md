---
title: Níveis Premium e Básico da Grade de Eventos do Azure
description: Este artigo descreve a diferença entre os níveis Premium e Básico da Grade de Eventos do Azure e quando usar cada um deles
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300330"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Níveis Premium e Básico da Grade de Eventos do Azure
A Grade de Eventos do Azure tem dois níveis: **Premium** e **Básico**. O nível básico usa o preço por consumo ou pago conforme o uso. Ele fornece todas as ferramentas de pub/sub básicas de que você precisa para usar a Grade de Eventos para modelos de programação orientados a eventos. O nível Premium leva isso um passo além com os recursos de segurança destinados a empresas. O nível Premium está no início da **visualização** com muitos de seus recursos ainda em desenvolvimento.

## <a name="overview"></a>Visão geral
Todos os tópicos e domínios personalizados na Grade de Eventos pertencem ao nível básico ou premium. Começando da versão da API `2020-04-01-preview`, você pode escolher o nível desejado como parte da criação de um tópico ou domínio. O valor padrão é o nível básico. Os tópicos e domínios criados usando versões mais antigas da API usam como padrão o nível básico. Para alterar o tipo de preço para seus tópicos e domínios, confira [Como atualizar o nível para tópicos e domínios](update-tier.md).

## <a name="capabilities-and-features"></a>Funcionalidades e recursos

A tabela a seguir descreve as diferenças entre os níveis:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Regras de firewall de IP para entrada                          | Visualização  | Visualização |
| Marcas de serviço para saída                                | Visualização  | Visualização |
| Integração VNet de ponto de extremidade privado na entrada          | Não disponível   | Visualização |

## <a name="availability"></a>Disponibilidade
Durante a visualização inicial, os tópicos e os domínios do nível Premium com integração de ponto de extremidade privado estão disponíveis nas seguintes regiões:

- Leste dos EUA
- Oeste dos EUA 2
- Centro-Sul dos Estados Unidos

## <a name="pricing-and-quotas"></a>Preços e cotas
Confira [Preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/) para obter os detalhes de preços do uso do nível básico. O preço do nível Premium ainda não foi anunciado e é gratuito até que os preços estejam disponíveis.

As cotas existentes no tópico e na contagem de domínios e na taxa de transferência são aplicáveis aos recursos do nível premium e básico até que o preço do nível premium seja anunciado.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- Para atualizar do nível básico para o premium, confira o artigo [Tipo de preço de atualização](update-tier.md). 
- É possível configurar o firewall de IP para o recurso de Grade de Eventos a fim de restringir o acesso pela Internet pública de apenas um conjunto selecionado de endereços IP ou intervalos de endereços IP. Para obter instruções passo a passo, confira [Configurar firewall](configure-firewall.md).
- É possível configurar pontos de extremidade privados para restringir o acesso somente de redes virtuais selecionadas. Para obter instruções passo a passo, confira [Configurar pontos de extremidade privados](configure-private-endpoints.md).