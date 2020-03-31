---
title: Monitor de Conexão (Preview) | Microsoft Docs
description: Saiba como usar o Connection Monitor (Preview) para monitorar a comunicação de rede em um ambiente distribuído.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599321"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoramento de conectividade de rede com monitor de conexão (Preview)

O Connection Monitor (Preview) fornece monitoramento unificado de conexão de ponta a ponta no Azure Network Watcher. O recurso Monitor de Conexão (Preview) suporta implantações híbridas e de nuvem Azure. O Network Watcher fornece ferramentas para monitorar, diagnosticar e visualizar métricas relacionadas à conectividade para suas implantações do Azure.

Aqui estão alguns casos de uso para Monitor de Conexão (Preview):

- Seu VM do servidor web front-end se comunica com um VM do servidor de banco de dados em um aplicativo de vários níveis. Você deseja verificar a conectividade de rede entre as duas VMs.
- Você quer VMs na região leste dos EUA para ping de VMs na região central dos EUA, e você quer comparar latências de rede entre regiões.
- Você tem vários escritórios no local em Seattle, Washington, e em Ashburn, Virgínia. Seus sites de escritório se conectam aos URLs do Office 365. Para seus usuários do Office 365 URLs, compare as latências entre Seattle e Ashburn.
- Seu aplicativo híbrido precisa de conectividade a um ponto final do Azure Storage. Seu site no local e seu aplicativo Azure se conectam ao mesmo ponto final do Azure Storage. Você deseja comparar as latências do site no local com as latências do aplicativo Azure.
- Você deseja verificar a conectividade entre suas configurações no local e as VMs do Azure que hospedam seu aplicativo em nuvem.

Em sua fase de visualização, o Monitor de Conexão combina o melhor de dois recursos: o recurso Network Watcher [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) e o recurso NPM [Service Connectivity Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) (Network Performance Monitor).

Aqui estão alguns benefícios do Monitor de Conexão (Preview):

* Experiência unificada e intuitiva para as necessidades de monitoramento do Azure e híbrido
* Monitoramento de conectividade entre regiões e espaço de trabalho
* Maiores frequências de sondagem e melhor visibilidade no desempenho da rede
* Alerta mais rápido para suas implantações híbridas
* Suporte para verificações de conectividade baseadas em HTTP, TCP e ICMP 
* Suporte a métricas e log analytics para configurações de teste azure e não-Azure

![Diagrama mostrando como o Monitor de Conexão interage com VMs do Azure, hosts não-Azure, pontos finais e locais de armazenamento de dados](./media/connection-monitor-2-preview/hero-graphic.png)

Para começar a usar o Connection Monitor (Preview) para monitoramento, siga estas etapas: 

1. Instale agentes de monitoramento.
1. Habilite o Network Watcher em sua assinatura.
1. Crie um monitor de conexão.
1. Configure análise de dados e alertas.
1. Diagnosticar problemas em sua rede.

As seções a seguir fornecem detalhes para essas etapas.

## <a name="install-monitoring-agents"></a>Instale agentes de monitoramento

O Monitor de Conexão conta com arquivos executáveis leves para executar verificações de conectividade.  Ele suporta verificações de conectividade tanto em ambientes do Azure quanto em ambientes locais. O arquivo executável que você usa depende se sua VM está hospedada no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais Azure

Para fazer com que o Monitor de Conexão reconheça suas VMs do Azure como fontes de monitoramento, instale a extensão da máquina virtual do Agente de Observador de Rede neles. Esta extensão também é conhecida como *extensão Do Observador de Rede*. As máquinas virtuais do Azure exigem a extensão para ativar o monitoramento de ponta a ponta e outras funcionalidades avançadas. 

Você pode instalar a extensão 'Observador de rede' ao [criar uma VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Você também pode instalar, configurar e solucionar problemas separadamente a extensão Do Observador de Rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Regras para um grupo de segurança de rede (NSG) ou firewall podem bloquear a comunicação entre a origem e o destino. O Monitor de Conexão detecta esse problema e o mostra como uma mensagem de diagnóstico na topologia. Para permitir o monitoramento de conexões, certifique-se de que as regras de NSG e firewall permitam pacotes sobre TCP ou ICMP entre a origem e o destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para máquinas no local

Para fazer com que o Monitor de Conexão reconheça suas máquinas locais como fontes de monitoramento, instale o agente Log Analytics nas máquinas. Em seguida, habilite a solução Network Performance Monitor. Esses agentes estão vinculados aos espaços de trabalho do Log Analytics, então você precisa configurar o ID do espaço de trabalho e a chave principal antes que os agentes possam iniciar o monitoramento.

Para instalar o agente Log Analytics para máquinas Windows, consulte [a extensão da máquina virtual do Azure Monitor para Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se o caminho incluir firewalls ou NVAs (Network Virtual Appliances), certifique-se de que o destino é acessível.

## <a name="enable-network-watcher-on-your-subscription"></a>Habilite o Network Watcher em sua assinatura

Todas as assinaturas que possuem uma rede virtual são habilitadas com o Network Watcher. Quando você cria uma rede virtual em sua assinatura, o Network Watcher é automaticamente habilitado na região e assinatura da rede virtual. Essa ativação automática não afeta seus recursos ou incorre em uma cobrança. Certifique-se de que o Network Watcher não está explicitamente desativado em sua assinatura. 

Para obter mais informações, consulte [Ativar observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão 

O Monitor de Conexão monitora a comunicação em intervalos regulares. Informa sobre mudanças na acessibilidade e latência. Você também pode verificar a topologia atual e histórica da rede entre agentes de origem e pontos finais de destino.

As fontes podem ser VMs azure ou máquinas locais que possuem um agente de monitoramento instalado. Os pontos finais de destino podem ser URLs office 365, DYNAMICS 365 URLs, URLs personalizados, IDs de recursos Do Zure VM, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="access-connection-monitor-preview"></a>Monitor de conexão de acesso (Visualização)

1. Na página inicial do portal Azure, vá para **Network Watcher**.
1. À esquerda, na seção **Monitoramento,** selecione **Monitor de conexão (Preview)**.
1. Você vê todos os monitores de conexão que foram criados no Monitor de Conexão (Preview). Para ver os monitores de conexão que foram criados na experiência clássica do Monitor de Conexão, acesse a guia **Monitor de Conexão.**

    ![Captura de tela mostrando monitores de conexão que foram criados no Connection Monitor (Preview)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

Nos monitores de conexão que você cria no Connection Monitor (Preview), você pode adicionar máquinas no local e VMs Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade para os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Conexão (Preview) inclui as seguintes entidades:

* **Recurso de monitor de conexão** – Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de conexão.
* **Endpoint** – Uma fonte ou destino que participa de verificações de conectividade. Exemplos de pontos finais incluem VMs do Azure, agentes no local, URLs e IPs.
* **Configuração do teste** – Uma configuração específica do protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, limiares, freqüência de teste e outros parâmetros.
* **Grupo de teste** – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o tempo de ida e volta (RTT).

 ![Diagrama mostrando um monitor de conexão, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Crie um monitor de conexão a partir do portal Azure

Para criar um monitor de conexão a partir do portal Azure, siga estas etapas:

1. No painel **Monitor de conexão (Preview),** no canto superior esquerdo, selecione **Criar**.
1. Na guia **Noções Básicas,** insira informações para o monitor de conexão:
   * **Nome do monitor de conexão** – Adicione o nome do monitor de conexão. Use as regras padrão de nomeação para os recursos do Azure.
   * **Assinatura** – Escolha uma assinatura para o monitor de conexão.
   * **Região** – Escolha uma região para o monitor de conexão. Você pode selecionar apenas as VMs de origem criadas nesta região.
   * **Configuração do espaço de trabalho** - Seu espaço de trabalho contém seus dados de monitoramento. Você pode usar um espaço de trabalho personalizado ou o espaço de trabalho padrão. 
       * Para usar o espaço de trabalho padrão, selecione a caixa de seleção. 
       * Para escolher um espaço de trabalho personalizado, limpe a caixa de seleção. Em seguida, escolha a assinatura e a região para o seu espaço de trabalho personalizado. 
1. Na parte inferior da guia, selecione **Next: Grupos de teste**.

   ![Captura de tela mostrando a guia Básico no Monitor de Conexão](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na guia **Grupos de teste,** selecione **+ Grupo de teste**. Para configurar seus grupos de teste, consulte [Criar grupos de teste no Monitor de Conexão](#create-test-groups-in-a-connection-monitor). 
1. Na parte inferior da guia, selecione **Next: Review + create** para rever seu monitor de conexão.

   ![Captura de tela mostrando a guia Grupos de teste e o painel onde você adiciona detalhes do grupo de teste](./media/connection-monitor-2-preview/create-tg.png)

1. Na guia **'Revisar + criar',** revise as informações básicas e os grupos de teste antes de criar o monitor de conexão. Se você precisar editar o monitor de conexão:
   * Para editar detalhes básicos, selecione o ícone do lápis.
   * Para editar um grupo de teste, selecione-o.

   > [!NOTE] 
   > A guia **'Revisão + criar'** mostra o custo por mês durante a fase de visualização do Monitor de Conexão. Atualmente, a coluna **CUSTO ATUAL** não mostra nenhum custo. Quando o Monitor de Conexão estiver disponível, esta coluna mostrará uma taxa mensal. 
   > 
   > Mesmo no estágio de visualização do Monitor de Conexão, as taxas de ingestão do Log Analytics são aplicadas.

1. Quando estiver pronto para criar o monitor de conexão, na parte inferior da guia **'Revisar + criar',** selecione **Criar**.

   ![Captura de tela do Monitor de Conexão, mostrando a guia Review + create](./media/connection-monitor-2-preview/review-create-cm.png)

O Connection Monitor (Preview) cria o recurso do monitor de conexão em segundo plano.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Crie um monitor de conexão usando o ARMClient

Use o seguinte código para criar um monitor de conexão usando armclient.

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

address: '\&lt;FQDN of your on-premises agent'

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

Aqui está o comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste em um monitor de conexão

Cada grupo de teste em um monitor de conexão inclui fontes e destinos que são testados em parâmetros de rede. Eles são testados para a porcentagem de verificações que falham e o RTT sobre as configurações de teste.

A partir do portal Azure, para criar um grupo de teste em um monitor de conexão, você especifica valores para os seguintes campos:

* **Desativar grupo de teste** – Você pode selecionar este campo para desativar o monitoramento de todas as fontes e destinos especificados pelo grupo de teste. Esta seleção é eliminada por padrão.
* **Nome** – Nomeie seu grupo de teste.
* **Fontes** – Você pode especificar tanto as VMs do Azure quanto as máquinas locais como fontes se os agentes estiverem instalados neles. Para instalar um agente para sua fonte, consulte [Instalar agentes de monitoramento](#install-monitoring-agents).
   * Para escolher os agentes do Azure, selecione a guia **Agentes do Azure.** Aqui você vê apenas VMs que estão vinculados à região que você especificou quando criou o monitor de conexão. Por padrão, as VMs são agrupadas na assinatura a que pertencem. Esses grupos entraram em colapso. 
   
       Você pode detalhar do nível de assinatura para outros níveis na hierarquia:

      **Grupos** > **de recursos de** > assinatura**VNETs** > **Subnets** > **VMs com agentes**

      Você também pode alterar o valor do **Grupo por** campo para iniciar a árvore de qualquer outro nível. Por exemplo, se você agrupar por rede virtual, verá as VMs que têm agentes na hierarquia **VNETs** > **Subnets** > **VMs com agentes**.

      ![Captura de tela do Monitor de Conexão, mostrando o painel Adicionar fontes e a guia Agentes do Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Para escolher os agentes no local, selecione a **guia Não-Azure Agents.** Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos esses espaços de trabalho têm a solução Network Performance Monitor configurada. 
   
       Se você precisar adicionar o Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar o Network Performance Monitor, consulte [soluções de monitoramento no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Na **exibição Criar monitor de conexão,** na guia **Básico,** a região padrão é selecionada. Se você mudar a região, você pode escolher agentes de espaços de trabalho na nova região. Você também pode alterar o valor do **Grupo por** campo para grupo por sub-redes.

      ![Captura de tela do Monitor de Conexão, mostrando o painel Adicionar fontes e a guia Não-Azure Agents](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Para revisar os agentes Azure e non-Azure selecionados, vá para a guia **'Revisar'.**

      ![Captura de tela do Monitor de Conexão, mostrando o painel Adicionar fontes e a guia Revisão](./media/connection-monitor-2-preview/review-sources.png)

   * Quando terminar de configurar fontes, na parte inferior do painel **Adicionar fontes,** selecione **Concluído**.

* **Destinos** – Você pode monitorar a conectividade com as VMs do Azure ou qualquer ponto final (um IP público, URL ou FQDN) especificando-as como destinos. Em um único grupo de teste, você pode adicionar VMs Azure, URLs office 365, URLs Dynamics 365 e pontos finais personalizados.

    * Para escolher as VMs do Azure como destinos, selecione a guia **VMs do Azure.** Por padrão, as VMs do Azure são agrupadas em uma hierarquia de assinatura que está na mesma região que você selecionou na exibição **Criar monitor de conexão,** na guia **Básico.** Você pode alterar a região e escolher As VMs do Azure da região recém-selecionada. Em seguida, você pode detalhar do nível de assinatura para outros níveis na hierarquia, como o nível de Agentes Azure.

       ![Captura de tela do painel Adicionar destinos, mostrando a guia VMs do Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Captura de tela do painel Adicionar destinos, mostrando o nível de assinatura](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Para escolher pontos finais como destinos, selecione a guia **Endpoints.** A lista de pontos finais inclui URLs de teste do Office 365 e URLs de teste Dynamics 365, agrupados por nome. Além desses pontos finais, você pode escolher um ponto final que foi criado em outros grupos de teste no mesmo monitor de conexão. 
    
        Para adicionar um novo ponto final, no canto superior direito, selecione **+ Pontos finais**. Em seguida, forneça um nome de ponto final e URL, IP ou FQDN.

       ![Captura de tela mostrando onde adicionar pontos finais como destinos no Monitor de Conexão](./media/connection-monitor-2-preview/add-endpoints.png)

    * Para revisar as VMs do Azure e os pontos finais escolhidos, selecione a guia **'Revisar'.**
    * Quando terminar de escolher destinos, selecione **Concluído**.

* **Configurações de teste** – Você pode associar configurações de teste em um grupo de teste. O portal Azure permite apenas uma configuração de teste por grupo de teste, mas você pode usar o ARMClient para adicionar mais.

    * **Nome** – Nomeie a configuração do teste.
    * **Protocolo** – Escolha TCP, ICMP ou HTTP. Para alterar HTTP para HTTPS, selecione **HTTP** como protocolo e selecione **443** como porta.
        * **Criar configuração de teste de rede** – Esta caixa de seleção só será exibida se você selecionar **HTTP** no campo **Protocolo.** Selecione esta caixa para criar outra configuração de teste que use as mesmas fontes e destinos que você especificou em outros lugares da sua configuração. A configuração de teste `<the name of your test configuration>_networkTestConfig`recém-criada é nomeada .
        * **Desativar traceroute** – Este campo se aplica a grupos de teste cujo protocolo é TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram topologia e RTT hop-by-hop.
    * **Porto de destino** – Você pode personalizar este campo com um porto de destino de sua escolha.
    * **Frequência de teste** – Use este campo para escolher com que freqüência as fontes pingarão destinos no protocolo e na porta que você especificou. Você pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. As fontes testarão a conectividade com destinos com base no valor escolhido.  Por exemplo, se você selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em um período de 30 segundos.
    * **Limite de sucesso** – Você pode definir limites nos seguintes parâmetros de rede:
       * **Verificações falhadas** – Defina a porcentagem de verificações que podem falhar quando as fontes verificam a conectividade aos destinos usando os critérios especificados. Para o protocolo TCP ou ICMP, a porcentagem de verificações com falha pode ser equiparada à porcentagem de perda de pacotes. Para o protocolo HTTP, este campo representa a porcentagem de solicitações HTTP que não receberam resposta.
       * **Tempo de ida e volta** – Defina o RTT em milissegundos para quanto tempo as fontes podem levar para se conectar ao destino durante a configuração do teste.
    
       ![Captura de tela mostrando onde configurar uma configuração de teste no Monitor de Conexão](./media/connection-monitor-2-preview/add-test-config.png)

Todas as fontes, destinos e configurações de teste que você adiciona a um grupo de teste são divididas em testes individuais. Aqui está um exemplo de como as fontes e destinos são divididos:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configurações de teste: 2 (Config 1, Config 2)
* Total de testes criados: 12

| Número do teste | Fonte | Destino | Configuração de teste |
| --- | --- | --- | --- |
| 1 | Um | D | Config 1 |
| 2 | Um | D | Config 2 |
| 3 | Um | E | Config 1 |
| 4 | Um | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Monitores máximos de conexão por assinatura por região: 100
* Grupos máximos de teste por monitor de conexão: 20
* Fontes e destinos máximos por monitor de conexão: 100
* Configurações máximas de teste por monitor de conexão: 
    * 20 via ARMClient
    * 2 através do portal Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analisar dados de monitoramento e definir alertas

Depois de criar um monitor de conexão, as fontes verificam a conectividade com destinos com base na configuração do teste.

### <a name="checks-in-a-test"></a>Verifica em um teste

Com base no protocolo que você escolheu na configuração do teste, o Connection Monitor (Preview) executa uma série de verificações para o par de destino de origem. As verificações são de acordo com a freqüência de teste que você escolheu.

Se você usar HTTP, o serviço calculará o número de respostas HTTP que retornaram um código de resposta. O resultado determina a porcentagem de verificações falhadas. Para calcular o RTT, o serviço mede o tempo entre uma chamada HTTP e a resposta.

Se você usar TCP ou ICMP, o serviço calculará a porcentagem de perda de pacote para determinar a porcentagem de verificações com falha. Para calcular o RTT, o serviço mede o tempo de recebimento do reconhecimento (ACK) para os pacotes que foram enviados. Se você habilitou dados de traceroute para seus testes de rede, poderá ver a perda e a latência de hop-by-hop para sua rede local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados que as verificações retornam, os testes podem ter os seguintes estados:

* **Passe** – Os valores reais para a porcentagem de verificações com falha e RTT estão dentro dos limites especificados.
* **Falha** – Os valores reais para a porcentagem de verificações com falha ou RTT excederam os limites especificados. Se nenhum limite for especificado, um teste atingirá o estado de Falha quando a porcentagem de verificações com falha for de 100.
* **Aviso** – Não foram especificados critérios para a porcentagem de verificações reprovadas. Na ausência de critérios especificados, o Monitor de Conexão (Preview) atribui automaticamente um limite. Quando esse limite é excedido, o status do teste muda para Aviso.

### <a name="data-collection-analysis-and-alerts"></a>Coleta de dados, análise e alertas

Os dados coletados pelo Connection Monitor (Preview) são armazenados no espaço de trabalho do Log Analytics. Você configura este espaço de trabalho quando criou o monitor de conexão. 

Os dados de monitoramento também estão disponíveis no Azure Monitor Metrics. Você pode usar o Log Analytics para manter seus dados de monitoramento pelo tempo que quiser. O Azure Monitor armazena métricas por apenas 30 dias por padrão. 

Você pode [definir alertas baseados em métricas nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Painéis de monitoramento

Nos painéis de monitoramento, você vê uma lista dos monitores de conexão que você pode acessar para suas assinaturas, regiões, carimbos de horário, fontes e tipos de destino.

Quando você vai para o Monitor de Conexão (Preview) do Network Watcher, você pode visualizar dados por:

* **Monitor de conexão** – Lista de todos os monitores de conexão criados para suas assinaturas, regiões, carimbos de tempo, fontes e tipos de destino. Este modo de exibição é o padrão.
* **Grupos de teste** – Lista de todos os grupos de teste criados para suas assinaturas, regiões, carimbos de tempo, fontes e tipos de destino. Esses grupos de teste não são filtrados por monitores de conexão.
* **Teste** – Lista de todos os testes executados para suas assinaturas, regiões, carimbos de horário, fontes e tipos de destino. Estes testes não são filtrados por monitores de conexão ou grupos de teste.

Na imagem a seguir, as três visualizações de dados são indicadas pela seta 1.

No painel, você pode expandir cada monitor de conexão para ver seus grupos de teste. Então você pode expandir cada grupo de teste para ver os testes que são executados nele. 

Você pode filtrar uma lista com base em:

* **Filtros de nível superior** – Escolha assinaturas, regiões, fontes de carimbo de tempo e tipos de destino. Veja a caixa 2 na imagem a seguir.
* **Filtros baseados no estado** – Filtrar pelo estado do monitor de conexão, grupo de teste ou teste. Veja a seta 3 na imagem a seguir.
* **Filtros personalizados** – Escolha **Selecionar tudo** para fazer uma pesquisa genérica. Para pesquisar por uma entidade específica, selecione na lista de paradas. Veja a seta 4 na imagem a seguir.

![Captura de tela mostrando como filtrar visualizações de monitores de conexão, grupos de teste e testes no Monitor de Conexão (Preview)](./media/connection-monitor-2-preview/cm-view.png)

Por exemplo, para olhar todos os testes no Monitor de Conexão (Preview) onde o IP de origem é 10.192.64.56:
1. Alterar a visualização para **Teste**.
1. No campo de busca, tipo *10.192.64.56*
1. Na lista de desímparadas, selecione **Fontes**.

Para mostrar apenas testes com falha no Monitor de Conexão (Preview) onde o IP de origem é 10.192.64.56:
1. Alterar a visualização para **Teste**.
1. Para o filtro baseado em estado, **selecione Fail**.
1. No campo de busca, tipo *10.192.64.56*
1. Na lista de desímparadas, selecione **Fontes**.

Para mostrar apenas testes falhados no Connection Monitor (Preview) onde o destino está outlook.office365.com:
1. Alterar a exibição para **Teste**.
1. Para o filtro baseado em estado, **selecione Fail**.
1. No campo de busca, digite *outlook.office365.com*
1. Na lista de itens de si, selecione **Destinos**.

   ![Captura de tela mostrando uma exibição filtrada para mostrar apenas testes falhos para o destino Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Para visualizar as tendências no RTT e a porcentagem de verificações falhadas para um monitor de conexão:
1. Selecione o monitor de conexão que deseja investigar. Por padrão, os dados de monitoramento são organizados por grupo de teste.

   ![Captura de tela mostrando métricas para um monitor de conexão, exibida por grupo de teste](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Escolha o grupo de teste que deseja investigar.

   ![Captura de tela mostrando onde selecionar um grupo de teste](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Você vê os cinco principais testes reprovados do seu grupo de teste, com base no RTT ou percentual de verificações falhadas. Para cada teste, você verá o RTT e as linhas de tendência para a porcentagem de verificações falhadas.
1. Selecione um teste na lista ou escolha outro teste para investigar. Para o seu intervalo de tempo e a porcentagem de verificações falhadas, você vê limiar e valores reais. Para RTT, você vê os valores para limiar, médio, mínimo e máximo.

   ![Captura de tela mostrando os resultados de um teste para RTT e porcentagem de verificações reprovadas](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Alterar o intervalo de tempo para visualizar mais dados.
1. Altere a exibição para ver fontes, destinos ou configurações de teste. 
1. Escolha uma fonte baseada em testes fracassados e investigue os cinco primeiros testes reprovados. Por exemplo, escolha **Exibir por** > **Fontes** e **Exibir por** > Destinos para investigar os testes**relevantes** no monitor de conexão.

   ![Captura de tela mostrando métricas de desempenho para os cinco primeiros testes com falha](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Para visualizar as tendências em RTT e a porcentagem de verificações falhadas para um grupo de teste:

1. Selecione o grupo de teste que deseja investigar. 

    Por padrão, os dados de monitoramento são organizados por fontes, destinos e configurações de teste (testes). Mais tarde, você pode alterar a exibição de grupos de teste para fontes, destinos ou configurações de teste. Em seguida, escolha uma entidade para investigar os cinco principais testes reprovados. Por exemplo, altere a visão para fontes e destinos para investigar os testes relevantes no monitor de conexão selecionado.
1. Escolha o teste que deseja investigar.

   ![Captura de tela mostrando onde selecionar um teste](./media/connection-monitor-2-preview/tg-drill.png)

    Para o seu intervalo de tempo e para sua porcentagem de verificações falhadas, você vê valores limiares e valores reais. Para RTT, você vê valores para limiar, médio, mínimo e máximo. Você também vê alertas de disparo para o teste que você selecionou.
1. Alterar o intervalo de tempo para visualizar mais dados.

Para visualizar as tendências em RTT e a porcentagem de verificações reprovadas para um teste:
1. Selecione a configuração de origem, destino e teste que deseja investigar.

    Para o seu intervalo de tempo e para a porcentagem de verificações falhadas, você vê valores limiares e valores reais. Para RTT, você vê valores para limiar, médio, mínimo e máximo. Você também vê alertas de disparo para o teste que você selecionou.

   ![Captura de tela mostrando métricas para um teste](./media/connection-monitor-2-preview/test-drill.png)

1. Para ver a topologia da rede, selecione **Topologia**.

   ![Captura de tela mostrando a guia Topologia da rede](./media/connection-monitor-2-preview/test-topo.png)

1. Para ver os problemas identificados, na topologia, selecione qualquer salto no caminho. (Esses lúpulos são recursos do Azure.) Essa funcionalidade não está disponível no momento para redes locais.

   ![Captura de tela mostrando um link de salto selecionado na guia Topologia](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Consultas de log no Log Analytics

Use o Log Analytics para criar visualizações personalizadas de seus dados de monitoramento. Todos os dados que a ui exibe são do Log Analytics. Você pode analisar interativamente os dados no repositório. Correlacionar os dados do Agent Health ou outras soluções baseadas no Log Analytics. Exporte os dados para excel ou Power BI ou crie um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Nos monitores de conexão criados antes da experiência do Monitor de Conexão (Preview), todas as quatro métricas estão disponíveis: % Testes falhou, AverageRoundtripMs, ChecksFailedPercent (Preview) e RoundTripTimeMs (Preview). Nos monitores de conexão criados na experiência Do Monitor de Conexão (Preview), os dados estão disponíveis apenas para as métricas marcadas com *(Preview)*.

![Captura de tela mostrando métricas no Monitor de Conexão (Preview)](./media/connection-monitor-2-preview/monitor-metrics.png)

Quando você usar métricas, defina o tipo de recurso como Microsoft.Network/networkWatchers/connectionMonitors

| Métrica | Nome de exibição | Unidade | Tipo de agregação | Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | Porcentagem de investigações com falha | Porcentagem | Média | A porcentagem de testes de monitoramento de conectividade falhou. | Sem dimensões |
| AverageRoundtripMs | Avg. Tempo de ida e volta (ms) | Milissegundos | Média | RTT de rede média para testes de monitoramento de conectividade enviados entre origem e destino. |             Sem dimensões |
| ChecksFailedPercent (Visualização) | % Verificações falhadas (Visualização) | Porcentagem | Média | Porcentagem de cheques reprovados para um teste. | ConexãoMonitorResourceId <br>Endereço de origem <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>ResourceId de destino <br>DestinationType <br>DestinationPort <br>TestGroupName <br>Nome da configuração do teste <br>Região |
| RoundTripTimeMs (Pré-visualização) | Tempo de ida e volta (ms) (Visualização) | Milissegundos | Média | RTT para verificações enviadas entre a fonte e o destino. Esse valor não é mediano. | ConexãoMonitorResourceId <br>Endereço de origem <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>ResourceId de destino <br>DestinationType <br>DestinationPort <br>TestGroupName <br>Nome da configuração do teste <br>Região |

#### <a name="metric-alerts-in-azure-monitor"></a>Alertas métricos no Monitor Do Azure

Para criar um alerta no Monitor do Azure:

1. Escolha o recurso do monitor de conexão que você criou no Monitor de Conexão (Preview).
1. Certifique-se de que **métrica** aparece como tipo de sinal para o monitor de conexão.
1. Em **Adicionar Condição**, para o nome do **sinal,** selecione **ChecksFailedPercent (Preview)** ou **RoundTripTimeMs(Preview)**.
1. Para **tipo de sinal,** escolha **Métricas**. Por exemplo, selecione **ChecksFailedPercent (Visualização)**.
1. Todas as dimensões da métrica estão listadas. Escolha o nome da dimensão e o valor da dimensão. Por exemplo, selecione **Endereço de origem** e digite o endereço IP de qualquer fonte no monitor de conexão.
1. Em **Alert Logic,** preencha os seguintes detalhes:
   * **Tipo de condição**: **Estática**.
   * **Condição** e **Limiar**.
   * **Agregação Granularity and Frequency of Evaluation**: Connection Monitor (Preview) atualiza dados a cada minuto.
1. Em **Ações,** escolha seu grupo de ação.
1. Forneça detalhes de alerta.
1. Criar a regra de alerta.

   ![Captura de tela mostrando a área de regra Criar no Monitor Do Azure; "Endereço de origem" e "Nome do ponto final da fonte" são destacados](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas em sua rede

O Monitor de Conexão (Preview) ajuda a diagnosticar problemas no monitor de conexão e na rede. Os problemas em sua rede híbrida são detectados pelos agentes log analytics que você instalou anteriormente. Os problemas no Azure são detectados pela extensão Do Observador de Rede. 

Você pode ver problemas na rede Azure na topologia da rede.

Para redes cujas fontes são VMs no local, os seguintes problemas podem ser detectados:

* Atingiu tempo limite solicitado.
* Ponto final não resolvido pelo DNS – temporário ou persistente. URL inválido.
* Nenhum hospedeiro foi encontrado.
* Fonte incapaz de se conectar ao destino. Alvo não acessível através do ICMP.
* Problemas relacionados a certificados: 
    * Certificado de cliente necessário para autenticar agente. 
    * A lista de realocação de certificados não está acessível. 
    * O nome do host do ponto final não corresponde ao assunto do certificado ou ao nome alternativo do assunto. 
    * O certificado raiz está faltando na loja local de certificação confiável de computadores da fonte. 
    * O certificado SSL é expirado, inválido, revogado ou incompatível.

Para redes cujas fontes são VMs Azure, os seguintes problemas podem ser detectados:

* Problemas com agentes:
    * O agente parou.
    * Resolução DNS falhada.
    * Nenhum aplicativo ou ouvinte ouvindo na porta de destino.
    * O soquete não pôde ser aberto.
* Problemas de estado vm: 
    * Iniciando
    * Parando
    * Parado
    * Desalocando
    * Desalocada
    * Reiniciar
    * Não alocado
* A entrada da tabela ARP está faltando.
* O tráfego foi bloqueado por causa de problemas de firewall locais ou regras do NSG.
* Problemas de gateway de rede virtual: 
    * Rotas perdidas.
    * O túnel entre dois gateways está desconectado ou faltando.
    * O segundo portal não foi encontrado perto do túnel.
    * Nenhuma informação de sicronia foi encontrada.
* A rota estava faltando no Microsoft Edge.
* O trânsito parou por causa das rotas do sistema ou UDR.
* O BGP não está habilitado na conexão gateway.
* A sonda DIP está no balanceador de carga.
