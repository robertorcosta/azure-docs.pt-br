---
title: Arquitetura Azure FarmBeats
description: Descreve a arquitetura do Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482368"
---
# <a name="integration-patterns"></a>Padrões de integração

Azure FarmBeats é uma oferta de negócios para negócios, disponível no Azure Marketplace. O FarmBeats permite a agregação de conjuntos de dados agrícolas entre provedores e a geração de insights acionáveis através da construção de modelos de Inteligência Artificial (IA) ou Machine Learning (ML) fundindo os conjuntos de dados.

![Projeto do FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

As seções a seguir descrevem o padrão de integração do Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Por que se integrar com o Azure FarmBeats?

Esta seção é focada em parceiros que desejam integrar seus sistemas de dados (como sensores, drones, estações meteorológicas) no Azure FarmBeats.

O Azure FarmBeats é uma oferta extensível, que permite que as empresas agrícolas adicionem seus diferentes conjuntos de dados agrícolas históricos e em tempo real em uma única plataforma. O Azure FarmBeats ajuda um negócio agrícola a normalizar, contextualizar e agregar seus dados no contexto de uma fazenda.

Ao se tornar um parceiro de dados com o Azure FarmBeats, você pode abrir seus sistemas para uma adoção mais ampla e alcançar mais clientes com suas ofertas de dados. O Azure FarmBeats fornece uma camada de API extensível chamada Datahub, que ajuda você a ingerir dados de seus dispositivos sistematicamente e em um esquema padronizado.

Uma vez que os dados estejam disponíveis na instância do Azure FarmBeats de seus clientes, seus clientes podem criar análises e ferramentas mais ricas em cima de seus dados.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados de sensores, consulte [a integração de dados de sensores](sensor-partner-integration-in-azure-farmbeats.md) e para a integração de parceiros de imagens, consulte [a integração do parceiro de imagens](imagery-partner-integration-in-azure-farmbeats.md).
