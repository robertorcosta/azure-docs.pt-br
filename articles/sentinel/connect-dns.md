---
title: Conecte dados de DNS no Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados DNS no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588307"
---
# <a name="connect-your-domain-name-server"></a>Conecte seu servidor de nome de domínio

> [!IMPORTANT]
> O conector de dados DNS no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar qualquer DNS (Domain Name Server) em execução no Windows ao Azure Sentinel. Isso é feito instalando um agente na máquina DNS. Usando registros de DNS, você pode obter insights relacionados à segurança, desempenho e operações na infra-estrutura DNS de sua organização, coletando, analisando e correlacionando registros analíticos e de auditoria e outros dados relacionados dos servidores DNS.

Quando você habilita a conexão de log do DNS, você pode:
- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

## <a name="connected-sources"></a>Fontes conectadas

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| **Fonte conectada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução coleta informações de DNS dos agentes do Windows. |
| [Agentes Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não coleta informações de DNS dos agentes diretos do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução coleta informações de DNS dos agentes em um grupo de gerenciamento conectado do Operations Manager. Não é necessária uma conexão direta entre o agente do Operations Manager e o Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos e ao inventário DNS dos servidores DNS, onde um agente do Log Analytics está instalado. Os dados relacionados ao inventário, como número de servidores DNS, zonas e registros de recursos, são coletados pela execução de cmdlets do PowerShell do DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs de análise e auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos pelo log e diagnóstico avançado de DNS no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Conecte seu aparelho DNS

1. No portal Do Azure Sentinel, selecione **conectores de dados** e escolha o azulejo **DNS (Preview).**
1. Se suas máquinas DeDNS estiverem no Azure:
    1. Clique **em Instalar agente na máquina virtual do Azure Windows**.
    1. Na lista **de máquinas virtuais,** selecione a máquina DNS que deseja transmitir no Azure Sentinel. Certifique-se de que este é um VM do Windows.
    1. Na janela que abre para aquela VM, clique **em Conectar**.  
    1. Clique em **Ativar** na janela **do conector DNS.** 

2. Se a sua máquina DNS não for uma VM Azure:
    1. Clique **em Instalar agente em máquinas não-Azure**.
    1. Na janela **agente direto,** selecione **Baixar o agente windows (64 bits)** ou **baixar o agente do Windows (32 bits)**.
    1. Instale o agente na máquina DNS. Copie o **ID do espaço de trabalho,** **a tecla principal**e **a tecla secundária** e use-as quando solicitada durante a instalação.

3. Para usar o esquema relevante no Log Analytics para os logs DNS, procure por **DnsEvents**.

## <a name="validate"></a>Validar 

No Log Analytics, procure o esquema **DnsEvents** e certifique-se de que há eventos.

## <a name="troubleshooting"></a>Solução de problemas

Se as consultas de consulta não aparecerem no Azure Sentinel, siga estas etapas para que as consultas sejam exibidas corretamente:
1. Ativar os logs do [DNS Analytics em seus servidores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Certifique-se de que os DNSEvents apareçam na lista de coleta do Log Analytics.
3. Ativar [o Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. No Azure DNS Analytics, em **Configuração,** altere qualquer uma das configurações, salve-a e altere-a de volta se necessário e salve-a novamente.
5. Verifique as análises do DNS do Azure para ter certeza de que as consultas estão sendo exibidas.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar aparelhos dns no local ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
