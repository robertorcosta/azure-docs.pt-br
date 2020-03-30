---
title: Configure a análise de dependência baseada em agentes na avaliação do servidor Azure Migrate
description: Este artigo descreve como configurar a análise de dependência baseada em agentes na Avaliação do Servidor Migração do Azure.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453608"
---
# <a name="set-up-dependency-visualization"></a>Configurar visualização de dependência

Este artigo descreve como configurar a análise de dependência baseada em agentes no Azure Migrate:Server Assessment. [A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar e entender as dependências entre as máquinas que deseja avaliar e migrar para o Azure.

## <a name="before-you-start"></a>Antes de começar

- [Saiba mais sobre](concepts-dependency-visualization.md#agent-based-analysis) a análise de dependência baseada em agentes.
- Revise os pré-requisitos e os requisitos de suporte para configurar a visualização de dependência baseada em agentes para [VMs VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e [VMs Hiper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto azure Migrate.
- Se você já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta Azure Migrate:Server Assessment.
- Certifique-se de configurar um [aparelho Azure Migrate](migrate-appliance.md) para descobrir suas máquinas no local. Saiba como configurar um aparelho para servidores [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)ou [físicos.](how-to-set-up-appliance-physical.md) O aparelho descobre máquinas no local e envia metadados, dados de desempenho para o Azure Migrate:Server Assessment.
- Para usar a visualização de dependência, você associa um [espaço de trabalho do Log Analytics](../azure-monitor/platform/manage-access.md) a um projeto Do Zure Migrate:
    - Você só pode anexar um espaço de trabalho depois de configurar o aparelho Azure Migrate e descobrir máquinas no projeto Azure Migrate.
    - Certifique-se de ter um espaço de trabalho na assinatura que contém o projeto Azure Migrate.
    - O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.
    - O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Você pode associar um espaço de trabalho novo ou existente do Log Analytics a um projeto do Azure Migrate.
    - Você anexa o espaço de trabalho na primeira vez que você configura a visualização de dependência para uma máquina. O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado.
    - No Log Analytics, o espaço de trabalho associado ao Azure Migrate é marcado com a tecla Migration Project e o nome do projeto.

## <a name="associate-a-workspace"></a>Associar um espaço de trabalho

1. Depois de descobrir máquinas para avaliação, no **Servers** > **Azure Migrate: Server Assessment**, clique em Visão **geral**.  
2. No **Azure Migrate: Avaliação do servidor,** clique **em Essenciais**.
3. No **OMS Workspace,** clique **em Requer configuração**.

     ![Configurar o espaço de trabalho do Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Em **Configurar o espaço de trabalho DO OMS,** especifique se deseja criar um novo espaço de trabalho ou use um já existente.
    - Você pode selecionar um espaço de trabalho existente de todos os espaços de trabalho na assinatura do projeto migrar.
    - Você precisa do acesso do Reader ao espaço de trabalho para associá-lo.
5. Se você criar um novo espaço de trabalho, selecione um local para ele.

    ![Adicionar um novo workspace](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

Em cada máquina que você deseja analisar, instale os agentes.

> [!NOTE]
> Para máquinas monitoradas pelo System Center Operations Manager 2012 R2 ou posterior, você não precisa instalar o agente de MMA. O Mapa de Serviços integra-se ao Gerente de Operações. [Siga](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) as orientações de integração.

1. No **Azure Migrate: Server Assessment**, clique em Servidores **descobertos**.
2. Para cada máquina que você deseja analisar com visualização de dependência, na coluna **Dependências,** clique em **Requer instalação do agente**.
3. Na página **Dependências,** baixe o agente DE MMA e Dependência para Windows ou Linux.
4. Em **Configurar agente de MMA,** copie o ID e a chave do espaço de trabalho. Você precisa disso quando instalar o agente de MMA.

    ![Instale os agentes](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalar o MMA

Instale o MMA em cada máquina Windows ou Linux que você deseja analisar.

### <a name="install-mma-on-a-windows-machine"></a>Instale o MMA em uma máquina Windows

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página **Bem-vindo**, clique em **Avançar**. Na página **Termos de Licença,** clique **em Eu Concordo** em aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Avançar**.

Você pode instalar o agente a partir da linha de comando ou usando um método automatizado, como Gerenciador de Configuração ou [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) sobre como usar esses métodos para instalar o agente do MMA.
- O agente MMA também pode ser instalado usando esse [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sobre os sistemas operacionais Windows suportados pelo MMA.

### <a name="install-mma-on-a-linux-machine"></a>Instale o MMA em uma máquina Linux

Para instalar o MMA em uma máquina Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sobre a lista de sistemas de operacionais Linux com suporte no MMA. 

## <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência

1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sobre como você pode usar scripts para instalar o Dependency agent.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sobre os sistemas operacionais suportados pelo agente dependency.


## <a name="create-a-group-using-dependency-visualization"></a>Crie um grupo usando visualização de dependência

Agora crie um grupo para avaliação. 


> [!NOTE]
> Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Se você tem mais de 10 máquinas, divida-as em grupos menores.

1. No **Azure Migrate: Server Assessment**, clique em Servidores **descobertos**.
2. Na coluna **Dependências,** clique **em Exibir dependências** para cada máquina que deseja revisar.
3. No mapa de dependência, você pode ver o seguinte:
    - Conexões TCP de entrada (clientes) e saída (servidores) de e para a máquina.
    - As máquinas dependentes que não possuem os agentes de dependência instalados são agrupadas por números de portas.
    - Máquinas dependentes com agentes de dependência instalados são mostradas como caixas separadas.
    - Processos funcionando dentro da máquina. Expanda cada caixa de máquina para visualizar os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para ver os detalhes.

4. Você pode procurar por dependências em períodos diferentes clicando no período no rótulo de intervalo de tempo.
    - Por padrão, o intervalo é de uma hora. 
    - Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
    - O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo maior, use o Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar as máquinas dependentes que deseja agrupar, use Ctrl+Click para selecionar várias máquinas no mapa e clique em **Máquinas de Grupo**.
6. Especifique um nome de grupo.
7. Verifique se as máquinas dependentes foram descobertas pelas Migrações para Azure.

    - Se uma máquina dependente não for descoberta pelo Azure Migrate: Server Assessment, você não poderá adicioná-la ao grupo.
    - Para adicionar uma máquina, execute a descoberta novamente e verifique se a máquina foi descoberta.

8. Se quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que você instale agentes em todas as máquinas do grupo e, em seguida, visualize dependências para todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta no Azure Monitor

Você pode consultar dados de dependência capturados pelo Mapa de Serviço no espaço de trabalho do Log Analytics associado ao projeto Azure Migrate. O Log Analytics é usado para gravar e executar consultas de log do Azure Monitor.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) pesquisar dados do Mapa de Serviços no Log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md) das consultas de log de gravação no [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Execute uma consulta para dados de dependência da seguinte forma:

1. Depois de instalar os agentes, acesse o portal e clique em **Visão geral**.
2. No **Azure Migrate: Avaliação do servidor,** clique **em Visão geral**. Clique na seta para baixo para expandir **essentials**.
3. No **OMS Workspace,** clique no nome do espaço de trabalho.
3. Na página do espaço de trabalho do Log Analytics > **Geral,** clique **em Logs**.
4. Escreva sua consulta e clique **em Executar**.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas de amostra que você pode usar para extrair dados de dependência.

- Você pode modificar as consultas para extrair seus pontos de dados preferenciais.
- [Revise](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) uma lista completa de registros de dados de dependência.
- [Revise](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) consultas adicionais de amostra.

#### <a name="sample-review-inbound-connections"></a>Amostra: Revisar conexões de entrada

Revise as conexões de entrada para um conjunto de VMs.

- Os registros na tabela para métricas de conexão (VMConnection) não representam conexões físicas individuais de rede.
- Várias conexões de rede física são agrupadas em uma conexão lógica.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de conexão de rede física são agregados no VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Amostra: Resumir dados enviados e recebidos

Esta amostra resume o volume de dados enviados e recebidos em conexões de entrada entre um conjunto de máquinas.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Próximas etapas

[Crie uma avaliação](how-to-create-assessment.md) para um grupo.


