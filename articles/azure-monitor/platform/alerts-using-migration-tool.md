---
title: Migre seus alertas clássicos no Azure Monitor usando a ferramenta de migração voluntária
description: Saiba como usar a ferramenta de migração voluntária para migrar suas regras de alerta clássicas.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 537d84639523a74cbd9403d4ad25c34a798b7061
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665095"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor estão sendo desativados em setembro de 2019 (foi originalmente 2019 de julho). Uma ferramenta de migração está disponível no portal do Azure aos clientes que usam regras de alerta clássicas e que desejam disparar a migração por conta própria. Este artigo explica como usar a ferramenta de migração para migrar voluntariamente suas regras de alerta clássicas antes que a migração automática comece em setembro de 2019.

> [!NOTE]
> Devido ao atraso na distribuição da ferramenta de migração, a data de desativação da migração de alertas clássicos foi [prorrogada para 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data de lançamento original de 30 de junho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios de novos alertas

Os alertas clássicos estão sendo substituídos por alertas novos e unificados no Azure Monitor. A nova plataforma de alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos outros serviços do Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Os novos alertas de métricas dão suporte a [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzem muito a sobrecarga do gerenciamento de muitas regras.
- O mecanismo de notificação unificada, que dá suporte a:
  - [Grupos de ações](action-groups.md), um mecanismo de notificação modular que funciona com todos os novos tipos de alertas (métrica, log e log de atividades).
  - Novos mecanismos de notificação, como SMS, voz e Conector ITSM.
- A [experiência de alerta unificada](alerts-overview.md) traz todos os alertas sobre diferentes sinais (métrica, log e log de atividades) em um único local.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte as regras de alerta clássicas em regras de alerta novas e equivalentes e cria grupos de ações. Em preparação, lembre-se dos seguintes pontos:

- O formato da carga de notificação e as APIs para criar e gerenciar novas regras de alerta são diferentes das regras de alerta clássicas, pois oferecem suporte a mais recursos. [Saiba como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras de alerta clássicas. Eles continuarão a executar e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como usar a ferramenta de migração

Para disparar a migração de suas regras de alerta clássicas no portal do Azure, siga estas etapas:

1. Em [portal do Azure](https://portal.azure.com), selecione **Monitor**.

1. Selecione **alertas**e, em seguida, selecione **gerenciar regras de alerta** ou **exibir alertas clássicos**.

1. Selecione **migrar para novas regras** para ir para a página de aterrissagem de migração. Esta página mostra uma lista de todas as suas assinaturas e seu status de migração:

    ![migração-inicial](media/alerts-migration/migration-landing.png "Migrar regras")

    Todas as assinaturas que podem ser migradas usando a ferramenta são marcadas como **prontas para migrar**.

    > [!NOTE]
    > A ferramenta de migração é distribuída em fases para todas as assinaturas que usam regras de alerta clássicas. Nas primeiras fases da distribuição, você poderá ver algumas assinaturas marcadas como não prontas para a migração.

1. Selecione uma ou mais assinaturas e, em seguida, selecione a **migração de visualização**.

    A página resultante mostra os detalhes das regras de alerta clássicas que serão migradas para uma assinatura de cada vez. Você também pode selecionar **baixar os detalhes de migração para esta assinatura** para obter os detalhes em um formato CSV.

    ![migração-visualização](media/alerts-migration/migration-preview.png "Migração de visualização")

1. Especifique um ou mais endereços de email a serem notificados sobre o status da migração. Você receberá um email quando a migração for concluída ou se alguma ação for necessária.

1. Selecione **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme se você está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma assinatura, você não poderá editar ou criar regras de alerta clássicas para essa assinatura. Essa restrição garante que nenhuma alteração nas regras de alerta clássicas seja perdida durante a migração para as novas regras. Embora você não consiga alterar suas regras de alerta clássicas, elas ainda continuarão sendo executadas e fornecerão alertas até que tenham sido migrados. Depois que a migração for concluída para sua assinatura, você não poderá mais usar as regras de alerta clássicas.

    ![migração-confirmar](media/alerts-migration/migration-confirm.png "Confirmar início da migração")

1. Quando a migração for concluída ou se a ação for necessária, você receberá um email nos endereços que você forneceu anteriormente. Você também pode verificar periodicamente o status na página de aterrissagem de migração no Portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Por que minha assinatura está listada como não pronta para migração?

A ferramenta de migração está distribuindo para os clientes em fases. Nas primeiras fases, a maioria ou todas as suas assinaturas podem ser marcadas como **não prontas para a migração**. 

Quando uma assinatura se tornar pronta para migração, o proprietário da assinatura receberá uma mensagem de email informando que a ferramenta está disponível. Fique atento à mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Os usuários que têm a função de colaborador de monitoramento atribuída a eles no nível de assinatura são capazes de disparar a migração. [Saiba mais sobre o controle de acesso baseado em função para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo a migração levará?

A migração foi concluída para a maioria das assinaturas em menos de uma hora. Você pode acompanhar o progresso da migração na página de aterrissagem de migração. Durante a migração, tenha certeza de que os alertas ainda estão sendo executados no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se encontrar um problema durante a migração?

Consulte o [Guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração. Se alguma ação for necessária para concluir a migração, você será notificado sobre os endereços de email que forneceu ao configurar a ferramenta.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
