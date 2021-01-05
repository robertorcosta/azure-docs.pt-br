---
title: Centro de backup-perguntas frequentes
description: Este artigo responde às perguntas frequentes sobre o centro de backup
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 5befa39411c22253bfccc689d8b5c5967a8cd759
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858613"
---
# <a name="backup-center---frequently-asked-questions"></a>Centro de backup-perguntas frequentes

## <a name="management"></a>Gerenciamento

### <a name="can-backup-center-be-used-across-tenants"></a>O centro de backup pode ser usado em locatários?

Sim, se você estiver usando o [Azure Lighthouse](../lighthouse/overview.md) e tiver delegado acesso a assinaturas em diferentes locatários, você poderá usar o centro de backup como um único painel para gerenciar backups entre locatários.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>O centro de backup pode ser usado para gerenciar cofres de serviços de recuperação e cofres de backup?

Sim, o centro de backup pode gerenciar os [cofres dos serviços de recuperação](./backup-azure-recovery-services-vault-overview.md) e os [cofres de backup](backup-vault-overview.md).

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Há um atraso antes das superfícies de dados no centro de backup?

O centro de backup tem como objetivo fornecer informações em tempo real. Pode haver um atraso de alguns segundos entre o tempo que uma entidade aparece em uma tela de cofre individual e a hora em que a mesma entidade aparece no centro de backup.

## <a name="configuration"></a>Configuração

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Preciso configurar qualquer coisa para ver os dados no centro de backup?

Não. O centro de backup vem pronto para uso. No entanto, para exibir [relatórios de backup](./configure-reports.md) no centro de backup, você precisa configurar relatórios para seus cofres.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Preciso ter permissões especiais para usar o centro de backup?

O centro de backup, como tal, não precisa de nenhuma nova permissão. Contanto que você tenha o nível certo de acesso do RBAC do Azure para os recursos que você está gerenciando, você pode usar o centro de backup para esses recursos. Por exemplo, para exibir informações sobre seus backups, você precisará de acesso de **leitor** aos seus cofres. Para configurar o backup e executar outras ações relacionadas ao backup, você precisará de funções de **operador de backup** ou colaborador de **backup** . Saiba mais sobre as [funções do Azure para o backup do Azure](./backup-rbac-rs-vault.md). 

Se você estiver usando [relatórios de backup](./configure-reports.md) no centro de backup, precisará de acesso aos espaços de trabalho do log Analytics aos quais seus cofres estão enviando dados para exibir relatórios para esses cofres.

## <a name="pricing"></a>Preços

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Preciso pagar algo extra para usar o Gerenciador de backup?

Atualmente, não há custos adicionais (além dos custos de backup) para usar o centro de backup. No entanto, se você estiver usando [relatórios de backup](./configure-reports.md) no centro de backup, haverá um [custo envolvido](https://azure.microsoft.com/pricing/details/monitor/) no uso de logs de Azure monitor para relatórios de backup.

## <a name="next-steps"></a>Próximas etapas

Leia as outras perguntas frequentes:

* [Perguntas comuns sobre os cofres dos serviços de recuperação](./backup-azure-backup-faq.md)
* [Perguntas comuns sobre backups de VM do Azure](./backup-azure-vm-backup-faq.md)