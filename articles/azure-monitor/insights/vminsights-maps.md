---
title: Exibir dependências de aplicativos com o Monitor Azure para VMs
description: Mapa é uma característica do Azure Monitor para VMs. Ele descobre automaticamente componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usar o recurso Mapa em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283847"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Use o recurso Mapa do Monitor Do Azure para VMs para entender os componentes do aplicativo
No Monitor Azure para VMs, você pode visualizar componentes de aplicativos descobertos em máquinas virtuais Windows e Linux (VMs) que são executadas no Azure ou no seu ambiente. Você pode observar as VMs de duas maneiras. Exibir um mapa diretamente de uma VM ou visualizar um mapa do Azure Monitor para ver os componentes em grupos de VMs. Este artigo irá ajudá-lo a entender esses dois métodos de visualização e como usar o recurso Mapa. 

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Faça login no [portal Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência do Mapa
Antes de mergulhar na experiência do Mapa, você deve entender como ele apresenta e visualiza informações. Se você selecionar o recurso Mapa diretamente de uma VM ou do Azure Monitor, o recurso Mapa apresenta uma experiência consistente. A única diferença é que do Azure Monitor, um mapa mostra todos os membros de um aplicativo ou cluster de vários níveis.

O recurso Mapa visualiza as dependências da VM descobrindo processos em execução que possuem: 

- Conexões de rede ativas entre servidores.
- Latência de conexão de entrada e saída.
- Portas em qualquer arquitetura conectada ao TCP em um intervalo de tempo especificado.  
 
Expanda uma VM para mostrar detalhes do processo e somente os processos que se comunicam com a VM. O grupo de clientes mostra a contagem de clientes front-end que se conectam à VM. Os grupos de porta de servidor mostram a contagem de servidores back-end aos que a VM se conecta. Expanda um grupo de porta de servidor para ver a lista detalhada de servidores que se conectam sobre essa porta.  

Quando você seleciona a VM, o painel **Propriedades** à direita mostra as propriedades da VM. As propriedades incluem informações do sistema relatadas pelo sistema operacional, propriedades da VM do Azure e um gráfico de rosquinhas que resume as conexões descobertas. 

![O painel Propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione **Log Events** para mostrar uma lista de dados que a VM enviou ao Azure Monitor. Esses dados estão disponíveis para consulta.  Selecione qualquer tipo de registro para abrir a página **Logs,** onde você verá os resultados desse tipo de registro. Você também vê uma consulta pré-configurada que é filtrada contra a VM.  

![O painel de eventos log](./media/vminsights-maps/properties-pane-logs-01.png)

Feche a página **Logs** e retorne ao painel **Propriedades.** Lá, selecione **Alertas** para visualizar alertas de critérios de saúde da VM. O recurso Mapa integra-se aos Alertas Do Azure para mostrar alertas para o servidor selecionado no intervalo de tempo selecionado. O servidor exibe um ícone para alertas atuais e o painel **Alertas de Máquina** lista os alertas. 

![O painel Alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para tornar o recurso Mapa exibir alertas relevantes, crie uma regra de alerta que se aplique a um computador específico:

- Inclua uma cláusula para agrupar alertas por computador (por exemplo, **pelo intervalo do computador de 1 minuto**).
- Baseie o alerta em uma métrica.

Para obter mais informações sobre o Azure Alerts e criar regras de alerta, consulte [Alertas Unificados no Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

No canto superior direito, a opção **Legenda** descreve os símbolos e papéis no mapa. Para dar uma olhada mais de perto no seu mapa e movê-lo, use os controles de zoom no canto inferior direito. Você pode definir o nível de zoom e encaixar o mapa no tamanho da página.  

## <a name="connection-metrics"></a>Métricas de conexão
O painel **Conexões** exibe métricas padrão para a conexão selecionada da VM sobre a porta TCP. As métricas incluem tempo de resposta, solicitações por minuto, taxa de transferência de tráfego e links.  

![Gráficos de conectividade de rede no painel Conexões](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Conexões com falha
O mapa mostra conexões com falha para processos e computadores. Uma linha vermelha tracejada indica que um sistema cliente está falhando em chegar a um processo ou porta. Para sistemas que usam o agente dependency, o agente relata tentativas de conexão fracassadas. O recurso Mapa monitora um processo observando soquetes TCP que não conseguem estabelecer uma conexão. Essa falha pode resultar de um firewall, uma configuração errada no cliente ou servidor ou um serviço remoto indisponível.

![Uma conexão falha no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Entender conexões com falha pode ajudá-lo a solucionar problemas, validar a migração, analisar a segurança e entender a arquitetura geral do serviço. Conexões fracassadas às vezes são inofensivas, mas muitas vezes apontam para um problema. As conexões podem falhar, por exemplo, quando um ambiente de failover de repente se torna inalcançável ou quando dois níveis de aplicativo não podem se comunicar entre si após uma migração na nuvem.

### <a name="client-groups"></a>Grupos de clientes
No mapa, os grupos de clientes representam máquinas clientes que se conectam à máquina mapeada. Um único grupo de clientes representa os clientes para um processo ou máquina individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os clientes monitorados e endereços IP dos sistemas em um grupo de clientes, selecione o grupo. O conteúdo do grupo aparece abaixo.  

![A lista de endereços IP de um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo incluir clientes monitorados e não monitorados, você pode selecionar a seção apropriada do gráfico de rosquinhas do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de porta de servidor
Os grupos de porta de servidor representam portas em servidores que têm conexões de entrada da máquina mapeada. O grupo contém a porta do servidor e uma contagem do número de servidores que possuem conexões com essa porta. Selecione o grupo para ver os servidores e conexões individuais. 

![Um grupo de porta de servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo incluir servidores monitorados e não monitorados, você poderá selecionar a seção apropriada do gráfico de rosquinhas do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Ver um mapa de uma VM 

Para acessar o Monitor Azure para VMs diretamente de uma VM:

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma VM. Na seção **Monitoramento,** escolha **Insights**.  
3. Selecione a guia **Mapa**.

O mapa visualiza as dependências da VM descobrindo grupos de processos e processos em execução que possuem conexões de rede ativas em um intervalo de tempo especificado.  

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências eram no passado, você pode consultar os intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor **TimeRange** no canto superior esquerdo. Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

![Visão geral do mapa de VM direta](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Exibir um mapa a partir de um conjunto de escala de máquina virtual

Para acessar o Monitor Azure para VMs diretamente de um conjunto de escala de máquina virtual:

1. No portal Azure, selecione **Conjuntos de escalas de máquinas virtuais**.
2. Na lista, escolha uma VM. Em seguida, na seção **Monitoramento,** escolha **Insights**.  
3. Selecione a guia **Mapa**.

O mapa visualiza todas as instâncias na escala definida como um nó de grupo, juntamente com as dependências do grupo. O nó expandido lista as instâncias no conjunto de escalas. Você pode percorrer essas instâncias 10 de cada vez. 

Para carregar um mapa para uma instância específica, primeiro selecione essa instância no mapa. Em seguida, selecione o botão **elipse** (...) à direita e escolha **'Mapa do servidor de carga'.** No mapa que aparece, você vê grupos de processos e processos que têm conexões de rede ativas em um intervalo de tempo especificado. 

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências eram no passado, você pode consultar os intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor **TimeRange.** Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.

![Visão geral do mapa de VM direta](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Você também pode acessar um mapa para uma instância específica a partir da exibição **Instâncias** para o conjunto de escalas da máquina virtual. Na seção Configurações, vá para > **'''''''''''Configurações'.** **Settings** **Instances**

## <a name="view-a-map-from-azure-monitor"></a>Veja um mapa do Azure Monitor

No Azure Monitor, o recurso Mapa fornece uma visão global de suas VMs e suas dependências. Para acessar o recurso Mapa no Monitor do Azure:

1. No portal Azure, selecione **Monitor**. 
2. Na seção **Insights,** escolha **Máquinas Virtuais**.
3. Selecione a guia **Mapa**.

   ![Mapa de visão geral do Monitor do Azure de várias VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha um espaço de trabalho usando o seletor **Workspace** na parte superior da página. Se você tiver mais de um espaço de trabalho do Log Analytics, escolha o espaço de trabalho habilitado com a solução e que tenha VMs reportando a ela. 

O seletor **de grupos** retorna assinaturas, grupos de recursos, grupos [de computadores](../../azure-monitor/platform/computer-groups.md)e conjuntos de escala de máquinas virtuais de computadores relacionados ao espaço de trabalho selecionado. Sua seleção se aplica apenas ao recurso Mapa e não é continuada em Desempenho ou Saúde.

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências eram no passado, você pode consultar os intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor **TimeRange.** Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

## <a name="next-steps"></a>Próximas etapas

Para identificar gargalos, verificar o desempenho e entender a utilização geral de suas VMs, consulte [Exibir o status de desempenho do Monitor Azure para VMs](vminsights-performance.md). 
