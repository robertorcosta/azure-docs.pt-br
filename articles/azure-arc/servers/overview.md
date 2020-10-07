---
title: Visão geral de servidores habilitados para Azure Arc
description: Saiba como usar servidores habilitados para Azure Arc para gerenciar servidores hospedados fora do Azure como um recurso do Azure.
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 09/16/2020
ms.topic: overview
ms.openlocfilehash: 9fcac35f943eefb15a200e9b148b22ce67cc3941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90887539"
---
# <a name="what-is-azure-arc-enabled-servers"></a>O que são servidores habilitados para Azure Arc?

Os servidores habilitados para Azure Arc permitem que você gerencie computadores Windows e Linux hospedados fora do Azure na sua rede corporativa ou em outro provedor de nuvem, semelhante à maneira como você gerencia máquinas virtuais nativas do Azure. Quando um computador híbrido é conectado ao Azure, ele se torna um computador conectado e é tratado como um recurso no Azure. Cada computador conectado tem uma ID de Recurso, é gerenciado como parte de um grupo de recursos dentro de uma assinatura e se beneficia de constructos padrão do Azure, tais como o Azure Policy e a aplicação de marcas. Os provedores de serviços que gerenciam a infraestrutura local de um cliente podem gerenciar seus computadores híbridos, assim como eles fazem hoje com recursos nativos do Azure, em vários ambientes de clientes, usando o [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) com o Azure Arc.

Para oferecer essa experiência com seus computadores híbridos hospedados fora do Azure, o agente do Azure Connected Machine precisa ser instalado em cada computador que você planeja conectar ao Azure. Esse agente não oferece nenhuma outra funcionalidade e não substitui o [agente do Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Quando você conecta seu computador a servidores habilitados para Azure Arc, ele habilita a capacidade de execução das seguintes tarefas de monitoramento e gerenciamento de configuração:

- Atribua [configurações de convidado do Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure.

- Relatar alterações de configuração sobre software instalado, serviços da Microsoft, Registro e arquivos do Windows e daemons do Linux em servidores monitorados usando a Automação do Azure [Controle de Alterações e Inventário](../../automation/change-tracking.md).

- Monitore o desempenho do sistema operacional convidado do computador conectado e descubra os componentes do aplicativo para monitorar seus processos e dependências com outros recursos com os quais o aplicativo se comunica usando o [Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md).

- Simplifique a implantação com outros serviços do Azure, como o [State Configuration](../../automation/automation-dsc-overview.md) da Automação do Azure e workspace do Log Analytics do Azure Monitor usando as [extensões de VM do Azure](manage-vm-extensions.md) com suporte para seu computador Windows ou Linux não Azure. Isso inclui a execução da instalação de software ou a configuração pós-implantação usando a Extensão de Script Personalizado.

- Use [Gerenciamento de Atualizações](../../automation/update-management/update-mgmt-overview.md) na Automação do Azure para gerenciar atualizações do sistema operacional para seus servidores Windows e Linux. Primeiro, implante a função de trabalho do [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) e siga as etapas para [habilitar Gerenciamento de Atualizações](../../automation/update-management/update-mgmt-enable-portal.md) em seu computador Windows ou Linux não Azure.

- Inclua seus servidores não Azure para detecção de ameaças e monitore proativamente possíveis ameaças à segurança usando a [Central de Segurança do Azure](../../security-center/security-center-intro.md).

Os dados de log coletados e armazenados em um workspace do Log Analytics do computador híbrido agora contêm propriedades específicas do computador, como uma ID de Recurso. Isso pode ser usado para dar suporte ao acesso ao log no [contexto de recurso](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões com suporte

Para obter uma lista definitiva de regiões com suporte com servidores habilitados para o Azure Arc, confira a página [Produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

Na maioria dos casos, o local selecionado durante a criação do script de instalação deve ser a região do Azure mais próxima à localização do seu computador. Os dados em repouso serão armazenados na geografia do Azure que contém a região especificada por você, o que também pode afetar sua escolha de região se você tiver requisitos de residência de dados. Se a região do Azure a qual seu computador está conectado for afetada por uma interrupção, a máquina conectada não será afetada, mas talvez as operações de gerenciamento que usam o Azure não possam terminar. No caso de uma interrupção regional, se houver várias localizações que dão suporte a um serviço com redundância geográfica, será melhor conectar os computadores em cada localização a uma região diferente do Azure.

### <a name="agent-status"></a>Status do agente

O agente do Connected Machine envia uma mensagem de pulsação regular para o serviço a cada 5 minutos. Se o serviço parar de receber essas mensagens de pulsação de um computador, esse computador será considerado offline e o status será alterado automaticamente para **Desconectado** no portal no período de 15 a 30 minutos. Ao receber uma mensagem de pulsação subsequente do agente do Connected Machine, o status dela será alterado automaticamente para **Conectado**.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar servidores habilitados para Arc em vários computadores híbridos, examine [Visão geral do agente do Computador Conectado](agent-overview.md) para entender os requisitos, os detalhes técnicos sobre o agente e os métodos de implantação.
