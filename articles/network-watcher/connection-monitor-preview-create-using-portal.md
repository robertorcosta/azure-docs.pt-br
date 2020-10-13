---
title: Criar visualização do monitor de conexão-portal do Azure
titleSuffix: Azure Network Watcher
description: Saiba como criar um monitor de conexão (versão prévia) usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362859"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Criar um monitor de conexão (versão prévia) usando o portal do Azure

Saiba como criar um monitor de conexão (versão prévia) para monitorar a comunicação entre os recursos usando o portal do Azure. Ele dá suporte a implantações de nuvem híbridas e do Azure.

## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de conexão criados no monitor de conexão (versão prévia), você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

O monitor de conexão (versão prévia) inclui as seguintes entidades:


* **Recurso de monitor de conexão** – um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade** – uma origem ou um destino que participa das verificações de conectividade. Exemplos de pontos de extremidade incluem 
    * VMs do Azure
    * VNETs do Azure
    * Sub-redes do Azure
    * Agentes locais
    * Sub-redes locais
    * Rede personalizada local, composta de várias sub-redes
    * URLs e IPs 
* **Configuração de teste** – uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste** – o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

    ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Etapas para criar

Para criar um monitor de conexão (versão prévia) usando o portal do Azure, siga as etapas abaixo:

1. Na home page portal do Azure, vá para **observador de rede**.
1. À esquerda, na seção **monitoramento** , selecione **Monitor de conexão (versão prévia)**.
1. Você verá todos os monitores de conexão que foram criados no monitor de conexão (versão prévia). Para ver os monitores de conexão que foram criados na experiência clássica do monitor de conexão, vá para a guia **Monitor de conexão** .

    ![Captura de tela mostrando monitores de conexão que foram criados no monitor de conexão (versão prévia)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. No painel do **Monitor de conexão (versão prévia)** , no canto superior esquerdo, selecione **criar**.

   ![Captura de tela mostrando a guia noções básicas no monitor de conexão](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na guia **noções básicas** , insira informações para o monitor de conexão:
   * **Nome do monitor de conexão** – adicione o nome do monitor de conexão. Use as regras de nomenclatura padrão para recursos do Azure.
   * **Assinatura** – escolha uma assinatura para o monitor de conexão.
   * **Região** – escolha uma região para o monitor de conexão. Você pode selecionar apenas as VMs de origem que são criadas nessa região.
   * **Configuração do espaço de trabalho** -seu espaço de trabalho mantém os dados de monitoramento. Você pode usar um espaço de trabalho personalizado ou o espaço de trabalho padrão. 
       * Para usar o espaço de trabalho padrão, marque a caixa de seleção. 
       * Para escolher um espaço de trabalho personalizado, desmarque a caixa de seleção. Em seguida, escolha a assinatura e a região para seu espaço de trabalho personalizado. 
1. Na parte inferior da guia, selecione **Avançar: grupos de teste**.

   ![Captura de tela mostrando a guia Criar grupo de teste no monitor de conexão](./media/connection-monitor-2-preview/create-tg.png)

1. Adicione fontes, destinos e configurações de teste em seus grupos de teste. Para configurar seus grupos de teste, consulte [criar grupos de teste no monitor de conexão](#create-test-groups-in-a-connection-monitor). 
1. Na parte inferior da guia, selecione **Avançar: criar alertas**.

   ![Captura de tela mostrando o painel em que você cria alertas](./media/connection-monitor-2-preview/create-alert.png)

1. Para criar alertas, consulte [criar alertas no monitor de conexão](#create-alerts-in-connection-monitor)
1. Na parte inferior da guia, selecione **Avançar: revisar + criar**.

  ![Captura de tela do monitor de conexão, mostrando a guia revisão + criar](./media/connection-monitor-2-preview/review-create-cm.png)

1. Na guia **revisar + criar** , examine as informações básicas e os grupos de teste antes de criar o monitor de conexão. Se você precisar editar o monitor de conexão:
   * Para editar os detalhes básicos, selecione o ícone de lápis.
   * Para editar um grupo de teste, selecione-o.

   > [!NOTE] 
   > A guia **revisar + criar** mostra o custo por mês durante o estágio de visualização do monitor de conexão. Atualmente, a coluna **custo atual** não mostra nenhum encargo. Quando o monitor de conexão se tornar disponível de forma geral, essa coluna mostrará um encargo mensal. 
   > 
   > Mesmo no estágio de visualização do monitor de conexão, Log Analytics se aplicam encargos de ingestão.

1. Quando você estiver pronto para criar o monitor de conexão, na parte inferior da guia **revisar + criar** , selecione **criar**.

O monitor de conexão (versão prévia) cria o recurso de monitor de conexão em segundo plano.

## <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste em um monitor de conexão

Cada grupo de teste em um monitor de conexão inclui origens e destinos que são testados em parâmetros de rede. Eles são testados para a porcentagem de verificações que falham e o RTT sobre as configurações de teste.

No portal do Azure, para criar um grupo de teste em um monitor de conexão, especifique valores para os seguintes campos:

* **Desabilitar grupo de teste** – você pode selecionar esse campo para desabilitar o monitoramento de todas as origens e destinos que o grupo de teste especifica. Essa seleção é desmarcada por padrão.
* **Nome** – o nome do grupo de teste.
* **Fontes** – você pode especificar VMs do Azure e máquinas locais como fontes se os agentes estiverem instalados neles. Para instalar um agente para sua origem, consulte [instalar agentes de monitoramento](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Para escolher os agentes do Azure, selecione a guia **pontos de extremidade do Azure** . Aqui você vê somente as VMs que estão associadas à região que você especificou quando criou o monitor de conexão. Por padrão, as VMs são agrupadas na assinatura à qual pertencem. Esses grupos são recolhidos. 
   
       Você pode fazer uma busca detalhada do nível de assinatura para outros níveis na hierarquia:

      **Assinatura**  >  do **Grupos**  >  de recursos **VNETs**  >  **Sub-redes**  >  **VMs com agentes**

      Você também pode alterar o valor do campo **Agrupar por** para iniciar a árvore de qualquer outro nível. Por exemplo, se você agrupar por rede virtual, verá as VMs que têm agentes na hierarquia **VNETs**  >  **sub-redes**  >  **VMs com agentes**.
       Selecionar uma VNET, uma sub-rede ou uma única VM define a ID de recurso correspondente como o ponto de extremidade. Por padrão, todas as VMs na VNET ou sub-rede selecionada com a extensão do observador de rede participam do monitoramento. Para reduzir o escopo, selecione sub-redes/agentes específicos ou altere o valor da propriedade escopo. 

      ![Captura de tela do monitor de conexão, mostrando o painel adicionar fontes e a guia agentes do Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Para escolher agentes locais, selecione a guia **não-agentes do Azure** . Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos esses espaços de trabalho têm a solução Monitor de Desempenho de Rede configurada. 
   
       Se você precisar adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [Monitoring Solutions in Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Na exibição **criar monitor de conexão** , na guia **noções básicas** , a região padrão é selecionada. Se você alterar a região, poderá escolher agentes de espaços de trabalho na nova região.  Você pode selecionar um ou mais agentes ou sub-redes. Na exibição de sub-rede, você pode selecionar IPs específicos para monitoramento. A adição de várias sub-redes criará uma rede local personalizada, ou seja, "OnPremises_Network_1". Você também pode alterar o valor do campo **Agrupar por** para agrupar por sub-redes.

      ![Captura de tela do monitor de conexão, mostrando o painel adicionar fontes e a guia agentes não Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * Você também pode escolher os pontos de extremidade usados recentemente, usando a guia de **Endpoint recente** 
   
   * Quando terminar de configurar as fontes, selecione **concluído**. Você ainda pode editar propriedades básicas como nome do ponto de extremidade clicando no ponto de extremidade na exibição criar grupo de teste. 

* **Destinos** – você pode monitorar a conectividade com VMs do Azure, máquinas locais ou qualquer ponto de extremidade (um IP público, URL ou FQDN) especificando-os como destinos. Em um único grupo de teste, você pode adicionar VMs do Azure, máquina local, URLs do Office 365, URLs do Dynamics 365 e pontos de extremidade personalizados.

    * Para escolher VMs do Azure como destinos, selecione a guia **pontos de extremidade do Azure** . Por padrão, as VMs do Azure são agrupadas em uma hierarquia de assinatura que está na mesma região que você selecionou na exibição **criar monitor de conexão** , na guia **noções básicas** . Você pode alterar a região e escolher VMs do Azure na região selecionada recentemente. Em seguida, você pode fazer uma busca detalhada do nível de assinatura para outros níveis na hierarquia, assim como os pontos de extremidade do Azure de origem.
     Você pode selecionar VNETs, sub-redes ou VMs únicas, semelhante aos pontos de extremidade do Azure de origem. Selecionar uma VNET, uma sub-rede ou uma única VM define a ID de recurso correspondente como o ponto de extremidade. Por padrão, todas as VMs na VNET ou sub-rede selecionada com a extensão do observador de rede participam do monitoramento. Para reduzir o escopo, selecione sub-redes/agentes específicos ou altere o valor da propriedade escopo. 

       ![Captura de tela do painel Adicionar destinos, mostrando a guia VMs do Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Captura de tela do painel Adicionar destinos, mostrando o nível de assinatura](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Para escolher os agentes não Azure como destinos, selecione a guia pontos de extremidade não Azure. Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos esses espaços de trabalho têm a solução Monitor de Desempenho de Rede configurada. Se você precisar adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no Azure Marketplace. Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte Monitoring Solutions in Azure Monitor. Na exibição criar monitor de conexão, na guia noções básicas, a região padrão é selecionada. Se você alterar a região, poderá escolher agentes de espaços de trabalho na nova região. Você pode selecionar um ou mais agentes ou sub-redes. Na exibição de sub-rede, você pode selecionar IPs específicos para monitoramento. A adição de várias sub-redes criará uma rede local personalizada, ou seja, "OnPremises_Network_1".  
    
     ![Captura de tela do painel Adicionar destinos não Azure](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Para escolher pontos de extremidade públicos como destinos, selecione a guia **endereços externos** . A lista de pontos de extremidade inclui URLs de teste do Office 365 e URLs de teste do Dynamics 365, agrupadas por nome. Além dessas extremidades, você pode escolher um ponto de extremidade que foi criado em outros grupos de teste no mesmo monitor de conexão. 
    
        Para adicionar um novo ponto de extremidade, no canto superior direito, selecione **+ extremidades**. Em seguida, forneça um nome de ponto de extremidade e URL, IP ou FQDN.

       ![Captura de tela mostrando onde adicionar pontos de extremidade públicos como destinos no monitor de conexão](./media/connection-monitor-2-preview/add-endpoints.png)

    * Para escolher os pontos de extremidade usados recentemente, vá para a guia **pontos de extremidade recentes**   .
    * Quando terminar de escolher destinos, selecione **concluído**. Você ainda pode editar propriedades básicas como nome do ponto de extremidade clicando no ponto de extremidade na exibição criar grupo de teste. 

* **Configurações de teste** – você pode associar uma ou mais configurações de teste em um grupo de teste. Crie uma nova configuração de teste usando a guia "nova configuração" ou use uma configuração de teste usada em outros grupos de teste no mesmo monitor de conexão por meio da guia "escolher existente".

    * **Nome** – nome da configuração de teste.
    * **Protocolo** – escolha TCP, ICMP ou http. Para alterar HTTP para HTTPS, selecione **http** como o protocolo e selecione **443** como a porta.
        * **Criar configuração de teste de rede** – essa caixa de seleção aparecerá apenas se você selecionar **http** no campo **protocolo** . Selecione esta caixa para criar outra configuração de teste que use as mesmas origens e destinos que você especificou em outro lugar em sua configuração. A configuração de teste recém-criada é denominada `<the name of your test configuration>_networkTestConfig` .
        * **Desabilitar traceroute** – este campo se aplica quando o protocolo é TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram a topologia e o RTT de salto a salto.
    * **Porta de destino** – você pode personalizar esse campo com uma porta de destino de sua escolha.
        * Escutar na porta – esse campo se aplica quando o protocolo é TCP. Selecione esta caixa para abrir a porta TCP escolhida, se ainda não estiver aberta. 
    * **Frequência de teste** – Use esse campo para escolher com que frequência as fontes de comando executarão ping no protocolo e na porta que você especificou. Você pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. Selecione personalizado para inserir a frequência de sua escolha entre 30 segundos e 30 minutos. As fontes testarão a conectividade com destinos com base no valor que você escolher.  Por exemplo, se você selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em um período de 30 segundos.
    * **Limite de êxito** – você pode definir limites nos seguintes parâmetros de rede:
       * **Verificações com falha** – defina a porcentagem de verificações que podem falhar quando as fontes verificarem a conectividade com os destinos usando os critérios que você especificou. Para o protocolo TCP ou ICMP, a porcentagem de verificações com falha pode ser igual à porcentagem de perda de pacotes. Para o protocolo HTTP, esse campo representa o percentual de solicitações HTTP que não receberam resposta.
       * **Tempo de ida e volta** – defina o RTT em milissegundos para o tempo que as fontes podem levar para se conectar ao destino pela configuração de teste.
    
       ![Captura de tela mostrando onde configurar uma configuração de teste no monitor de conexão](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Criar alertas no monitor de conexão

Você pode configurar alertas em testes que estão falhando com base nos limites definidos em configurações de teste.   

No portal do Azure, para criar alertas em um monitor de conexão, especifique valores para os seguintes campos: 

- Criar alerta-você pode selecionar este campo para criar um alerta de métrica em Azure Monitor. Selecionar isso permitirá que os outros campos sejam editados. Encargos adicionais para o alerta serão aplicáveis com base no [preço dos alertas](https://azure.microsoft.com/pricing/details/monitor/) 

- Recurso de > de escopo e hierarquia de > de escopo-isso é preenchedo com base nos valores especificados na guia noções básicas 

- Condição – > o alerta é criado na métrica "resultado do teste (visualização)". Quando o resultado do teste do monitor de conexão falha, a regra de alerta será acionada. 

- Grupo de ações – você pode optar por inserir seu email diretamente ou pode optar por criar alertas por meio de grupos de ação. Se você optar por inserir seu email diretamente, um grupo de ações com o nome NPM email Group será criado e a ID de email será adicionada a esse grupo de ações. Se você optar por usar grupos de ações, precisará selecionar um grupo de ações criado anteriormente. Você pode aprender a criar um grupo de ações aqui. Depois que o alerta for criado com êxito, você poderá usar o link Gerenciar alertas para gerenciar seus alertas. 

- Nome da regra de alerta-nome do monitor de conexão 

- Habilitar Regra após a criação – isso habilitará a regra de alerta com base na condição. Desabilite-o se você quiser criar a regra, mas não habilitá-la. 

    ![Captura de tela mostrando a criação de alerta no painel do monitor de conexão](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 2 por meio do portal do Azure

## <a name="next-steps"></a>Próximas etapas

* Saiba [como analisar dados de monitoramento e definir alertas](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas em sua rede](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
