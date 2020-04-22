---
title: Perguntas sobre descoberta, avaliação e análise de dependência no Azure Migrate
description: Obtenha respostas para perguntas comuns sobre descoberta, avaliação e análise de dependência no Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681911"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Análise de descoberta, avaliação e dependência - Perguntas comuns

Este artigo responde a perguntas comuns sobre a descoberta, avaliação e análise de dependência no Azure Migrate. Se você tiver outras perguntas, verifique esses recursos:

- [Perguntas gerais](resources-faq.md) sobre o Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Receba perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quais geografias são apoiadas para descoberta e avaliação com o Azure Migrate?

Reveja as geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantas VMs posso descobrir com um aparelho?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 250 servidores físicos usando um único aparelho. Se você tiver mais máquinas, leia sobre [escalar uma avaliação Hyper-V,](scale-hyper-v-assessment.md) [dimensionar uma avaliação vMware](scale-vmware-assessment.md)ou [dimensionar uma avaliação física do servidor](scale-physical-assessment.md).

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Eu não posso ver alguns tipos de VM no governo Azure

Os tipos de VM suportados para avaliação e migração dependem da disponibilidade na localização do governo azure. Você pode [rever e comparar](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) tipos de VM no Governo Azure.


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>O tamanho da minha VM mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate coleta continuamente informações sobre o ambiente local.  Uma avaliação é um instantâneo pontual de VMs no local. Se você alterar as configurações de uma VM que deseja avaliar, use a opção de recalcular para atualizar a avaliação com as últimas alterações.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como descobrir VMs em um ambiente multilocatário?

- **VMware**: Se um ambiente for compartilhado entre os inquilinos e você não quiser descobrir as VMs de um inquilino na assinatura de outro inquilino, crie credenciais vMware vCenter Server que podem acessar apenas as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no aparelho Azure Migrate.
- **Hyper-V**: Discovery usa credenciais de host Hyper-V. Se as VMs compartilham o mesmo host Hyper-V, atualmente não há como separar a descoberta.  

## <a name="do-i-need-vcenter-server"></a>Preciso do vCenter Server?

Sim, o Azure Migrate requer o vCenter Server em um ambiente VMware para realizar a descoberta. O Azure Migrate não suporta a descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

## <a name="what-are-the-sizing-options"></a>Quais são as opções de dimensionamento?

Com o dimensionamento as-on-premises, o Azure Migrate não considera os dados de desempenho da VM para avaliação. O Azure Migrate avalia os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado em dados de utilização.

Por exemplo, se uma VM no local tiver quatro núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização de memória:
- O dimensionamento as-no-local recomendará um Azure VM SKU que tenha quatro núcleos e 8 GB de memória.
- O dimensionamento baseado em desempenho recomendará um VM SKU que tenha dois núcleos e 4 GB de memória porque a porcentagem de utilização é considerada.

Da mesma forma, o dimensionamento do disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, o Azure Migrate leva em conta os valores de IOPS e throughput do disco quando identifica o tipo de disco de destino (Padrão ou Premium).
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, o Azure Migrate recomenda um SKU de disco Premium com base no tamanho do disco local. A mesma lógica é aplicada ao dimensionamento de disco quando o dimensionamento é as-on-premises e o tipo de armazenamento é Padrão ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>O histórico de desempenho e a utilização afetam o dimensionamento?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento no Azure Migrate.

### <a name="performance-history"></a>Histórico de desempenho

Apenas para dimensionamento baseado em desempenho, o Azure Migrate coleta o histórico de desempenho das máquinas locais e, em seguida, usa-o para recomendar o tamanho da VM e o tipo de disco no Azure:

1. O aparelho traça um perfil contínuo do ambiente local para coletar dados de utilização em tempo real a cada 20 segundos.
1. O aparelho enrola as amostras coletadas de 20 segundos e as usa para criar um único ponto de dados a cada 15 minutos.
1. Para criar o ponto de dados, o aparelho seleciona o valor de pico de todas as amostras de 20 segundos.
1. O aparelho envia o ponto de dados para o Azure.

### <a name="utilization"></a>Utilização

Quando você cria uma avaliação no Azure, dependendo da duração do desempenho e do valor percentual do histórico de desempenho definido, o Azure Migrate calcula o valor de utilização efetivo e, em seguida, usa-o para dimensionamento.

Por exemplo, se você definir a duração do desempenho para um dia e o valor do percentil para 95% do percentil, o Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo coletor para o dia passado em ordem crescente. Ele escolhe o valor do percentil 95 como a utilização efetiva.

O uso do valor do percentil 95 garante que os outliers sejam ignorados. Outliers podem ser incluídos se o seu Azure Migrate usar o percentil 99. Para escolher o pico de uso para o período sem perder nenhum outliers, coloque O Azure Migrate para usar o percentil 99.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Como as avaliações baseadas em importação são diferentes das avaliações com a fonte de descoberta como aparelho?

Avaliações baseadas em importação são avaliações criadas com máquinas que são importadas para o Azure Migrate usando um arquivo CSV. Apenas quatro campos são obrigatórios para importar: nome do servidor, núcleos, memória e sistema operacional. Aqui estão algumas coisas a notar: 
 - Os critérios de prontidão são menos rigorosos em avaliações baseadas em importação no parâmetro tipo boot. Se o tipo de inicialização não for fornecido, presume-se que a máquina tenha o tipo de inicialização do BIOS e a máquina não esteja marcada como **Condicionadamente Pronta**. Em avaliações com a fonte de descoberta como aparelho, a prontidão é marcada como **Condicionalmente pronta** se o tipo de inicialização estiver faltando. Essa diferença no cálculo de prontidão é porque os usuários podem não ter todas as informações sobre as máquinas nos estágios iniciais do planejamento migratório quando as avaliações baseadas em importação são feitas. 
 - As avaliações de importação baseadas em desempenho usam o valor de utilização fornecido pelo usuário para cálculos de dimensionamento certo. Uma vez que o valor de utilização é fornecido pelo usuário, o **histórico de desempenho** e as opções de utilização **percentil** são desativadas nas propriedades de avaliação. Em avaliações com a fonte de descoberta como aparelho, o valor do percentil escolhido é colhido a partir dos dados de desempenho coletados pelo aparelho.

## <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependência pode ajudá-lo a avaliar grupos de VMs para migrar com maior confiança. A visualização de dependência saqueia as dependências da máquina antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás, e ajuda a evitar paralisações inesperadas quando você migra para o Azure. As Migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A análise de dependência baseada em agentes não está disponível no governo Azure. Você pode usar a análise de dependência sem agente

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre agente e sem agente?

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agentes**
--- | --- | ---
Suporte | Esta opção está atualmente em pré-visualização, e está disponível apenas para VMware VMware. [Revisar](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sistemas operacionais suportados. | Disponibilidade geral (GA).
Agente | Não há necessidade de instalar agentes em máquinas que você deseja cruzar. | Agentes a serem instalados em cada máquina local que você deseja analisar: o [agente de monitoramento microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e o agente de [dependência](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Pré-requisitos | [Revise](concepts-dependency-visualization.md#agentless-analysis) os pré-requisitos e os requisitos de implantação. | [Revise](concepts-dependency-visualization.md#agent-based-analysis) os pré-requisitos e os requisitos de implantação.
Log Analytics | Não obrigatório. | O Azure Migrate usa a solução [Mapa de Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nos registros do Monitor do [Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-analysis).
Como ele funciona | Captura dados de conexão TCP em máquinas habilitadas para visualização de dependência. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados em uma máquina coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor da máquina de destino, processo, nome do aplicativo e porta. | Nome do servidor da máquina de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor da máquina de destino, processo, nome do aplicativo e porta.<br/><br/> O número de conexões, informações de latência e transferência de dados são coletados e disponíveis para consultas do Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visualizado durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa pode ser visto em apenas uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova servidores em um grupo da exibição do mapa.
Exportação de dados | Não pode ser baixado no momento em formato tabular. | Os dados podem ser consultados com o Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Eu pago pela visualização da dependência?

Não. Saiba mais sobre [os preços do Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que eu instalo para visualização de dependência baseada em agentes?

Para usar a visualização de dependência baseada em agentes, baixe e instale agentes em cada máquina local que você deseja avaliar:

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se você tem máquinas que não possuem conectividade com a internet, baixe e instale o gateway Log Analytics neles.

Você só precisa desses agentes se usar a visualização de dependência baseada em agentes.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um workspace existente?

Sim, para visualização de dependência baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência em visualização baseada em agentes não pode ser exportado. No entanto, o Azure Migrate usa o Mapa de Serviço, e você pode usar a [API Do Mapa de Serviço para](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agentes:

- Use um [script para instalar o agente dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Para MMA, [use a linha de comando ou automação,](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)ou use um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além dos scripts, você pode usar ferramentas de implantação como O Microsoft Endpoint Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implantar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operacionais o MMA suporta?

- Veja a lista de [sistemas operacionais Windows que o MMA suporta](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Veja a lista de [sistemas operacionais Linux que o MMA suporta](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso visualizar dependências por mais de uma hora?

Para visualização baseada em agentes, você pode visualizar dependências por até uma hora. Você pode voltar até um mês para uma data específica na história, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para visualizar dependências para ontem, mas você pode visualizar dependências apenas por uma janela de uma hora. No entanto, você pode usar os logs do Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

Para visualização sem agente, você pode visualizar o mapa de dependência de um único servidor a partir de uma duração entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Posso visualizar dependências para grupos de mais de 10 VMs?

Você pode [visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) para grupos que têm até 10 VMs. Se você tem um grupo que tem mais de 10 VMs, recomendamos que você divida o grupo em grupos menores e, em seguida, visualize as dependências.

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
