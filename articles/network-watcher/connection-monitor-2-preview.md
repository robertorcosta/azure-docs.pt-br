---
title: Monitor de conexão (visualização) | Microsoft Docs
description: Saiba como usar o monitor de conexão (versão prévia) para monitorar a comunicação de rede em um ambiente distribuído
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 43c49cce1dd53edd5c2b13b01a31f94752579dff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169329"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoramento de conectividade unificada com o monitor de conexão (versão prévia)

O monitor de conexão (versão prévia) fornece recursos unificados de monitoramento de conexão de ponta a ponta no observador de rede do Azure para implantações de nuvem híbridas e do Azure. O observador de rede do Azure fornece ferramentas para monitorar, diagnosticar e exibir métricas relacionadas à conectividade para suas implantações do Azure.

Principais casos de uso:

- Você tem uma VM de servidor Web front-end que se comunica com uma VM do servidor de banco de dados em um aplicativo de várias camadas. Você deseja verificar a conectividade de rede entre as duas VMs.
- Você deseja que as VMs na região leste dos EUA executem ping nas VMs na região EUA Central e compare as latências de rede entre regiões
- Você tem vários sites do Office local em cidades como Seattle. Conectando-se às URLs do Office 365. Você deseja comparar as latências experimentadas pelos usuários usando as URLs do Office 365 de Seattle e Ashburn.
- Você tem uma configuração de aplicativo híbrido que precisa de conectividade com um ponto de extremidade de armazenamento do Azure. Você deseja comparar as latências entre um site local e o aplicativo do Azure conectando-se ao mesmo ponto de extremidade de armazenamento do Azure.
- Você deseja verificar a conectividade de VMs do Azure que hospedam seu aplicativo de nuvem para as configurações locais.

Nesta fase de visualização, a solução reúne o melhor de dois recursos principais: monitor de [conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) do observador de rede e monitor de conectividade do [serviço](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)de monitor de desempenho de rede (NPM).

Descreve

* Experiência unificada e intuitiva para necessidades de monitoramento híbrido e do Azure
* Monitoramento de conectividade entre espaços de trabalho entre regiões
* Frequências de investigação mais altas e melhor visibilidade do desempenho da rede
* Alertas mais rápidos para suas implantações híbridas
* Suporte para verificações de conectividade baseadas em HTTP, TCP e ICMP
* Métricas e suporte Log Analytics para configurações de teste do Azure e não Azure

![Monitor de conexão](./media/connection-monitor-2-preview/hero-graphic.png)

Siga as etapas mencionadas abaixo para iniciar o monitoramento usando o monitor de conexão (versão prévia)

## <a name="step-1-install-monitoring-agents"></a>Etapa 1: instalar agentes de monitoramento

O monitor de conexão depende de executáveis leves para executar verificações de conectividade.  Damos suporte a verificações de conectividade de ambientes do Azure e locais. O executável específico a ser usado depende se sua VM está hospedada no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais do Azure

Para que o monitor de conexão reconheça suas VMs do Azure como fonte de monitoramento, você precisa instalar a extensão da máquina virtual do agente do observador de rede (também conhecida como extensão do observador de rede) neles. A extensão do agente do observador de rede é um requisito para disparar o monitoramento de ponta a ponta e outras funcionalidades avançadas em máquinas virtuais do Azure. Você pode [criar uma VM e instalar a extensão do observador de rede](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[nela](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm).  Você também pode instalar, configurar e solucionar problemas da extensão do observador de rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) separadamente.

Se as regras de firewall ou NSG estiverem bloqueando a comunicação entre a origem e o destino, o monitor de conexão detectará o problema e o mostrará como uma mensagem de diagnóstico na topologia. Para habilitar o monitoramento de conexão, verifique se as regras de firewall e NSG permitem pacotes via TCP ou ICMP entre a origem e o destino.

### <a name="agents-for-on-premise-machines"></a>Agentes para computadores locais

Para que o monitor de conexão reconheça seus computadores locais como fontes de monitoramento, você precisará instalar o agente de Log Analytics nos computadores e habilitar a solução de monitoramento de desempenho de rede. Esses agentes são vinculados a espaços de trabalho Log Analytics e precisam de ID do espaço de trabalho e da chave primária configurados antes que possam iniciar o monitoramento.

Para instalar Log Analytics agente para computadores Windows, siga as instruções mencionadas neste [link](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Verifique se o destino está acessível se houver firewalls ou NVA (dispositivos de rede virtual) no caminho.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Etapa 2: habilitar o observador de rede em sua assinatura

Todas as assinaturas com uma VNET são habilitadas com o observador de rede. Quando você cria uma rede virtual em sua assinatura, o observador de rede será habilitado automaticamente na região e na assinatura da rede virtual. Não há nenhum impacto em seus recursos ou no encargo associado para habilitar automaticamente o observador de rede. Verifique se o observador de rede não está explicitamente desabilitado em sua assinatura. Para obter mais informações, consulte [habilitar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Etapa 3: criar um monitor de conexão 

O _Monitor de conexão_ monitora a comunicação em intervalos regulares e informa você sobre a acessibilidade, a latência e as alterações de topologia de rede entre os agentes de origem e os pontos de extremidade de destino. As fontes podem ser VMs do Azure ou máquinas locais que têm um agente de monitoramento instalado. Os pontos de extremidade de destino podem ser URLs do Office 365, URLs do Dynamics 365, URLs personalizadas, IDs de recursos de VM do Azure, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="accessing-connection-monitor-preview"></a>Acessando o monitor de conexão (versão prévia)

1. Na home page de portal do Azure, visite observador de rede
2. Clique na guia "Monitor de conexão (visualização)" na seção monitoramento no painel esquerdo do observador de rede.
3. Você pode ver todos os monitores de conexão que são criados usando a experiência do monitor de conexão (versão prévia). Todos os monitores de conexão criados com a experiência clássica da guia Monitor de conexão ficarão visíveis na guia Monitor de conexão.

    ![Criar um monitor de conexão](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Criando um monitor de conexão

Os monitores de conexão criados usando o monitor de conexão (versão prévia) fornecem a capacidade de adicionar VMs locais e do Azure como fontes e monitorar a conectividade a pontos de extremidade, que podem abranger o Azure ou qualquer outra URL/IP.

A seguir estão as entidades em um monitor de conexão:

* Recurso de monitor de conexão – recurso do Azure específico da região. Todas as entidades mencionadas abaixo são propriedades de um recurso de monitor de conexão.
* Pontos de extremidade – todas as origens e destinos que participam de verificações de conectividade são chamados de pontos de extremidade. Exemplos de ponto de extremidade – VMs do Azure, agentes locais, URLs, IPs
* Configuração de teste – cada configuração de teste é específica de protocolo. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros
* Grupo de teste – cada grupo de teste contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Cada monitor de conexão pode conter mais de um grupo de teste
* Teste – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste faz um teste. Teste é o nível mais baixo no qual os dados de monitoramento (verificações com falha% e RTT) estão disponíveis

 ![Criar um monitor de conexão](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Do portal

Para criar um monitor de conexão, siga as etapas mencionadas abaixo:

1. No painel Monitor de conexão (versão prévia), clique em "criar" no canto superior esquerdo.
2. Na guia básico, insira informações para o monitor de conexão
   1. Nome do monitor de conexão – nome do monitor de conexão. Regras de nomenclatura padrão para recursos do Azure se aplicam aqui.
   2. Assinatura – escolha uma assinatura para o monitor de conexão.
   3. Região – escolha uma região para o recurso de monitor de conexão. Você só pode selecionar as VMs de origem que são criadas nessa região.
   4. Configuração do espaço de trabalho-você pode usar o espaço de trabalho padrão criado pelo monitor de conexão para armazenar os dados de monitoramento clicando na caixa de seleção padrão. Para escolher um espaço de trabalho personalizado, desmarque essa caixa. Escolha a assinatura e a região para selecionar o espaço de trabalho, que manterá os dados de monitoramento.
   5. Clique em "Avançar: grupos de teste" para adicionar grupos de teste

      ![Criar um monitor de conexão](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Na guia grupos de teste, clique em "+ grupo de teste" para adicionar um grupo de teste. Use a _criação de grupos de teste no monitor de conexão_ para adicionar grupos de teste. Clique em "revisar + criar" para examinar o monitor de conexão.

   ![Criar um monitor de conexão](./media/connection-monitor-2-preview/create-tg.png)

4. Na guia "revisar + criar", examine as informações básicas e os grupos de teste antes de criar o monitor de conexão. Para editar o monitor de conexão da exibição "revisar + criar":
   1. Para editar os detalhes básicos, use o ícone de lápis conforme especificado na caixa 1 na imagem 2
   2. Para editar os grupos de teste individuais, clique no grupo de teste que você deseja editar para abrir o grupo de teste no modo de edição.
   3. O custo/mês atual indicou o custo durante a visualização. Atualmente, não há cobrança para usar o monitor de conexão, portanto, essa coluna mostrará zero. O custo/mês real indicou o preço que será cobrado após a disponibilidade geral. Observe que os encargos de ingestão do log Analytics serão aplicados mesmo durante a visualização.

5. Na guia "revisar + criar", clique no botão "criar" para criar o monitor de conexão.

   ![Criar um monitor de conexão](./media/connection-monitor-2-preview/review-create-cm.png)

6.  O monitor de conexão (versão prévia) criará o recurso de monitor de conexão em segundo plano.

#### <a name="from-armclient"></a>De Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Criando grupos de teste no monitor de conexão

Cada grupo de teste no monitor de conexão inclui origens e destinos que são testados em parâmetros de rede de verificações com falha e RTT em configurações de teste.

#### <a name="from-portal"></a>Do portal

Para criar um grupo de teste em um monitor de conexão, especifique o valor para os campos mencionados abaixo:

1. Desabilitar grupo de teste – a verificação deste campo desabilitará o monitoramento de todas as origens e destinos especificados no grupo de teste. Você verá essa opção desmarcada por padrão.
2. Nome – nome do seu grupo de teste
3. Fontes – você pode especificar VMs do Azure e máquinas locais como fontes se os agentes estiverem instalados neles. Consulte a etapa 1 para instalar o agente específico para sua fonte.
   1. Clique na guia "agentes do Azure" para selecionar agentes do Azure. Você verá apenas as VMs listadas que estão associadas à região especificada no momento da criação do monitor de conexão. As VMs são agrupadas por padrão na assinatura à qual pertencem e os grupos são recolhidos. Você pode fazer uma busca detalhada no nível de assinaturas para outros níveis na hierarquia:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Você também pode alterar o valor do campo "Agrupar por" para iniciar a árvore de qualquer outro nível. Por exemplo: Group by – VNET mostrará as VMs com agentes na hierarquia VNETs-\&gt; Sub-redes-\&gt; VMs com agentes.

      ![Adicionar fontes](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Clique na guia "agentes não-Azure" para selecionar agentes locais. Por padrão, você verá agentes agrupados em espaços de trabalho em uma região. Somente os espaços de trabalho que têm a solução Monitor de Desempenho de Rede configurada serão listados. Adicionar a solução de Monitor de Desempenho de Rede ao workspace [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Você também pode usar o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Por padrão, você verá a região selecionada na guia informações básicas na exibição criar monitor de conexão. Você pode alterar a região e escolher agentes de espaços de trabalho na região selecionada recentemente. Você também pode alterar o valor do campo "Agrupar por" para agrupar em sub-redes.

      ![Fontes não Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Clique em "examinar" para examinar os agentes do Azure e não Azure selecionados.

      ![Examinar fontes](./media/connection-monitor-2-preview/review-sources.png)

   4. Clique em "concluído" quando terminar de selecionar as fontes.

4. Destinos – você pode monitorar a conectividade com as VMs do Azure ou qualquer ponto de extremidade (IP público, URL, FQDN) especificando-os como destinos. Em um único grupo de teste, você pode adicionar VMs do Azure, URLs do O365, URLs de D365 ou pontos de extremidade personalizados.

   1. Clique na guia "VMs do Azure" para selecionar VMs do Azure como destinos. Por padrão, você verá as VMs do Azure agrupadas na hierarquia de assinatura na mesma região que foi selecionada na guia informações básicas na exibição criar monitor de conexão. Você pode alterar a região e escolher VMs do Azure na região selecionada recentemente. Você pode fazer uma busca detalhada no nível de assinaturas para outros níveis na hierarquia, como os agentes do Azure.

      ![Adicionar destinos](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Adicionar destinos 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Clique na guia "pontos de extremidade" para selecionar pontos de extremidade como destinos. A lista de pontos de extremidade será preenchida com URLs de teste do O365 e do D365, agrupadas por nome.  Você também pode escolher um ponto de extremidade criado em outros grupos de teste no mesmo monitor de conexão. Para adicionar um novo ponto de extremidade, clique em "+ ponto de extremidade" no canto superior direito da tela e forneça a URL/IP/FQDN e o nome do ponto de extremidade

      ![Adicionar pontos de extremidade](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Clique em "examinar" para examinar os agentes do Azure e não Azure selecionados.
   4. Clique em "concluído" quando terminar de selecionar as fontes.

5. Configuração de teste – você pode associar qualquer número de configurações de teste em um determinado grupo de teste. O portal restringe-o a uma configuração de teste por grupo de teste, mas use Armclient para adicionar mais.
   1. Nome – nome da configuração de teste
   2. Protocolo – você pode escolher entre TCP, ICMP ou HTTP. Para alterar HTTP para HTTPS, selecione HTTP como protocolo e 443 como porta
   3. Criar configuração de teste de rede – você verá essa caixa de seleção somente se selecionar HTTP no campo protocolo. Habilite este campo para criar outra configuração de teste usando as mesmas origens e destinos especificados na etapa 3 e 4 sobre o protocolo TCP/ICMP. A configuração de teste criada recentemente é denominada "\&lt; nome especificado em 5. a\&gt;\_networkTestConfig "
   4. Desabilitar traceroute – este campo será aplicável a grupos de teste com TCP ou ICMP como protocolo.  Marque este campo para impedir que as fontes descubram a topologia e o tempo de ida e volta de salto a salto.
   5. Porta de destino – você pode personalizar esse campo para colocar em uma porta de destino de sua escolha.
   6. Frequência de teste – este campo decide com que frequência as fontes de ping serão destinos no protocolo e na porta especificada acima. Você pode escolher entre 30 segundos, 1 minuto, 5 minutos, 15 minutos e 30 minutos. As fontes testarão a conectividade com destinos com base no valor escolhido.  Por exemplo, se você selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em 30 segundos-período.
   7. Limites de integridade – você pode definir limites nos parâmetros de rede mencionados abaixo
      1. As verificações com falha em%-percentual de verificações falharam quando as origens verificam a conectividade com o destino nos critérios especificados acima. Para o protocolo TCP/ICMP, as verificações com falha em% podem ser equivalentes à% de perda de pacotes. Para o protocolo HTTP, esse campo representa o número de solicitações HTTP que não obtiveram uma resposta.
      2. RTT em milissegundos – tempo de ida e volta em milissegundos quando as fontes se conectam ao destino na configuração de teste especificada acima.

      ![Adicionar TG](./media/connection-monitor-2-preview/add-test-config.png)

Todas as origens e destinos adicionados a um grupo de teste com a configuração de teste especificada são divididos em testes individuais. Por exemplo:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configuração de teste: 2 (config 1, config 2)
* Testes criados: total = 12

| **Número do teste** | **Origem** | **Destino** | **Nome da configuração de teste** |
| --- | --- | --- | --- |
| 1 | Um | D | Configuração 1 |
| 2 | Um | D | Configuração 2 |
| 3 | Um | E | Configuração 1 |
| 4 | Um | E | Configuração 2 |
| 5 | B | D | Configuração 1 |
| 6 | B | D | Configuração 2 |
| 7 | B | E | Configuração 1 |
| 8 | B | E | Configuração 2 |
| 9 | C | D | Configuração 1 |
| 10 | C | D | Configuração 2 |
| 11 | C | E | Configuração 1 |
| 12 | C | E | Configuração 2 |

### <a name="scale-limits"></a>Limites de escala

* N º máximo de monitores de conexão por assinatura por região – 100
* N º máximo de grupos de teste por monitor de conexão-20
* N º máximo de origens + destinos por monitor de conexão – 100
* N º máximo de configurações de teste por monitor de conexão – 20 via Armclient. 2 por meio do Portal.

## <a name="step-4--data-analysis-and-alerts"></a>Etapa 4: análise de dados e alertas

Depois que um monitor de conexão é criado, as origens verificam a conectividade com os destinos com base na configuração de teste especificada.

### <a name="checks-in-a-test"></a>Verificações em um teste

Com base no protocolo selecionado por um usuário na configuração de teste, o monitor de conexão (versão prévia) executa uma série de verificações para o par de destino de origem sobre a frequência de teste escolhida.

Se HTTP for selecionado, o serviço calculará o número de respostas HTTP que retornaram um código de resposta para determinar as verificações com falha%.  Para calcular o RTT, medimos o tempo necessário para receber a resposta de uma chamada HTTP.

Se TCP ou ICMP for selecionado, o serviço calculará% do pacote para determinar as verificações com falha%. Para calcular o RTT, medimos o tempo necessário para receber a confirmação dos pacotes enviados. Se você habilitou os dados de traceroute para seus testes de rede, poderá ver a perda e a latência de saltos por salto para sua rede local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados retornados por cheques em um teste, cada teste pode ter os seguintes Estados:

* Pass = quando os valores reais das verificações falharam% e RTT estão dentro dos limites especificados
* Fail = quando os valores reais das verificações falharam% ou limites de RTT cruzado. Se nenhum limite for especificado, um teste será marcado como falha quando as verificações falharem% = 100%
* Aviso – quando os critérios das verificações com falha% não são especificados. Nesse caso, o monitor de conexão (versão prévia) usa um critério de conjunto automático como limite e quando esse limite é violado, o status do teste é definido como "aviso"

### <a name="data-collection-analysis-and-alerts"></a>Coleta de dados, análise e alertas

Todos os dados coletados pelo monitor de conexão (versão prévia) são armazenados no espaço de trabalho Log Analytics configurado no momento da criação do monitor de conexão. Os dados de monitoramento também estão disponíveis em métricas de Azure Monitor. Você pode usar Log Analytics para manter seus dados de monitoramento pelo tempo que desejar, mas Azure Monitor armazena métricas por padrão por 30 dias **.** Em seguida, você pode [definir alertas com base em métricas nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Monitorando painéis na solução de monitor de conexão

Você verá uma lista de seu monitor de conexão ao qual você tem acesso para uma determinada seleção de assinaturas, regiões, carimbo de data/hora, origem e tipos de destino.

Ao navegar até o monitor de conexão (versão prévia) do serviço observador de rede, você pode optar **por exibir**:

* Monitor de conexão (padrão) – lista de todos os monitores de conexão criados para assinaturas, regiões, carimbo de data/hora, fonte e tipos de destino escolhidos
* Grupos de teste – lista de todos os grupos de teste criados para assinaturas, regiões, carimbo de data/hora, fonte e tipos de destino escolhidos. Esses grupos de testes não são filtrados no monitor de conexão
* Testes – lista de todos os testes em execução para assinaturas, regiões, carimbo de data/hora, tipos de origem e destino escolhidos. Esses testes não são filtrados no monitor de conexão ou nos grupos de teste.

Você pode expandir cada monitor de conexão nos grupos de teste e em cada grupo de teste nos vários testes individuais que estão sendo executados nele no painel. Marcado como [1] na imagem abaixo.

Você pode filtrar essa lista com base em:

* Filtros de nível superior-assinaturas, regiões, fonte de carimbo de data/hora e tipos de destino. Marcado como [2] na imagem abaixo.
* Filtros baseados em estado-filtro de segundo nível no estado do monitor de conexão/grupo de teste/teste. Marcado como [3] na imagem abaixo.
* Campo de pesquisa – escolha "todos" para fazer uma pesquisa genérica. Para pesquisar em qualquer entidade específica, use a lista suspensa para restringir os resultados da pesquisa. Marcado como [4] na imagem abaixo.

![Filtrar testes](./media/connection-monitor-2-preview/cm-view.png)

Por exemplo:

1. Para examinar todos os testes em todo o monitor de conexão (versão prévia), em que IP de origem = 10.192.64.56:
   1. Alterar exibição de para "testes"
   2. Pesquisar arquivado = 10.192.64.56
   3. Use DropDown ao lado de Value para selecionar "sources"
2. Para filtrar apenas os testes com falha em todo o monitor de conexão (versão prévia), em que IP de origem = 10.192.64.56
   1. Alterar exibição de para "testes"
   2. Selecione "falha" em filtros baseados em estado.
   3. Campo de pesquisa = 10.192.64.56
   4. Use DropDown ao lado de Value para selecionar "sources"
3. Para filtrar apenas os testes com falha em todo o monitor de conexão (versão prévia) em que o destino é outlook.office365.com
   1. Alterar exibição de para "testes"
   2. Selecione "falha" em filtros baseados em estado.
   3. Campo de pesquisa = outlook.office365.com
   4. Use DropDown ao lado de valor para selecionar "destinos"

   ![Testes com falha](./media/connection-monitor-2-preview/tests-view.png)

Para exibir as tendências das verificações com falha% e RTT para:

1. Monitor de conexão
   1. Clique no monitor de conexão que você deseja investigar em detalhes
   2. Por padrão, você exibirá dados de monitoramento por "grupos de teste"

      ![Exibir métricas por](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Escolha o grupo de teste que você deseja investigar em detalhes

      ![Métricas por TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Você verá os 5 principais testes com falha nas verificações com falha% ou RTT MS para o grupo de teste que você escolheu na etapa anterior. Para cada teste, você verá linhas de tendência para verificações com falha% e RTT MS
   5. Selecione um teste na lista acima ou escolha outro teste para investigar em detalhes.
   6. Para o intervalo de tempo selecionado, para verificações com falha, você verá os valores de limite e reais. Para o RTT msec, você verá os valores de threshold, AVG, min e Max.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Alterar o intervalo de tempo para exibir mais dados
  8. Você pode alterar o modo de exibição na etapa b e escolher exibir por fontes, destinos ou configurações de teste. Em seguida, escolha uma fonte com base em testes com falha e investigue os 5 principais testes com falha.  Por exemplo: escolha Exibir por: origens e destinos para investigar todos os testes que são executados entre essa combinação no monitor de conexão selecionado.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Grupo de teste
   1. Clique no grupo de teste que você deseja investigar em detalhes
   2. Por padrão, você exibirá dados de monitoramento por "origem + destino + configuração de teste (teste)"

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Escolha o teste que você deseja investigar em detalhes
   4. Para o intervalo de tempo selecionado, para verificações com falha, você verá os valores de limite e reais. Para o RTT msec, você verá os valores de threshold, AVG, min e Max. Você também verá alertas acionados específicos para o teste selecionado.
   5. Alterar o intervalo de tempo para exibir mais dados
   6. Você pode alterar o modo de exibição na etapa b e escolher exibir por fontes, destinos ou configurações de teste. Em seguida, escolha uma entidade para investigar os 5 principais testes com falha.  Por exemplo: escolha Exibir por: origens e destinos para investigar todos os testes que são executados entre essa combinação no monitor de conexão selecionado.

3. Teste
   1. Clique na configuração de origem + destino + teste que você deseja investigar em detalhes
   2. Para o intervalo de tempo selecionado, para verificações com falha, você verá os valores de limite e reais. Para o RTT msec, você verá os valores de threshold, AVG, min e Max. Você também verá alertas acionados específicos para o teste selecionado.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. Você também pode clicar em "topologia" para ver a topologia de rede em qualquer ponto no tempo.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. Você pode clicar em qualquer salto do link para a rede do Azure para ver os problemas identificados pelo monitor de conexão. Esse recurso não está disponível para redes locais no momento.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Consultas de log em Azure Monitor Log Analytics

Use Log Analytics para criar exibições personalizadas de seus dados de monitoramento. Todos os dados exibidos na interface do usuário são populados a partir de Log Analytics. Você pode executar a análise interativa de dados no repositório e correlacionar dados de diferentes fontes, como a integridade do agente e outros aplicativos baseados em Log Analytics. Você também pode exportar os dados para o Excel, Power BI ou um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Para o monitor de conexão que foi criado antes da experiência do monitor de conexão (versão prévia), todas as quatro métricas estarão disponíveis. Para os monitores de conexão criados por meio da experiência do monitor de conexão (versão prévia), os dados estarão disponíveis apenas para as métricas marcadas com "(visualização)".

Tipo de recurso-Microsoft. Network/networkWatchers/connectionMonitors

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | Porcentagem de investigações com falha | Porcentagem | Média | Porcentagem de investigações de monitoramento de conectividade com falha | Sem dimensões |
| AverageRoundtripMs | Média de tempo de ida e volta (MS) | MilliSeconds | Média | Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino |             Sem dimensões |
| ChecksFailedPercent (visualização) | % De verificações com falha (versão prévia) | Porcentagem | Média | % das verificações com falha para um teste | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protocolo <br> * DestinationAddress <br> * Destinationname <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Região |
| RoundTripTimeMs (visualização) | Tempo de ida e volta (MS) (visualização) | Milissegundos | Média | Tempo de ida e volta (MS) para cheques enviados entre a origem e o destino. Este valor não é de média | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protocolo <br> * DestinationAddress <br> * Destinationname <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Região |

 ![Monitorar métricas](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Alertas de métrica no Azure Monitor

Para criar um alerta:

1. Escolha o recurso de monitor de conexão criado usando o monitor de conexão (versão prévia)
2. Verifique se "Metric" aparece como tipo de sinal para o recurso selecionado na etapa anterior
3. Em Adicionar condição, escolha nome do sinal como ChecksFailedPercent (visualização) ou RoundTripTimeMs (visualização) e tipo de sinal como métricas. Por exemplo: escolher ChecksFailedPercent (visualização)
4. Todas as dimensões aplicáveis de acordo com as métricas serão listadas.  Escolha o nome da dimensão e o valor da dimensão. Por exemplo: escolha o endereço de origem e forneça o endereço IP de qualquer fonte envolvida no recurso de monitor de conexão escolhido na etapa 1
5. Em lógica de alerta, escolha:
   1. Tipo de condição – estático
   2. Condição e limite
   3. Granularidade de agregação e frequência de avaliação – o monitor de conexão (versão prévia) atualiza os dados a cada 1 minuto.
6.  Em ações, escolha seu grupo de ação
7. Fornecer detalhes do alerta
8. Criar regra de alerta

   ![Alertas](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Etapa 5: diagnosticar problemas em sua rede

O monitor de conexão o ajudará a diagnosticar problemas correspondentes ao recurso de monitor de conexão e em sua rede. Os problemas em sua rede híbrida serão detectados pelos agentes de Log Analytics instalados na etapa 1 e os problemas no Azure serão detectados pela extensão do observador de rede.  Os problemas na rede híbrida estarão visíveis na página de diagnósticos e os problemas na rede do Azure ficarão visíveis na topologia de rede.

Para redes com VMs locais como fontes, detectamos:

* Tempo limite da solicitação expirado
* Ponto de extremidade não resolvido pelo DNS – temporário ou persistente. URL inválida.
* Nenhum host encontrado.
* A origem não pode se conectar ao destino. Destino não acessível por meio de ICMP.
* Problema relacionado ao certificado-certificado de cliente necessário para autenticar agente, lista de realocação de certificado não acessível, o nome do host do ponto de extremidade não corresponde à entidade do certificado ou ao nome alternativo da entidade, certificado raiz ausente no repositório de autoridades de certificação confiáveis do computador local da origem, certificado SSL expirado/inválido/revogado, incompatível

Para redes com VMs do Azure são fontes, detectamos:

* Problemas de agente – agente interrompido, resolução de DNS com falha, nenhum aplicativo/ouvinte escuta na porta de destino, não foi possível abrir o soquete
* Problemas de estado da VM – iniciando, parando, parado, desalocando, desalocando, reinicializando, não alocado
* Entrada de tabela ARP ausente
* Tráfego bloqueado devido a problemas de firewall local, regras de NSG
* Gateway de VNET – rotas ausentes, o túnel entre dois gateways está desconectado ou ausente ou o segundo gateway não foi encontrado pelo túnel, nenhuma informação de emparelhamento foi encontrada
* Rota ausente no MS Edge.
* O tráfego parou devido a rotas do sistema ou UDR
* BGP não habilitado na conexão de gateway
* Investigação de DIP para baixo na Load Balancer
