---
title: Problemas conhecidos do Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo aborda os problemas conhecidos com o Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade, da descoberta de dependência do aplicativo e de desempenho do sistema operacional da VM do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: 711b3707d536c4858578817589670edf0f467b64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670722"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (versão prévia)

Este artigo aborda os problemas conhecidos com o Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade, da descoberta de componentes de aplicativo e de desempenho do sistema operacional da VM do Azure. 

## <a name="health"></a>Integridade 
Os seguintes são problemas conhecidos com a versão atual do recurso de Integridade:

- Se uma VM do Azure for removida ou excluída, ela será mostrada na exibição de lista da VM durante algum tempo. Além disso, ao clicar no estado de uma VM removida ou excluída, a exibição **Diagnóstico de Integridade** será aberta e, em seguida, um loop de carregamento será iniciado. A seleção do nome da VM excluída abre um painel com uma mensagem informando que a VM foi excluída.
- As alterações de configuração, como a atualização de um limite, levam até 30 minutos, mesmo se o portal ou a API do Monitor da Carga de Trabalho atualizá-las imediatamente. 
- A experiência de Diagnóstico de Integridade é atualizada mais rapidamente do que outras exibições. As informações podem ser atrasadas quando você alterna entre elas. 
- Para VMs do Linux, o título da página listando os critérios de integridade para exibição de uma única VM tem o nome de domínio inteiro da VM em vez do nome da VM definido pelo usuário. 
- Depois que você desabilitar o monitoramento de uma VM usando um dos métodos compatíveis e tentar implantá-la novamente, deverá implantá-la no mesmo workspace. Se você optar por usar um workspace diferente e tentar exibir o estado de integridade dessa VM, ela poderá apresentar um comportamento inconsistente.
- Depois de remover os componentes da solução do seu workspace, você poderá continuar a ver o estado de integridade de suas VMs do Azure; especificamente dados de desempenho e mapa quando você navega para qualquer visualização no portal. Os dados acabarão parando de aparecer na visualização Desempenho e Mapa depois de algum tempo; no entanto, a visualização de integridade continuará mostrando o status de integridade de suas VMs. A opção **Experimente agora** estará disponível para reintegração apenas das exibições Desempenho e Mapa.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
Para entender os requisitos e métodos para habilitar o monitoramento de suas máquinas virtuais, examine [habilitar Azure monitor para VMs](vminsights-enable-overview.md).
