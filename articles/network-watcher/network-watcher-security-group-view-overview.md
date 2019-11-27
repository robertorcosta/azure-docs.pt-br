---
title: Introdução à exibição do grupo de segurança no Observador de Rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de exibição da segurança do Observador de Rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: kumud
ms.openlocfilehash: 856135c6cf903b1530f0ae922edddd09e5a1b0d3
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539227"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introdução à exibição de regras de segurança em vigor no observador de rede do Azure

Os grupos Segurança da Rede podem ser associados no nível da sub-rede ou no nível da NIC. Quando associado no um nível da sub-rede, ele se aplica a todas as instâncias de VM na sub-rede. A exibição de regras de segurança em vigor retorna todos os NSGs e regras configurados que estão associados em um nível de NIC e de sub-rede para uma máquina virtual que fornece informações sobre a configuração. Além disso, as regras de segurança efetivas são retornadas para cada NIC em uma VM. Usando a exibição de regras de segurança efetivas, você pode avaliar uma VM para vulnerabilidades de rede, como portas abertas. Também é possível validar se o Grupo de Segurança de Rede está funcionando como o esperado com base em uma [comparação entre as regras de segurança configuradas e as aprovadas](network-watcher-nsg-auditing-powershell.md).

Um caso de uso mais estendido está em conformidade com a segurança e auditoria. Você pode definir um conjunto prescritivo de regras de segurança como um modelo de controle da segurança em sua organização. Uma auditoria de conformidade periódica pode ser implementada de forma programática comparando as regras prescritivas com as regras efetivas para cada uma das VMs em sua rede.

Nas regras do portal são exibidas para cada interface de rede e agrupadas por entrada vs de saída. Isso fornece uma exibição simples das regras aplicadas em uma máquina virtual. Um botão de download é fornecido para baixar com facilidade todas as regras de segurança, independentemente da guia em um arquivo CSV.

![exibição do grupo de segurança][1]

As regras podem ser selecionadas e uma nova folha é aberta para mostrar o Grupo de Segurança da Rede e os prefixos de origem e destino. Nessa folha, você pode navegar diretamente para o recurso Grupo de Segurança da Rede.

![busca detalhada][2]

### <a name="next-steps"></a>Próximos passos

Saiba como fazer a auditoria das configurações do Grupo de Segurança da Rede visitando [Auditar as configurações do Grupo de Segurança da Rede com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









