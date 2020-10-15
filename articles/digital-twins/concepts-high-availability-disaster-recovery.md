---
title: Alta disponibilidade e recuperação de desastre
titleSuffix: Azure Digital Twins
description: Descreve os recursos do Azure digital gêmeos que ajudam você a criar soluções de IoT do Azure altamente disponíveis com recursos de recuperação de desastre.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094238"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre do Azure digital gêmeos

Uma importante área de consideração para soluções de IoT resilientes é a continuidade dos negócios e a recuperação de desastres. A criação de **ha (alta disponibilidade)** e **recuperação de desastres (Dr)** pode ajudá-lo a definir e atingir as metas de tempo de atividade apropriadas para sua solução.

Este artigo aborda os recursos de HA e DR oferecidos especificamente pelo serviço gêmeos do Azure digital.

O Azure digital gêmeos dá suporte a essas opções de recurso:
* *Ha entre regiões* – redundância interna para fornecer o tempo de atividade do serviço
* *Dr de região cruzada* – failover para uma região do Azure emparelhada geograficamente no caso de uma falha de data center inesperada

Você também pode ver a seção [*práticas recomendadas*](#best-practices) para obter diretrizes gerais do Azure sobre como projetar para ha/Dr.

## <a name="intra-region-ha"></a>HA entre regiões
 
O Azure digital gêmeos fornece HA de região interna implementando redundâncias dentro do serviço. **Nenhum trabalho adicional é exigido pelos desenvolvedores de uma solução de gêmeos digital do Azure para aproveitar esses recursos de alta disponibilidade.** Embora o Azure digital gêmeos ofereça uma garantia de tempo de atividade razoavelmente alta, as falhas transitórias ainda podem ser esperadas, como em qualquer plataforma de computação distribuída. As políticas de repetição apropriadas devem ser internas aos componentes que interagem com um aplicativo de nuvem para lidar com falhas transitórias.

## <a name="cross-region-dr"></a>Recuperação de desastres de região cruzada

Pode haver algumas situações raras em que um data center enfrenta interrupções extensas devido a falhas de energia ou outros eventos na região. Esses eventos são raros e, durante essas falhas, o recurso de HA de região interna descrito acima pode não ajudar. O gêmeos digital do Azure aborda isso com o failover iniciado pela Microsoft.

O **failover iniciado pela Microsoft** é exercido pela Microsoft em raras situações para realizar o failover de todas as instâncias de gêmeos digitais do Azure de uma região afetada para a região emparelhada geograficamente correspondente. Esse processo é uma opção padrão (sem nenhuma maneira para os usuários recusarem) e não requer nenhuma intervenção do usuário. A Microsoft se reserva o direito de determinar quando essa opção será exercida. Esse mecanismo não envolve um consentimento de usuário antes do failover da instância do usuário.

>[!NOTE]
> Alguns serviços do Azure também fornecem uma opção adicional chamada **failover iniciado pelo cliente**, que permite que os clientes iniciem um failover apenas para sua instância, como para executar uma análise de recuperação de desastre. No momento, esse mecanismo **não tem suporte** do Azure digital gêmeos. 

## <a name="best-practices"></a>Práticas recomendadas

Para obter as práticas recomendadas de HA/DR, consulte as seguintes diretrizes do Azure neste tópico: 
* O artigo [*orientação técnica da continuidade de negócios do Azure*](/azure/architecture/framework/resiliency/overview) descreve uma estrutura geral para ajudá-lo a pensar na continuidade dos negócios e na recuperação de desastres. 
* O artigo [*recuperação de desastres e alta disponibilidade para aplicativos do Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornece diretrizes de arquitetura sobre estratégias para aplicativos do Azure para atingir alta disponibilidade (ha) e recuperação de desastres (Dr).

## <a name="next-steps"></a>Próximas etapas 

Leia mais sobre a introdução às soluções de gêmeos digital do Azure:
 
* [*O que são Gêmeos Digitais do Azure?*](overview.md)
* [*Início rápido: explorar um cenário de exemplo*](quickstart-adt-explorer.md)