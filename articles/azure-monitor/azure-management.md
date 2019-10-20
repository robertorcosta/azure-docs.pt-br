---
title: Gerenciamento do Azure e OMS (Operations Management Suite) | Microsoft Docs
description: Visão geral das áreas de gerenciamento de aplicativos e recursos do Azure com links para conteúdo em ferramentas de gerenciamento do Azure que foram previamente agrupadas como OMS (Operations Management Suite).
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/07/2018
ms.openlocfilehash: 7f515c1c5d44058e14f02f7681bfbb7fd2787c6f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597361"
---
# <a name="azure-management---monitoring"></a>Gerenciamento do Azure-monitoramento

O monitoramento no Azure é um aspecto do gerenciamento do Azure.  Este artigo descreve brevemente as diferentes áreas de gerenciamento necessárias para implantar e manter seus aplicativos e recursos no Azure com links para a documentação para você começar.

## <a name="management-in-azure"></a>Gerenciamento no Azure

O gerenciamento refere-se às tarefas e aos processos necessários para manter seus aplicativos de negócios e os recursos que dão suporte a eles.  O Azure tem vários serviços e ferramentas que trabalham juntos para fornecer gerenciamento completo para não apenas seus aplicativos em execução no Azure, mas também em outras nuvens e locais.  Compreender as diferentes ferramentas disponíveis e como elas podem ser usadas juntas para uma variedade de cenários de gerenciamento é a primeira etapa na criação de um ambiente de gerenciamento completo.

O diagrama a seguir ilustra as diferentes áreas de gerenciamento que são necessárias para manter qualquer aplicativo ou recurso.  Essas áreas diferentes podem ser consideradas em termos de um ciclo de vida em que cada uma delas é necessária em caso de sucesso contínuo durante o período da vida útil de um recurso.  Isso começa com sua implantação inicial, por meio de sua operação contínua e, por fim, quando ela é desativada.

![Recursos de gerenciamento](media/management-overview/management-capabilities.png)


As seções a seguir descrevem brevemente as diferentes áreas de gerenciamento e fornecem links para conteúdo detalhado sobre os principais serviços do Azure destinados a solucioná-los.

## <a name="monitor"></a>Monitorar
O monitoramento é o ato de coletar e analisar dados para determinar o desempenho, a integridade e a disponibilidade do seu aplicativo de negócios e os recursos dos quais ele depende. Uma estratégia de monitoramento eficaz ajudará você a entender a operação detalhada dos diferentes componentes do seu aplicativo e aumentar o tempo de atividade, notificando-o proativamente sobre problemas críticos para que você possa resolvê-los antes que eles se tornem problemas. O monitoramento no Azure é fornecido principalmente por [Azure monitor](../azure-monitor/overview.md) que fornece armazenamentos comuns para armazenar dados de monitoramento, várias fontes de dados para coletar dados de diferentes camadas que dão suporte ao seu aplicativo e recursos para análise e respondendo aos dados coletados.

## <a name="configure"></a>Configurar
Configurar refere-se à implantação inicial e à configuração de aplicativos e recursos e à manutenção contínua com patches e atualizações.  A automação dessas tarefas por meio de script e de política permite eliminar a redundância, minimizando seu tempo e esforço e aumentando a precisão e a eficiência.  A [automação do Azure](../automation/automation-intro.md) fornece a grande quantidade de serviços para automatizar tarefas de configuração.  Além de runbooks para automatizar processos, ele fornece configuração e gerenciamento de atualizações, que o ajudarão a gerenciar a configuração por meio da política e de identificar e implantar atualizações.

## <a name="govern"></a>Indicadores
A governança fornece mecanismos e processos para manter o controle sobre seus aplicativos e recursos no Azure.  Ele envolve planejar suas iniciativas e definir prioridades estratégicas.  A governança no Azure é implementada principalmente com dois serviços.  [Azure Policy](../governance/policy/overview.md) permite criar, atribuir e gerenciar definições de política que impõem regras e ações diferentes sobre seus recursos, para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. O [Gerenciamento de custos do Azure](../cost-management/overview-cost-mgt.md) permite que você acompanhe o uso e as despesas da nuvem para seus recursos do Azure e outros provedores de nuvem, incluindo o AWS e o Google.

## <a name="secure"></a>Segura
Gerenciar a segurança de seus aplicativos, recursos e dados envolve uma combinação de avaliar ameaças, coletar e analisar dados de segurança e garantir que seus aplicativos e recursos sejam projetados e configurados de maneira segura.  Monitoramento de segurança e análise de ameaças são fornecidos pela [central de segurança do Azure](../security-center/security-center-intro.md) , que inclui gerenciamento de segurança unificado e proteção avançada contra ameaças em cargas de trabalho de nuvem híbrida.  Você também deve ver [introdução à segurança do Azure](../security/fundamentals/overview.md) para obter informações abrangentes sobre segurança no Azure e diretrizes sobre como configurar com segurança os recursos do Azure.


## <a name="protect"></a>Proteja
A proteção refere-se a garantir que seus aplicativos e dados estejam sempre disponíveis, mesmo no caso de interrupções além do seu controle.  A proteção no Azure é fornecida por dois serviços.  O [backup do Azure](../backup/backup-introduction-to-azure-backup.md) fornece backup e recuperação de seus dados na nuvem ou localmente.    [Azure site Recovery](../site-recovery/site-recovery-overview.md) garante a alta disponibilidade do seu aplicativo fornecendo continuidade de negócios e recuperação imediata em caso de desastre.

## <a name="migrate"></a>Migre 
A migração refere-se à transição de cargas de trabalho atualmente em execução no local para a nuvem do Azure.  As [migrações para Azure](../migrate/migrate-overview.md) são um serviço que ajuda a avaliar a adequação da migração, incluindo estimativas de custo e dimensionamento com base no desempenho, de máquinas virtuais locais para o Azure.  Azure Site Recovery pode ajudá-lo a executar a migração real de máquinas virtuais [do local](../site-recovery/migrate-tutorial-on-premises-azure.md) ou [do Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  A migração de banco de dados [do Azure](../dms/dms-overview.md) ajudará você a migrar várias fontes de banco de dado para as plataformas do Azure.

