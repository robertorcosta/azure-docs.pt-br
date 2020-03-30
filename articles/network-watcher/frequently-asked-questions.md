---
title: Perguntas frequentes (FAQ) sobre o Azure Network Watcher | Microsoft Docs
description: Este artigo responde a perguntas frequentes sobre o serviço Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471849"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Perguntas frequentes (FAQ) sobre o Azure Network Watcher
O serviço [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece um conjunto de ferramentas para monitorar, diagnosticar, visualizar métricas e ativar ou desativar logs para recursos em uma rede virtual Do Zure. Este artigo responde a perguntas comuns sobre o serviço.

## <a name="general"></a>Geral

### <a name="what-is-network-watcher"></a>O que é o Observador de Rede?
O Network Watcher foi projetado para monitorar e reparar a saúde da rede de componentes IaaS (Infrastructure-as-a-Service), que inclui Máquinas Virtuais, Redes Virtuais, Gateways de Aplicativos, Balanceadores de carga e outros recursos em uma rede virtual Do Zure. Não é uma solução para monitorar a infra-estrutura PaaS (Platform-as-a-Service) ou obter análises web/móvel.

### <a name="what-tools-does-network-watcher-provide"></a>Quais ferramentas o Network Watcher fornece?
O Network Watcher fornece três grandes conjuntos de recursos
* Monitoramento
  * [A exibição de topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) mostra os recursos em sua rede virtual e as relações entre eles.
  * [O Monitor de Conexão](https://docs.microsoft.com/azure/network-watcher/connection-monitor) permite monitorar a conectividade e a latência entre uma VM e outro recurso de rede.
  * [O monitor de desempenho de rede](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) permite monitorar conectividade e latências em arquiteturas de rede híbridas, circuitos Expressroute e pontos finais de serviço/aplicativo.  
* Diagnósticos
  * [O IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) permite detectar problemas de filtragem de tráfego em um nível de VM.
  * [O Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) ajuda você a verificar rotas de tráfego e detectar problemas de roteamento.
  * [A solução de problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) permite uma verificação única de conectividade e latência entre uma VM e outro recurso de rede.
  * [A captura de pacotes](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite que você capture todo o tráfego em uma VM em sua rede virtual.
  * [A solução de problemas VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) executa várias verificações de diagnóstico em seus gateways e conexões VPN para ajudar a depurar problemas.
* Registrando em log
  * [O NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) permite que você registre todo o tráfego em seus [NSGs (Network Security Groups, grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [O Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) processa seus dados do NSG Flow Log permitindo visualizar, consultar, analisar e entender o tráfego da rede.


Para obter informações mais detalhadas, consulte a página visão geral do [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Como funciona o preço do Network Watcher?
Visite a [página de preços](https://azure.microsoft.com/pricing/details/network-watcher/) para componentes do Network Watcher e seus preços.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Em quais regiões o Network Watcher é suportado/disponível?
Você pode ver a última disponibilidade regional na [página de disponibilidade do Azure Service](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Quais permissões são necessárias para usar o Network Watcher?
Veja a lista de [permissões RBAC necessárias para usar o Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Para implantar recursos, você precisa de permissões de contribuinte para o NetworkWatcherRG (veja abaixo).

### <a name="how-do-i-enable-network-watcher"></a>Como fazer a habilitação do Observador de Rede?
O serviço Network Watcher é [habilitado automaticamente](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) para cada assinatura.

### <a name="what-is-the-network-watcher-deployment-model"></a>O que é o modelo de implantação do Network Watcher?
O recurso pai do Network Watcher é implantado com uma instância única em cada região. Formato de nomeação: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centralus é o recurso do Network Watcher para a região "Central US".

### <a name="what-is-the-networkwatcherrg"></a>O que é o NetworkWatcherRG?
Os recursos do Network Watcher estão localizados no grupo de recursos **NetworkWatcherRG** oculto que é criado automaticamente. Por exemplo, o recurso NSG Flow Logs é um recurso infantil do Network Watcher e está habilitado no NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Por que preciso instalar a extensão do Network Watcher? 
A extensão Do Observador de Rede é necessária para qualquer recurso que precise gerar ou interceptar tráfego de uma VM. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quais recursos requerem a extensão do Network Watcher?
Os recursos De captura de pacotes, solução de problemas de conexão e monitor de conexão precisam que a extensão Do Observador de Rede esteja presente.

### <a name="what-are-resource-limits-on-network-watcher"></a>Quais são os limites de recursos no Network Watcher?
Consulte a página [Limites de Serviço](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) para todos os limites.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Por que apenas uma instância do Network Watcher é permitida por região? 
O Network Watcher só precisa ser habilitado uma vez para que seus recursos funcionem, este não é um limite de serviço.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Como posso gerenciar o recurso de observador de rede? 
O recurso Network Watcher representa o serviço de back-end para o Network Watcher e é totalmente gerenciado pelo Azure. Os clientes não precisam gerenciá-lo. Operações como move não são suportadas no recurso. No entanto, [o recurso pode ser excluído](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>Registros de fluxo NSG

### <a name="what-does-nsg-flow-logs-do"></a>O que o NSG Flow Logs faz?
Os recursos de rede do Azure podem ser combinados e gerenciados através de [NSGs (Network Security Groups, grupos de segurança de rede).](https://docs.microsoft.com/azure/virtual-network/security-overview) Os registros de fluxo do NSG permitem que você registre informações de fluxo de 5 tuplos sobre todo o tráfego através de seus NSGs. Os registros de fluxo bruto são gravados em uma conta do Azure Storage de onde podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Como uso o NSG Flow Logs com uma conta de armazenamento atrás de um firewall?

Para usar uma conta de armazenamento atrás de um firewall, você tem que fornecer uma exceção para que os Serviços Confiáveis da Microsoft acessem sua conta de armazenamento:

* Navegue até a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página Contas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) de armazenamento
* Na seção **CONFIGURAÇÕES**, selecione **Firewalls e redes virtuais**
* Em "Permitir acesso de", selecione **Redes selecionadas**. Em seguida, em **Exceções,** marque a caixa ao lado de **"Permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento"** 
* Se já estiver selecionada, nenhuma alteração será necessária.  
* Localize seu NSG de destino na página de visão geral do [NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e habilite os Registros de Fluxo do NSG com a conta de armazenamento acima selecionada.

Você pode verificar os logs de armazenamento após alguns minutos, você deve ver um carimbo de data/hora atualizado ou um novo arquivo JSON criado.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Como usar o NSG Flow Logs com uma conta de armazenamento atrás de um ponto final de serviço?

Os Registros de Fluxo do NSG são compatíveis com pontos finais de serviço sem exigir qualquer configuração extra. Por favor, veja o [tutorial sobre como ativar pontos finais de serviço](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) em sua rede virtual.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual é a diferença entre as versões de logs de fluxo 1 & 2?
Flow Logs versão 2 introduz o conceito de *Flow State* & armazena informações sobre bytes e pacotes transmitidos. [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Próximas etapas
 - Vá para nossa [página de visão geral da documentação](https://docs.microsoft.com/azure/network-watcher/) para alguns tutoriais para você começar com o Network Watcher.
