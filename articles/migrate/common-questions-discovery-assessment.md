---
title: Perguntas frequentes sobre descoberta, avaliação e análise de dependência
description: Obtenha respostas para perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e46d1e6ee1dd404e6e040eb394e89dd86a3d4d8e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082264"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência-perguntas comuns

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre a [migração do servidor](common-questions-server-migration.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)

## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantas VMs posso descobrir com um dispositivo?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 250 servidores físicos usando um único dispositivo. Se você tiver mais computadores, leia sobre como [dimensionar uma avaliação do Hyper-V](scale-hyper-v-assessment.md), [dimensionando uma avaliação do VMware](scale-vmware-assessment.md)ou [dimensionando uma avaliação de servidor físico](scale-physical-assessment.md).

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>O tamanho da minha VM foi alterado. Posso executar uma avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local.  Uma avaliação é um instantâneo point-in-time de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como fazer descobrir VMs em um ambiente multilocatário?

- **VMware**: se um ambiente for compartilhado entre locatários e você não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie VMware vCenter Server credenciais que possam acessar somente as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.
- **Hyper-v**: a descoberta usa credenciais de host do Hyper-v. Se as VMs compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  

## <a name="do-i-need-vcenter-server"></a>Preciso de vCenter Server?

Sim, as migrações para Azure exigem vCenter Server em um ambiente VMware para executar a descoberta. As migrações para Azure não dão suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

## <a name="what-are-the-sizing-options"></a>Quais são as opções de dimensionamento?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM para avaliação. As migrações para Azure avaliam os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

Por exemplo, se uma VM local tiver quatro núcleos e 8 GB de memória às 50% de utilização da CPU e 50% de utilização da memória:
- O dimensionamento local recomendará um SKU de VM do Azure que tenha quatro núcleos e 8 GB de memória.
- O dimensionamento baseado em desempenho recomendará uma SKU de VM que tenha dois núcleos e 4 GB de memória, pois a porcentagem de utilização é considerada.

Da mesma forma, o dimensionamento de disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, as migrações para Azure levarão os valores de IOPS e taxa de transferência do disco em conta quando identificarem o tipo de disco de destino (Standard ou Premium).
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, as migrações para Azure recomendarão uma SKU de disco Premium com base no tamanho do disco local. A mesma lógica é aplicada ao dimensionamento de disco quando o dimensionamento é o local e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>O histórico de desempenho e a utilização afetam o dimensionamento?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento nas migrações para Azure.

### <a name="performance-history"></a>Histórico de desempenho

Somente para o dimensionamento baseado em desempenho, as migrações para Azure coletam o histórico de desempenho de computadores locais e, em seguida, o usam para recomendar o tamanho da VM e o tipo de disco no Azure:

1. O dispositivo cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos.
1. O dispositivo acumula os exemplos de 20 segundos coletados e os usa para criar um único ponto de dados a cada 15 minutos.
1. Para criar o ponto de dados, o dispositivo seleciona o valor de pico de todas as amostras de 20 segundos.
1. O dispositivo envia o ponto de dados para o Azure.

### <a name="utilization"></a>Utilização

Quando você cria uma avaliação no Azure, dependendo da duração do desempenho e do valor do percentual do histórico de desempenho definido, as migrações para Azure calculam o valor efetivo de utilização e, em seguida, as usam para o dimensionamento.

Por exemplo, se você definir a duração do desempenho para um dia e o valor de percentil para 95 º percentil, as migrações para Azure classificarão os pontos de exemplo de 15 minutos enviados pelo coletor para o dia anterior em ordem crescente. Ele escolhe o valor de 95 º percentil como a utilização efetiva.

O uso do valor 95 º percentil garante que as exceções sejam ignoradas. As exceções poderão ser incluídas se a migração do Azure usar o 99 º percentil. Para escolher o pico de uso do período sem exceções, defina migrações para Azure para usar o 99 º percentil.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Como as avaliações baseadas em importação são diferentes das avaliações com a fonte de descoberta como dispositivo?

As avaliações baseadas em importação são avaliações criadas com computadores que são importados para migrações para Azure usando um arquivo CSV. Somente quatro campos são obrigatórios para importar: nome do servidor, núcleos, memória e sistema operacional. Aqui estão algumas coisas a serem observadas: 
 - Os critérios de preparação são menos rigorosos em avaliações baseadas em importação no parâmetro de tipo de inicialização. Se o tipo de inicialização não for fornecido, supõe-se que o computador tem o tipo de inicialização BIOS e que o computador não está marcado como **condicionalmente pronto**. Em avaliações com a origem de descoberta como dispositivo, a preparação será marcada como **condicionalmente pronta** se o tipo de inicialização estiver ausente. Essa diferença no cálculo de preparação é porque os usuários podem não ter todas as informações sobre as máquinas nos estágios iniciais do planejamento de migração quando são feitas avaliações baseadas em importação. 
 - As avaliações de importação baseadas em desempenho usam o valor de utilização fornecido pelo usuário para cálculos de dimensionamento correto. Como o valor de utilização é fornecido pelo usuário, as opções **histórico de desempenho** e utilização de **percentil** são desabilitadas nas propriedades de avaliação. Em avaliações com a origem de descoberta como dispositivo, o valor de percentil escolhido é escolhido dos dados de desempenho coletados pelo dispositivo.

## <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependência pode ajudá-lo a avaliar grupos de VMs para migrar com maior confiança. A visualização de dependência faz verificações entre as dependências do computador antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás e ajuda a evitar interrupções inesperadas ao migrar para o Azure. As Migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre o agente e o sem agente?

As diferenças entre a visualização sem agente e a visualização baseada em agente são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Essa opção está atualmente em visualização e só está disponível para VMs VMware. [Examine](migrate-support-matrix-vmware.md#agentless-dependency-visualization) os sistemas operacionais com suporte. | Em disponibilidade geral (GA).
Agente do | Não é necessário instalar agentes em computadores que você deseja verificar. | Agentes a serem instalados em cada computador local que você deseja analisar: o [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e o [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1} | [Examine](concepts-dependency-visualization.md#agentless-visualization) os pré-requisitos e os requisitos de implantação. | [Examine](concepts-dependency-visualization.md#agent-based-visualization) os pré-requisitos e os requisitos de implantação.
Log Analytics | Não obrigatório. | As migrações para Azure usam a solução [mapa do serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-visualization).
Como ele funciona | Captura dados de conexão TCP em computadores habilitados para visualização de dependência. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Mapa do Serviço agentes instalados em um computador coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
Dados | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta. | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta.<br/><br/> Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 
Visualização | O mapa de dependências de um único servidor pode ser exibido durante uma duração de uma hora a 30 dias. | Mapa de dependências de um único servidor.<br/><br/> O mapa pode ser exibido somente em uma hora.<br/><br/> Mapa de dependências de um grupo de servidores.<br/><br/> Adicionar e remover servidores de um grupo da exibição de mapa.
Exportação de dados | Atualmente, não pode ser baixado no formato tabular. | Os dados podem ser consultados com Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Eu pago pela visualização de dependência?

Não. Saiba mais sobre os [preços das migrações para Azure](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que instalo para a visualização de dependência baseada em agente?

Para usar a visualização de dependência baseada em agente, baixe e instale agentes em cada computador local que você deseja avaliar:

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se você tiver computadores que não têm conectividade com a Internet, baixe e instale o Log Analytics gateway neles.

Você precisará desses agentes somente se usar a visualização de dependência baseada em agente.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um workspace existente?

Sim, para a visualização de dependências baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência na visualização baseada em agente não pode ser exportado. No entanto, as migrações para Azure usam Mapa do Serviço e você pode usar a [API REST mapa do serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agente:

- Use um [script para instalar o Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Para MMA, [use a linha de comando ou automação](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)ou use um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além de scripts, você pode usar ferramentas de implantação como o Microsoft Endpoint Configuration Manager e o [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implantar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>A quais sistemas operacionais o MMA dá suporte?

- Exiba a lista de [sistemas operacionais Windows aos quais o MMA dá suporte](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Exiba a lista de [sistemas operacionais Linux aos quais o MMA dá suporte](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso Visualizar dependências por mais de uma hora?

Para a visualização baseada em agente, você pode visualizar dependências de até uma hora. Você pode voltar até um mês até uma data específica no histórico, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibir dependências somente para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

Para a visualização sem agente, você pode exibir o mapa de dependências de um único servidor de uma duração de entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Posso Visualizar dependências de grupos com mais de 10 VMs?

Você pode [Visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) para grupos que têm até 10 VMS. Se você tiver um grupo com mais de 10 VMs, é recomendável dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
