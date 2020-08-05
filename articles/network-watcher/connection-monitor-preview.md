---
title: Monitor de conexão (visualização) | Microsoft Docs
description: Saiba como usar o monitor de conexão (versão prévia) para monitorar a comunicação de rede em um ambiente distribuído.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 0cb51cd224145e7fe359e2b14a87ed2b87b18c26
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563017"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoramento de conectividade de rede com o monitor de conexão (versão prévia)

O monitor de conexão (versão prévia) fornece monitoramento de conexão de ponta a ponta unificado no observador de rede do Azure. O recurso monitor de conexão (versão prévia) dá suporte a implantações de nuvem híbridas e do Azure. O observador de rede fornece ferramentas para monitorar, diagnosticar e exibir métricas relacionadas à conectividade para suas implantações do Azure.

Aqui estão alguns casos de uso para o monitor de conexão (versão prévia):

- A VM do servidor Web front-end se comunica com uma VM do servidor de banco de dados em um aplicativo de várias camadas. Você deseja verificar a conectividade de rede entre as duas VMs.
- Você deseja que as VMs na região leste dos EUA executem ping nas VMs na região EUA Central e você deseja comparar as latências de rede entre regiões.
- Você tem vários sites locais do Office em Seattle, Washington, e no Ashburn, Virgínia. Seus sites do Office se conectam às URLs do Office 365. Para seus usuários de URLs do Office 365, compare as latências entre Seattle e Ashburn.
- Seu aplicativo híbrido precisa de conectividade com um ponto de extremidade de armazenamento do Azure. Seu site local e seu aplicativo do Azure se conectam ao mesmo ponto de extremidade de armazenamento do Azure. Você deseja comparar as latências do site local com as latências do aplicativo do Azure.
- Você deseja verificar a conectividade entre suas configurações locais e as VMs do Azure que hospedam seu aplicativo de nuvem.

Em sua fase de visualização, o monitor de conexão combina o melhor de dois recursos: o recurso monitor de [conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) do observador de rede e o recurso de [Monitor de conectividade do serviço](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) monitor de desempenho de rede (NPM).

Aqui estão alguns benefícios do monitor de conexão (visualização):

* Experiência unificada e intuitiva para necessidades de monitoramento híbrido e do Azure
* Monitoramento de conectividade entre regiões e entre espaços de trabalho
* Frequências de investigação mais altas e melhor visibilidade do desempenho da rede
* Alertas mais rápidos para suas implantações híbridas
* Suporte para verificações de conectividade baseadas em HTTP, TCP e ICMP 
* Métricas e suporte Log Analytics para configurações de teste do Azure e não Azure

![Diagrama mostrando como o monitor de conexão interage com VMs do Azure, hosts não Azure, pontos de extremidade e locais de armazenamento de dados](./media/connection-monitor-2-preview/hero-graphic.png)

Para começar a usar o monitor de conexão (versão prévia) para monitoramento, siga estas etapas: 

1. Instale os agentes de monitoramento.
1. Habilite o observador de rede em sua assinatura.
1. Crie um monitor de conexão.
1. Configure a análise de dados e alertas.
1. Diagnostique problemas em sua rede.

As seções a seguir fornecem detalhes para essas etapas.

## <a name="install-monitoring-agents"></a>Instalar agentes de monitoramento

O monitor de conexão depende de arquivos executáveis leves para executar verificações de conectividade.  Ele dá suporte a verificações de conectividade de ambientes do Azure e ambientes locais. O arquivo executável que você usa depende se sua VM está hospedada no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais do Azure

Para fazer com que o monitor de conexão reconheça suas VMs do Azure como fontes de monitoramento, instale a extensão da máquina virtual do agente do observador de rede neles. Essa extensão também é conhecida como a *extensão do observador de rede*. As máquinas virtuais do Azure exigem a extensão para disparar o monitoramento de ponta a ponta e outras funcionalidades avançadas. 

Você pode instalar a extensão do observador de rede ao [criar uma VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Você também pode instalar, configurar e solucionar problemas separadamente da extensão do observador de rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

As regras para um grupo de segurança de rede (NSG) ou firewall podem bloquear a comunicação entre a origem e o destino. O monitor de conexão detecta esse problema e o mostra como uma mensagem de diagnóstico na topologia. Para habilitar o monitoramento de conexão, verifique se as regras de firewall e NSG permitem pacotes via TCP ou ICMP entre a origem e o destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para computadores locais

Para fazer com que o monitor de conexão reconheça seus computadores locais como fontes de monitoramento, instale o agente de Log Analytics nos computadores. Em seguida, habilite a solução Monitor de Desempenho de Rede. Esses agentes são vinculados a espaços de trabalho do Log Analytics, portanto, você precisa configurar a ID do espaço de trabalho e a chave primária antes que os agentes possam iniciar o monitoramento.

Para instalar o agente de Log Analytics para computadores Windows, consulte [Azure monitor extensão de máquina virtual para Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se o caminho incluir firewalls ou NVAs (soluções de virtualização de rede), verifique se o destino está acessível.

## <a name="enable-network-watcher-on-your-subscription"></a>Habilitar o observador de rede em sua assinatura

Todas as assinaturas que têm uma rede virtual são habilitadas com o observador de rede. Quando você cria uma rede virtual em sua assinatura, o observador de rede é habilitado automaticamente na região e na assinatura da rede virtual. Essa habilitação automática não afeta seus recursos ou incorre em um encargo. Verifique se o observador de rede não está explicitamente desabilitado em sua assinatura. 

Para obter mais informações, consulte [habilitar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão 

O monitor de conexão monitora a comunicação em intervalos regulares. Ele informa sobre alterações na acessibilidade e na latência. Você também pode verificar a topologia de rede atual e histórica entre os agentes de origem e os pontos de extremidade de destino.

As fontes podem ser VMs do Azure ou computadores locais que têm um agente de monitoramento instalado. Os pontos de extremidade de destino podem ser URLs do Office 365, URLs do Dynamics 365, URLs personalizadas, IDs de recursos de VM do Azure, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="access-connection-monitor-preview"></a>Monitor de conexão de acesso (visualização)

1. Na home page portal do Azure, vá para **observador de rede**.
1. À esquerda, na seção **monitoramento** , selecione **Monitor de conexão (versão prévia)**.
1. Você verá todos os monitores de conexão que foram criados no monitor de conexão (versão prévia). Para ver os monitores de conexão que foram criados na experiência clássica do monitor de conexão, vá para a guia **Monitor de conexão** .

    ![Captura de tela mostrando monitores de conexão que foram criados no monitor de conexão (versão prévia)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

Nos monitores de conexão criados no monitor de conexão (versão prévia), você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

O monitor de conexão (versão prévia) inclui as seguintes entidades:

* **Recurso de monitor de conexão** – um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade** – uma origem ou um destino que participa das verificações de conectividade. Os exemplos de pontos de extremidade incluem VMs do Azure, agentes locais, URLs e IPs.
* **Configuração de teste** – uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste** – o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

 ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes](./media/connection-monitor-2-preview/cm-tg-2.png)

Você pode criar uma visualização do monitor de conexão usando [portal do Azure](connection-monitor-preview-create-using-portal.md) ou [ARMClient](connection-monitor-preview-create-using-arm-client.md)

Todas as fontes, destinos e configurações de teste que você adiciona a um grupo de teste são divididos em testes individuais. Veja um exemplo de como as origens e os destinos são divididos:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configurações de teste: 2 (config 1, config 2)
* Total de testes criados: 12

| Número do teste | Fonte | Destino | Configuração de teste |
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

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 
    * 20 por meio de ARMClient
    * 2 por meio do portal do Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analisar dados de monitoramento e definir alertas

Depois de criar um monitor de conexão, as origens verificam a conectividade com os destinos com base em sua configuração de teste.

### <a name="checks-in-a-test"></a>Verificações em um teste

Com base no protocolo que você escolheu na configuração de teste, o monitor de conexão (versão prévia) executa uma série de verificações para o par de origem-destino. As verificações são executadas de acordo com a frequência de teste escolhida.

Se você usar HTTP, o serviço calculará o número de respostas HTTP que retornaram um código de resposta. O resultado determina a porcentagem de verificações com falha. Para calcular o RTT, o serviço mede o tempo entre uma chamada HTTP e a resposta.

Se você usar TCP ou ICMP, o serviço calculará a porcentagem de perda de pacotes para determinar a porcentagem de verificações com falha. Para calcular o RTT, o serviço mede o tempo necessário para receber a confirmação (ACK) dos pacotes que foram enviados. Se você habilitou os dados de traceroute para seus testes de rede, poderá ver a perda e a latência de saltos por salto para sua rede local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados retornados pelas verificações, os testes podem ter os seguintes Estados:

* **Pass** – valores reais para a porcentagem de verificações com falha e RTT estão dentro dos limites especificados.
* **Falha** – valores reais para a porcentagem de verificações com falha ou RTT excedeu os limites especificados. Se nenhum limite for especificado, um teste atingirá o estado de falha quando a porcentagem de verificações com falha for 100.
* **Aviso** – nenhum critério foi especificado para a porcentagem de verificações com falha. Na ausência de critérios especificados, o monitor de conexão (versão prévia) atribui automaticamente um limite. Quando esse limite for excedido, o status do teste será alterado para aviso.

### <a name="data-collection-analysis-and-alerts"></a>Coleta de dados, análise e alertas

Os dados coletados pelo monitor de conexão (visualização) são armazenados no espaço de trabalho Log Analytics. Você configura esse espaço de trabalho quando criou o monitor de conexão. 

Os dados de monitoramento também estão disponíveis em métricas de Azure Monitor. Você pode usar Log Analytics para manter seus dados de monitoramento pelo tempo que desejar. O Azure Monitor armazena métricas por apenas 30 dias por padrão. 

Você pode [definir alertas baseados em métricas nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorando painéis

Nos painéis de monitoramento, você verá uma lista dos monitores de conexão que você pode acessar para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino.

Ao ir para o monitor de conexão (versão prévia) do observador de rede, você pode exibir os dados por:

* **Monitor de conexão** – lista de todos os monitores de conexão criados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Este modo de exibição é o padrão.
* **Grupos de teste** – lista de todos os grupos de teste criados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Esses grupos de teste não são filtrados por monitores de conexão.
* **Teste** – lista de todos os testes que são executados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Esses testes não são filtrados por monitores de conexão ou grupos de teste.

Na imagem a seguir, as três exibições de dados são indicadas pela seta 1.

No painel, você pode expandir cada monitor de conexão para ver seus grupos de teste. Em seguida, você pode expandir cada grupo de teste para ver os testes que são executados nele. 

Você pode filtrar uma lista com base em:

* **Filtros de nível superior** – escolha assinaturas, regiões, fontes de carimbo de data/hora e tipos de destino. Consulte a caixa 2 na imagem a seguir.
* **Filtros baseados em estado** – filtre pelo estado do monitor de conexão, do grupo de teste ou do teste. Consulte a seta 3 na imagem a seguir.
* **Filtros personalizados** – escolha **selecionar tudo** para fazer uma pesquisa genérica. Para pesquisar por uma entidade específica, selecione na lista suspensa. Consulte a seta 4 na imagem a seguir.

![Captura de tela mostrando como filtrar exibições de monitores de conexão, grupos de teste e testes no monitor de conexão (versão prévia)](./media/connection-monitor-2-preview/cm-view.png)

Por exemplo, para examinar todos os testes no monitor de conexão (versão prévia) em que o IP de origem é 10.192.64.56:
1. Altere a exibição para **teste**.
1. No campo de pesquisa, digite *10.192.64.56*
1. Na lista suspensa, selecione **fontes**.

Para mostrar apenas os testes com falha no monitor de conexão (versão prévia) em que o IP de origem é 10.192.64.56:
1. Altere a exibição para **teste**.
1. Para o filtro baseado em estado, selecione **falha**.
1. No campo de pesquisa, digite *10.192.64.56*
1. Na lista suspensa, selecione **fontes**.

Para mostrar apenas os testes com falha no monitor de conexão (versão prévia) em que o destino é outlook.office365.com:
1. Alterar exibição para **teste**.
1. Para o filtro baseado em estado, selecione **falha**.
1. No campo de pesquisa, digite *Outlook.office365.com*
1. Na lista suspensa, selecione **destinos**.

   ![Captura de tela mostrando uma exibição filtrada para mostrar apenas os testes com falha para o destino Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Para exibir as tendências em RTT e a porcentagem de verificações com falha para um monitor de conexão:
1. Selecione o monitor de conexão que você deseja investigar. Por padrão, os dados de monitoramento são organizados por grupo de teste.

   ![Captura de tela mostrando métricas para um monitor de conexão, exibido pelo grupo de teste](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Escolha o grupo de teste que você deseja investigar.

   ![Captura de tela mostrando onde selecionar um grupo de teste](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Você vê os cinco principais testes com falha do seu grupo de teste, com base no RTT ou na porcentagem de verificações com falha. Para cada teste, você vê as linhas de RTT e tendência para a porcentagem de verificações com falha.
1. Selecione um teste na lista ou escolha outro teste para investigar. Para o intervalo de tempo e a porcentagem de verificações com falha, você verá os valores de limite e reais. Para RTT, você vê os valores de limite, média, mínimo e máximo.

   ![Captura de tela mostrando os resultados de um teste para RTT e percentual de verificações com falha](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Altere o intervalo de tempo para exibir mais dados.
1. Altere a exibição para ver origens, destinos ou configurações de teste. 
1. Escolha uma fonte com base em testes com falha e investigue os cinco principais testes com falha. Por exemplo, escolha **Exibir por**  >  **fontes** e **Exibir por**  >  **destinos** para investigar os testes relevantes no monitor de conexão.

   ![Captura de tela mostrando as métricas de desempenho dos cinco principais testes com falha](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Para exibir as tendências em RTT e a porcentagem de verificações com falha para um grupo de teste:

1. Selecione o grupo de teste que você deseja investigar. 

    Por padrão, os dados de monitoramento são organizados por fontes, destinos e configurações de teste (testes). Posteriormente, você pode alterar a exibição de grupos de teste para origens, destinos ou configurações de teste. Em seguida, escolha uma entidade para investigar os cinco principais testes com falha. Por exemplo, altere a exibição para origens e destinos para investigar os testes relevantes no monitor de conexão selecionado.
1. Escolha o teste que você deseja investigar.

   ![Captura de tela mostrando onde selecionar um teste](./media/connection-monitor-2-preview/tg-drill.png)

    Para o intervalo de tempo e para o percentual de verificações com falha, você verá valores de limite e valores reais. Para RTT, você vê valores para limite, média, mínimo e máximo. Você também verá alertas acionados para o teste que você selecionou.
1. Altere o intervalo de tempo para exibir mais dados.

Para exibir as tendências em RTT e a porcentagem de verificações com falha para um teste:
1. Selecione a configuração de origem, destino e teste que você deseja investigar.

    Para o intervalo de tempo e para a porcentagem de verificações com falha, você verá valores de limite e valores reais. Para RTT, você vê valores para limite, média, mínimo e máximo. Você também verá alertas acionados para o teste que você selecionou.

   ![Captura de tela mostrando métricas para um teste](./media/connection-monitor-2-preview/test-drill.png)

1. Para ver a topologia de rede, selecione **topologia**.

   ![Captura de tela mostrando a guia topologia de rede](./media/connection-monitor-2-preview/test-topo.png)

1. Para ver os problemas identificados, na topologia, selecione qualquer salto no caminho. (Esses saltos são recursos do Azure.) Essa funcionalidade não está disponível atualmente para redes locais.

   ![Captura de tela mostrando um link de salto selecionado na guia topologia](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Consultas de log em Log Analytics

Use Log Analytics para criar exibições personalizadas de seus dados de monitoramento. Todos os dados que a interface do usuário exibe são de Log Analytics. Você pode analisar dados interativamente no repositório. Correlacione os dados de Integridade do Agente ou outras soluções baseadas em Log Analytics. Exporte os dados para o Excel ou Power BI, ou crie um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Nos monitores de conexão que foram criados antes da experiência do monitor de conexão (versão prévia), todas as quatro métricas estão disponíveis:% investigações com falha, AverageRoundtripMs, ChecksFailedPercent (versão prévia) e RoundTripTimeMs (versão prévia). Nos monitores de conexão que foram criados na experiência do monitor de conexão (versão prévia), os dados estão disponíveis apenas para as métricas que são marcadas com *(versão prévia)*.

![Captura de tela mostrando métricas no monitor de conexão (visualização)](./media/connection-monitor-2-preview/monitor-metrics.png)

Ao usar as métricas, defina o tipo de recurso como Microsoft. Network/networkWatchers/connectionMonitors

| Métrica | Nome para exibição | Unidade | Tipo de agregação | Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | Porcentagem de investigações com falha | Porcentagem | Média | Falha na porcentagem de investigações de monitoramento de conectividade. | Sem dimensões |
| AverageRoundtripMs | Média Tempo de ida e volta (ms) | Milissegundos | Média | RTT de rede médio para investigações de monitoramento de conectividade enviadas entre a origem e o destino. |             Sem dimensões |
| ChecksFailedPercent (visualização) | % De verificações com falha (versão prévia) | Porcentagem | Média | Percentual de verificações com falha para um teste. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |
| RoundTripTimeMs (visualização) | Tempo de ida e volta (MS) (visualização) | Milissegundos | Média | RTT para cheques enviados entre a origem e o destino. Esse valor não é médio. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |

#### <a name="metric-alerts-in-azure-monitor"></a>Alertas de métrica no Azure Monitor

Para criar um alerta no Azure Monitor:

1. Escolha o recurso de monitor de conexão que você criou no monitor de conexão (versão prévia).
1. Verifique se a **métrica** aparece como tipo de sinal para o monitor de conexão.
1. Em **Adicionar condição**, para o **nome do sinal**, selecione **ChecksFailedPercent (visualização)** ou **RoundTripTimeMs (versão prévia)**.
1. Para **tipo de sinal**, escolha **métricas**. Por exemplo, selecione **ChecksFailedPercent (visualização)**.
1. Todas as dimensões para a métrica são listadas. Escolha o nome da dimensão e o valor da dimensão. Por exemplo, selecione **endereço de origem** e, em seguida, insira o endereço IP de qualquer fonte no monitor de conexão.
1. Em **lógica de alerta**, preencha os seguintes detalhes:
   * **Tipo de condição**: **estática**.
   * **Condição** e **limite**.
   * **Granularidade de agregação e frequência de avaliação**: o monitor de conexão (versão prévia) atualiza os dados a cada minuto.
1. Em **ações**, escolha o grupo de ações.
1. Forneça detalhes do alerta.
1. Criar a regra de alerta.

   ![Captura de tela mostrando a área criar regra no Azure Monitor; "Endereço de origem" e "nome do ponto de extremidade de origem" estão realçados](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas em sua rede

O monitor de conexão (versão prévia) ajuda você a diagnosticar problemas no monitor de conexão e na rede. Os problemas em sua rede híbrida são detectados pelos agentes de Log Analytics que você instalou anteriormente. Problemas no Azure são detectados pela extensão do observador de rede. 

Você pode exibir problemas na rede do Azure na topologia de rede.

Para redes cujas fontes são VMs locais, os seguintes problemas podem ser detectados:

* Atingiu tempo limite solicitado.
* Ponto de extremidade não resolvido pelo DNS – temporário ou persistente. URL inválida.
* Nenhum host encontrado.
* A origem não pode se conectar ao destino. Destino não acessível por meio de ICMP.
* Problemas relacionados a certificados: 
    * Certificado de cliente necessário para autenticar o agente. 
    * A lista de realocação de certificado não está acessível. 
    * O nome do host do ponto de extremidade não corresponde ao assunto ou ao nome alternativo da entidade do certificado. 
    * O certificado raiz está ausente no repositório de autoridades de certificação confiáveis do computador local da origem. 
    * O certificado SSL expirou, é inválido, revogado ou é incompatível.

Para redes cujas fontes são VMs do Azure, os seguintes problemas podem ser detectados:

* Problemas do agente:
    * Agente interrompido.
    * Falha na resolução de DNS.
    * Nenhum aplicativo ou ouvinte escuta na porta de destino.
    * Não foi possível abrir o soquete.
* Problemas de estado da VM: 
    * Iniciando
    * Parando
    * Parado
    * Desalocando
    * Desalocada
    * Reinicialização
    * Não alocado
* A entrada da tabela ARP está ausente.
* O tráfego foi bloqueado devido a problemas de firewall local ou regras de NSG.
* Problemas de gateway de rede virtual: 
    * Rotas ausentes.
    * O túnel entre dois gateways está desconectado ou ausente.
    * O segundo gateway não foi encontrado pelo túnel.
    * Nenhuma informação de emparelhamento foi encontrada.
* A rota estava ausente no Microsoft Edge.
* O tráfego parou devido a rotas do sistema ou UDR.
* O BGP não está habilitado na conexão do gateway.
* A investigação DIP está inoperante no balanceador de carga.
