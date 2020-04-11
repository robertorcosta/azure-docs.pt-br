---
title: Migrar regras de alerta do Azure Monitor
description: Aprenda a usar a ferramenta de migração voluntária para migrar suas regras clássicas de alerta.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114259"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar suas regras clássicas de alerta

Como [anunciado anteriormente,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão sendo retirados em setembro de 2019 (originalmente julho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras clássicas de alerta e que querem acionar a migração. Este artigo explica como usar a ferramenta de migração para migrar voluntariamente suas regras clássicas de alerta antes da migração automática começar em setembro de 2019.

> [!NOTE]
> Devido ao atraso na implantação da ferramenta de migração, a data de aposentadoria para migração de alertas clássicos foi prorrogada para 31 de agosto de [2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a partir da data originalmente anunciada de 30 de junho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios de novos alertas

Alertas clássicos estão sendo substituídos por novos alertas unificados no Azure Monitor. A nova plataforma de alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos mais serviços do Azure.](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Os novos alertas métricos suportam [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzem consideravelmente a sobrecarga do gerenciamento de muitas regras.
- O mecanismo de notificação unificada, que suporta:
  - [Grupos de ação](action-groups.md), um mecanismo de notificação modular que funciona com todos os novos tipos de alerta (métrica, log e registro de atividade).
  - Novos mecanismos de notificação como SMS, voz e Conector ITSM.
- A [experiência de alerta unificada](alerts-overview.md) traz todos os alertas em diferentes sinais (métrica, log e registro de atividade) em um só lugar.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte regras clássicas de alerta em novas regras de alerta equivalentes e cria grupos de ação. Na preparação, esteja atento aos seguintes pontos:

- Tanto o formato de carga de notificação quanto as APIs para criar e gerenciar novas regras de alerta são diferentes das regras clássicas de alerta, pois suportam mais recursos. [Aprenda a se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras clássicas de alerta. Eles continuarão a correr e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como usar a ferramenta de migração

Para ativar a migração de suas regras clássicas de alerta no portal Azure, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

1. Selecione **Alertas**e selecione **Gerenciar regras de alerta** ou Exibir **alertas clássicos.**

1. Selecione **Migrar para novas regras** para ir à página de aterrissagem da migração. Esta página mostra uma lista de todas as suas assinaturas e seu status de migração:

    ![migração de desembarque](media/alerts-migration/migration-landing.png "Migrar regras")

    Todas as assinaturas que podem ser migradas usando a ferramenta são marcadas como **Prontas para migrar**.

    > [!NOTE]
    > A ferramenta de migração está sendo desmembrada em fases para todas as assinaturas que usam regras clássicas de alerta. Nas fases iniciais do lançamento, você pode ver algumas assinaturas marcadas como não prontas para migração.

1. Selecione uma ou mais assinaturas e selecione **Migração de visualização**.

    A página resultante mostra os detalhes das regras clássicas de alerta que serão migradas para uma assinatura por vez. Você também pode selecionar **Baixar os detalhes de migração para esta assinatura** para obter os detalhes em um formato CSV.

    ![pré-visualização de migração](media/alerts-migration/migration-preview.png "Pré-visualização de migração")

1. Especifique um ou mais endereços de e-mail para serem notificados sobre o status da migração. Você receberá e-mail quando a migração estiver concluída ou se alguma ação for necessária de você.

1. Selecione **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme se você está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma assinatura, você não poderá editar ou criar regras clássicas de alerta para essa assinatura. Essa restrição garante que nenhuma alteração nas regras clássicas de alerta seja perdida durante a migração para as novas regras. Embora você não seja capaz de alterar suas regras clássicas de alerta, eles ainda continuarão a ser executados e a fornecer alertas até que eles tenham migrado. Depois que a migração estiver completa para sua assinatura, você não poderá mais usar regras clássicas de alerta.

    ![migração confirmar](media/alerts-migration/migration-confirm.png "Confirmar iniciar a migração")

1. Quando a migração estiver concluída ou se a ação for necessária de você, você receberá um e-mail nos endereços que você forneceu anteriormente. Você também pode verificar periodicamente o status na página de desembarque da migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Por que minha assinatura está listada como não pronta para migração?

A ferramenta de migração está sendo desmembrada para os clientes em fases. Nas fases iniciais, a maioria ou todas as suas assinaturas podem ser marcadas como **Não estão prontas para migração.** 

Quando uma assinatura estiver pronta para migração, o proprietário da assinatura receberá uma mensagem de e-mail informando que a ferramenta está disponível. Fique de olho nessa mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Os usuários que têm a função Contribuinte de Monitoramento atribuído a eles no nível de assinatura podem desencadear a migração. [Saiba mais sobre o Controle de Acesso Baseado em Função para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo levará a migração?

A migração é concluída para a maioria das assinaturas em menos de uma hora. Você pode acompanhar o progresso da migração na página de aterrissagem da migração. Durante a migração, tenha certeza de que seus alertas ainda estão sendo executados no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se tiver um problema durante a migração?

Consulte o [guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração. Se alguma ação for necessária de você para concluir a migração, você será notificado nos endereços de e-mail fornecidos quando configurar a ferramenta.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
