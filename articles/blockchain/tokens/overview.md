---
title: O que são Tokens de Blockchain do Azure?
description: Os Tokens de Blockchain do Azure são uma PaaS (plataforma como serviço) para emissão e gerenciamento de tokens.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: c1c18298ba82775c9b10bb79c420704bd462e2b4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326080"
---
# <a name="what-is-azure-blockchain-tokens"></a>O que são Tokens do Azure Blockchain?

[!INCLUDE [Preview note](./includes/preview.md)]

Os Tokens de Blockchain do Azure são uma PaaS (plataforma como serviço) para emissão e gerenciamento de tokens padronizados em todos os razões de blockchain do Azure.

Usando os Tokens de Blockchain do Azure você pode criar tokens padronizados para sua solução de blockchain empregando um modelo de token predefinido. Você também pode compor seu próprio modelo de token usando o serviço. Depois de criado, use os Tokens de Blockchain do Azure para se conectar e emitir os tokens em um blockchain. Depois de emitidos, você pode gerenciar os tokens em várias redes de blockchain.

## <a name="templates"></a>Modelos

Use os Tokens de Blockchain do Azure para selecionar um modelo de token criado previamente ou crie seu próprio modelo de token. Os Tokens de Blockchain do Azure são compatíveis com a capacidade de composição do modelo de token que permite criar seu próprio modelo de token com base em comportamentos compatíveis. Os modelos de token podem ser usados para a maioria das soluções de blockchain, uma vez que elas podem ser mapeadas para os tokens utilizados com mais frequência. Você pode começar com um modelo, personalizá-lo e implantar os tokens para a sua solução.

Para obter mais informações sobre os modelos de Tokens de Blockchain do Azure, confira [Modelos de Tokens de Blockchain do Azure](templates.md).

## <a name="management"></a>Gerenciamento

Os Tokens de Blockchain do Azure fornecem gerenciamento do portal do Azure e APIs para se conectar a uma rede blockchain existente. No momento, você pode se conectar ao [Azure Blockchain Service](../service/overview.md) ou a outro blockchain da família Ethereum.

Uma vez conectado a uma ou mais redes blockchain, você pode usar as APIs dos Tokens de Blockchain do Azure para emitir e gerenciar tokens para uso na solução de blockchain. Usando APIs, você poderá integrar o gerenciamento de tokens na lógica e nos aplicativos de negócios. Por exemplo, você pode usar a API REST para gerenciar tokens em vez de gerenciar tokens diretamente no blockchain.

## <a name="blockchains-and-accounts"></a>Blockchains e contas

Os Tokens de Blockchain do Azure fornecem APIs e gerenciamento do portal do Azure para criar novos grupos e novas contas blockchain em redes blockchain conectadas. Você pode criar novas contas diretamente em suas redes conectadas e os Tokens de Blockchain do Azure gerenciam as chaves privadas da conta em seu nome. Usando grupos, você poderá agrupar diferentes contas blockchain de várias redes e gerenciar o controle de acesso por meio dos grupos.

Para obter mais informações sobre o gerenciamento de conta dos Tokens de Blockchain do Azure, confira [Gerenciamento de conta dos Tokens de Blockchain do Azure](account-management.md).

## <a name="token-taxonomy-framework"></a>Estrutura de Taxonomia de Tokens

Os Tokens de Blockchain do Azure são fundamentados em uma base padronizada chamada de TTF (Estrutura de Taxonomia de Tokens). A TTF é um conjunto de entregas criado no grupo de trabalho de tokens da [TTI](https://entethalliance.org/participate/token-taxonomy-initiative/) (Iniciativa de Taxonomia de Tokens). O grupo de trabalho da TTI define uma taxonomia de negócios para tokens e seus comportamentos, que pode ser aplicada em todos os principais razões, incluindo Ethereum, Quorum, Corda e Hyperledger Fabric. O objetivo do grupo de trabalho é criar uma estrutura que padronize o uso de tokens de uma perspectiva de negócios para promover a simplificação e democratizar o desenvolvimento baseado em tokens. Ao permitir que o setor defina esses tokens e seus comportamentos no nível de negócios, a implementação detalhada dos tokens é dissociada da lógica de negócios que manipula os tokens.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [modelos disponíveis de Tokens de Blockchain do Azure](templates.md).
