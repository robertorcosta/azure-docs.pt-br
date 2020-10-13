---
title: Como restaurar dados do Azure Cosmos DB de um backup
description: Este artigo descreve como restaurar dados do Azure Cosmos DB a partir de um backup, como entrar em contato com o suporte do Azure para restaurar dados, etapas a serem seguidas após a restauração dos dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797965"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar dados do backup no Azure Cosmos DB

Se você excluir acidentalmente seu banco de dados ou um contêiner, poderá [arquivar um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [chamar o suporte do Azure](https://azure.microsoft.com/support/options/) para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como **Standard**, **Developer**e Plans maiores do que os. Suporte do Azure não está disponível com plano **Basic**. Para saber mais sobre os diferentes planos de suporte, consulte a página [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

## <a name="request-a-restore"></a>Solicitar uma restauração

Você deve ter os seguintes detalhes antes de solicitar uma restauração:

* Ter sua ID de assinatura pronta.

* Com base em como seus dados foram excluídos ou modificados acidentalmente, você deve se preparar para obter informações adicionais. É aconselhável que você tenha as informações disponíveis à frente para minimizar o processo de back-and-forth que pode ser prejudicial em alguns casos sensíveis ao tempo.

* Se toda a conta do Azure Cosmos DB for excluída, você precisará fornecer o nome da conta excluída. Se você criar outra conta com o mesmo nome da conta excluída, compartilhe-a com a equipe de suporte, pois isso ajudará a determinar a conta certa a ser escolhida. É recomendável arquivar tíquetes de suporte diferentes para cada conta excluída porque minimiza a confusão para o estado da restauração.

* Se um ou mais bancos de dados forem excluídos, você deverá fornecer a conta do Azure Cosmos, bem como os nomes do banco de dados do Azure Cosmos, e especificar se existe um novo banco de dados com o mesmo nome.

* Se um ou mais contêineres forem excluídos, você deverá fornecer o nome da conta do Azure Cosmos, os nomes do banco de dados e os nomes dos contêineres. E especifique se existe um contêiner com o mesmo nome.

* Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups. **Antes de criar uma solicitação de suporte para restaurar os dados, certifique-se de [aumentar a retenção de backup](online-backup-and-restore.md) para sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas desse evento.** Dessa forma, a equipe de suporte do Azure Cosmos DB terá tempo suficiente para restaurar sua conta.

Além do nome da conta do Azure Cosmos, nomes de banco de dados, nomes de contêiner, você deve especificar o ponto no tempo para o qual os dados podem ser restaurados. É importante ser o mais preciso possível para nos ajudar a determinar os melhores backups disponíveis naquele momento. **Também é importante especificar o horário no UTC.**

A captura de tela a seguir ilustra como criar uma solicitação de suporte para um contêiner (coleção/gráfico/tabela) para restaurar dados usando o portal do Azure. Forneça detalhes adicionais, como tipo de dados, finalidade da restauração, horário em que os dados foram excluídos para nos ajudar a priorizar a solicitação.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Criar uma solicitação de suporte de backup usando o portal do Azure":::

## <a name="post-restore-actions"></a>Ações de pós-restauração

Depois de restaurar os dados, você recebe uma notificação sobre o nome da nova conta (normalmente, no formato `<original-name>-restored1`) e a hora em que a conta foi restaurada. A conta restaurada terá o mesmo rendimento provisionado, políticas de indexação e está na mesma região da conta original. Um usuário que é o administrador da assinatura ou um coadministrador pode ver a conta restaurada.

Depois que os dados são restaurados, você deve inspecionar e validar os dados na conta restaurada e certificar-se de que ela contenha a versão que você está esperando. Se tudo estiver bem, você deve migrar os dados de volta para sua conta original usando o [feed de alteração do Azure Cosmos DB](change-feed.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

É aconselhável excluir o contêiner ou banco de dados imediatamente após a migração dos dados. Se você não excluir os bancos de dados ou contêineres restaurados, eles incorrerão em custos para unidades de solicitação, armazenamento e saída.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender sobre como migrar os dados de volta para sua conta original usando os seguintes artigos:

* Para fazer uma solicitação de restauração, entre em contato com o Suporte do Azure, [arquive um ticket no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para o Azure Cosmos DB.

* [Use o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.
