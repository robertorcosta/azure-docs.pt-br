---
title: Entenda como funciona o processo de migração automática para os alertas clássicos do Azure Monitor
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668240"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Entenda o processo de migração automática para suas regras clássicas de alerta

Como [anunciado anteriormente,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão sendo retirados em setembro de 2019 (originalmente julho de 2019). Como parte do processo de aposentadoria, [uma ferramenta de migração](alerts-using-migration-tool.md) está disponível no portal Azure para que os clientes desencadeiem a migração. Se você não tiver usado a ferramenta de migração até 31 de agosto de 2019, o Azure Monitor iniciará o processo de migração automática de seus alertas clássicos a partir de 1º de setembro de 2019.
Este artigo orienta você através do processo de migração automática e ajuda você a resolver quaisquer problemas que você possa encontrar.

  > [!NOTE]
  > Este artigo só se aplica à nuvem pública do Azure. Processo de aposentadoria para o Azure Monitor alertas clássicos na nuvem do governo azure e Azure China 21Vianet serão anunciados na data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>O que acontecerá durante o processo de migração automática?

- A partir **de 1º de setembro de 2019**, os clientes não poderão criar novas regras clássicas de alerta, exceto em [determinadas métricas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - Para as exceções, o cliente pode continuar a criar novas regras clássicas de alerta e usar seus alertas clássicos até junho de 2020.
- A partir de 1º de setembro de **2019**, a migração de alertas clássicos será acionada em lotes para todos os clientes que tiverem alertas clássicos.
- Assim como na ferramenta de migração voluntária, certas regras clássicas de alerta que não são migratórias serão deixadas como estão. Essas regras clássicas de alerta continuarão a ser suportadas até junho de 2020. No entanto, quaisquer regras de alerta clássicas inválidas serão excluídas por não serem funcionais.
Quaisquer regras clássicas de alerta que estejam monitorando recursos de destino excluídos ou em [métricas que não são mais suportadas são consideradas inválidas.](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)
- Uma vez iniciada a migração para sua assinatura, a menos que haja algum problema, a migração deve ser concluída dentro de uma hora. Os clientes podem monitorar o status da migração [na lâmina de migração no Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Os proprietários de assinaturas receberão um e-mail sobre a conclusão bem-sucedida da migração.
- Se houver algum problema durante a migração, os proprietários de assinatura também receberão um e-mail informando-os do mesmo. Os clientes podem usar a lâmina de migração para ver todos os detalhes do problema.
- Caso uma ação seja necessária do cliente, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de diretiva, os clientes precisarão resolver quaisquer problemas até 31 de outubro de 2019. Se os problemas não forem resolvidos até lá, a migração bem sucedida de seus alertas clássicos não pode ser garantida.

    > [!NOTE]
    > Se você não quiser esperar o início do processo de migração automática, você ainda pode acionar a migração voluntariamente usando a ferramenta de migração.

## <a name="important-things-to-note"></a>Coisas importantes a serem observados

O processo de migração converte regras clássicas de alerta em novas regras de alerta equivalentes e cria grupos de ação. Na preparação, esteja atento aos seguintes pontos:

- Os formatos de carga de notificação para novas regras de alerta são diferentes dos regras de alerta clássicos porque suportam mais recursos. Se você tem aplicativos lógicos, runbooks ou webhooks que são acionados pela regra clássica de alerta, eles podem parar de funcionar como esperado, uma vez que a migração é concluída devido a diferenças nas cargas de notificação. [Aprenda a se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras clássicas de alerta. Eles continuarão a correr e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários de assinaturas receberão um e-mail notificando-os sobre o problema. Você pode usar a lâmina de migração no Azure Monitor para ver todos os detalhes do problema.

Consulte o [guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração.

  > [!NOTE]
  > Caso uma ação seja necessária do cliente, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de diretiva, os clientes precisarão resolver quaisquer problemas até 31 de outubro de 2019. Se os problemas não forem resolvidos até lá, a migração bem sucedida de seus alertas clássicos não pode ser garantida.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
