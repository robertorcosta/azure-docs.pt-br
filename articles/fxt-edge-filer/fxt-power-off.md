---
title: Como desligar uma unidade de arquivo de borda Microsoft Azure FXT
description: Procedimentos para inicialização e desligamento seguro de um nó Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255992"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware do Azure FXT Edge Filer

Embora você possa usar o botão de alimentação física para ligar um nó individual, você não deve usá-lo para desligar a unidade em circunstâncias normais.

Depois que um nó do Azure FXT Edge Filer estiver em uso como parte de um cluster, você deve usar o software do painel de controle de cluster para desligar o hardware. 

> [!NOTE] 
> Para evitar possíveis perdas ou corrupção de dados, use sempre o software Painel de Controle para desligar um Azure FXT Edge Filer. Não use o botão de alimentação física para desligamento, a menos que você seja instruído a fazê-lo pelo Serviço e Suporte ao Cliente da Microsoft.
> 
> Em uma emergência elétrica, desconecte os cabos de alimentação ou use o mecanismo de desconexão elétrica do seu data center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligue um nó do Painel de Controle

Siga estas instruções para desligar com segurança um nó do Azure FXT Edge Filer:

1. Faça login no painel de controle do cluster. (Direções nas [páginas Abrir as Configurações)](fxt-cluster-create.md#open-the-settings-pages)
1. Clique na guia **Configurações** e carregue a página **'Nó fxt' do** **cluster.** > 
1. Na lista de nódulos de cluster, encontre o que você deseja desligar. Clique no botão **Desligar** em sua coluna **Ações.** 
1. Espere alguns momentos. O nó se desligará e desligará a energia.

## <a name="next-steps"></a>Próximas etapas

* Conheça os LEDs de status e outros indicadores no [status de hardware do Monitor Azure FXT Edge Filer](fxt-monitor.md).
* Leia mais sobre as fontes de alimentação do Azure FXT Edge Filer em [cabos de alimentação Connect](fxt-network-power.md#connect-power-cables).
