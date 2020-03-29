---
title: Introdução à exibição do grupo de segurança no Observador de Rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de exibição da segurança do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840750"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introdução à exibição de regras de segurança eficazes no Azure Network Watcher

Os grupos Segurança da Rede podem ser associados no nível da sub-rede ou no nível da NIC. Quando associado no um nível da sub-rede, ele se aplica a todas as instâncias de VM na sub-rede. A exibição de regras de segurança eficazes retorna todos os NSGs e regras configurados que estão associados a um nível de NIC e sub-rede para uma máquina virtual fornecendo insights sobre a configuração. Além disso, as regras de segurança efetivas são retornadas para cada NIC em uma VM. Usando a exibição de regras de segurança eficazes, você pode avaliar uma VM para vulnerabilidades de rede, como portas abertas. Também é possível validar se o Grupo de Segurança de Rede está funcionando como o esperado com base em uma [comparação entre as regras de segurança configuradas e as aprovadas](network-watcher-nsg-auditing-powershell.md).

Um caso de uso mais estendido está em conformidade com a segurança e auditoria. Você pode definir um conjunto prescritivo de regras de segurança como um modelo de controle da segurança em sua organização. Uma auditoria de conformidade periódica pode ser implementada de forma programática comparando as regras prescritivas com as regras efetivas para cada uma das VMs em sua rede.

Nas regras do portal são exibidas para cada Interface de Rede e agrupadas por entrada vs saída. Isso fornece uma exibição simples das regras aplicadas em uma máquina virtual. Um botão de download é fornecido para baixar com facilidade todas as regras de segurança, independentemente da guia em um arquivo CSV.

![exibição do grupo de segurança][1]

As regras podem ser selecionadas e uma nova folha é aberta para mostrar o Grupo de Segurança da Rede e os prefixos de origem e destino. Nessa folha, você pode navegar diretamente para o recurso Grupo de Segurança da Rede.

![busca detalhada][2]

### <a name="next-steps"></a>Próximas etapas

Saiba como fazer a auditoria das configurações do Grupo de Segurança da Rede visitando [Auditar as configurações do Grupo de Segurança da Rede com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









