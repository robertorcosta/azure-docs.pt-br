---
title: Visão geral de servidores habilitados para o Azure Arc (versão prévia)
description: Saiba como usar servidores habilitados para Azure Arc (versão prévia) para gerenciar servidores hospedados fora do Azure como um recurso do Azure.
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: c368307df911f5143541bb5337eb76a208416909
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228080"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>O que são servidores habilitados para Azure Arc (versão prévia)?

Os servidores habilitados para Azure Arc (versão prévia) permitem que você gerencie computadores Windows e Linux hospedados fora do Azure na sua rede corporativa ou em outro provedor de nuvem, semelhante à maneira como você gerencia máquinas virtuais nativas do Azure. Quando um computador híbrido é conectado ao Azure, ele se torna um computador conectado e é tratado como um recurso no Azure. Cada computador conectado tem uma ID de Recurso, é gerenciado como parte de um grupo de recursos dentro de uma assinatura e se beneficia de constructos padrão do Azure, tais como o Azure Policy e a aplicação de marcas.

Para oferecer essa experiência com seus computadores híbridos hospedados fora do Azure, o agente do Azure Connected Machine precisa ser instalado em cada computador que você planeja conectar ao Azure. Esse agente não oferece nenhuma outra funcionalidade e não substitui o [agente do Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Essa versão prévia destina-se a fins de avaliação. Recomendamos que você não gerencie computadores de produção críticos.
>

## <a name="supported-scenarios"></a>Cenários com suporte

Quando você conecta seu computador a servidores habilitados para Azure Arc (versão prévia), ele habilita a capacidade de execução das seguintes tarefas de gerenciamento de configuração:

- Atribua [configurações de convidado do Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure.

- Monitore o desempenho do sistema operacional convidado do computador conectado e descubra os componentes do aplicativo para monitorar seus processos e dependências com outros recursos com os quais o aplicativo se comunica usando o [Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md).

- Simplifique a implantação com outros serviços do Azure, como o State Configuration da Automação do Azure e workspace do Log Analytics do Azure Monitor usando as [extensões de VM do Azure](manage-vm-extensions.md) com suporte para suas máquinas Windows ou Linux que não são do Azure. Isso inclui a execução da instalação de software ou a configuração pós-implantação usando a Extensão de Script Personalizado.

Os dados de log coletados e armazenados em um workspace do Log Analytics do computador híbrido agora contêm propriedades específicas do computador, como uma ID de Recurso. Isso pode ser usado para dar suporte ao acesso ao log no [contexto de recurso](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Regiões com suporte

Com servidores habilitados para Azure Arc (versão prévia), só há suporte a determinadas regiões:

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

Na maioria dos casos, o local selecionado durante a criação do script de instalação deve ser a região do Azure mais próxima à localização do seu computador. Os dados em repouso serão armazenados na geografia do Azure que contém a região especificada por você, o que também pode afetar sua escolha de região se você tiver requisitos de residência de dados. Se a região do Azure a qual seu computador está conectado for afetada por uma interrupção, a máquina conectada não será afetada, mas talvez as operações de gerenciamento que usam o Azure não possam terminar. No caso de uma interrupção regional, se houver várias localizações que dão suporte a um serviço com redundância geográfica, será melhor conectar os computadores em cada localização a uma região diferente do Azure.

### <a name="agent-status"></a>Status do agente

O agente do Connected Machine envia uma mensagem de pulsação regular para o serviço a cada 5 minutos. Se o serviço parar de receber essas mensagens de pulsação de um computador, esse computador será considerado offline e o status será alterado automaticamente para **Desconectado** no portal no período de 15 a 30 minutos. Ao receber uma mensagem de pulsação subsequente do agente do Connected Machine, o status dela será alterado automaticamente para **Conectado**.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar servidores habilitados para Arc (versão prévia) em vários computadores híbridos, examine [Visão geral do agente do Computador Conectado](agent-overview.md) para entender os requisitos, os detalhes técnicos sobre o agente e os métodos de implantação.
