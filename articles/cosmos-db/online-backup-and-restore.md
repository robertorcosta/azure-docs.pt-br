---
title: Backup on-line e restauração de dados sob demanda no Azure Cosmos DB
description: Este artigo descreve como o backup automático, a restauração de dados sob demanda funciona, como configurar o intervalo de backup e a retenção em Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 54bbd5d45e14c1d345570eea9dc5469f77694154
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853928"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup on-line e restauração de dados sob demanda no Azure Cosmos DB

O Azure Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade das operações do banco de dados. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. Os backups automáticos são úteis em cenários quando você acidentalmente exclui ou atualiza sua conta, banco de dados ou contêiner do Azure Cosmos e, posteriormente, exige a recuperação de dados.

## <a name="automatic-and-online-backups"></a>Backups automáticos e online

Com o Azure Cosmos DB, não apenas seus dados, mas também os backups de seus dados são altamente redundantes e resilientes a desastres regionais. As etapas a seguir mostram como o Azure Cosmos DB executa o backup de dados:

* Azure Cosmos DB faz automaticamente um backup de seu banco de dados a cada 4 horas e a qualquer momento, somente os dois backups mais recentes são armazenados por padrão. Se os intervalos padrão não forem suficientes para suas cargas de trabalho, você poderá alterar o intervalo de backup e o período de retenção do portal do Azure. Você pode alterar a configuração de backup durante ou após a criação da conta do Azure Cosmos. Se o contêiner ou banco de dados for excluído, Azure Cosmos DB manterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias.

* O Azure Cosmos DB armazena esses backups no armazenamento de BLOBs do Azure, enquanto os dados reais residem localmente no Azure Cosmos DB.

* Para garantir baixa latência, o instantâneo do backup é armazenado no armazenamento de BLOBs do Azure na mesma região que a região de gravação atual (ou **uma** das regiões de gravação, caso você tenha uma configuração de vários mestres). Para resiliência contra desastres regionais, cada captura instantânea dos dados de backup no armazenamento do Azure Blob é novamente replicada para outra região por meio de armazenamento geo-redundante (GRS). A região na qual o backup é replicado é baseada em sua região de origem e no par regional associado à região de origem. Para saber mais, consulte a [lista de artigos de pares geo-redundantes de regiões do Azure](../best-practices-availability-paired-regions.md). Você não pode acessar esse backup diretamente. Azure Cosmos DB equipe irá restaurar o backup quando você solicitar por meio de uma solicitação de suporte.

   A imagem a seguir mostra como é feito o backup de um contêiner do Azure Cosmos com todas as três partições físicas primárias no oeste dos EUA em uma conta remota do Armazenamento de Blobs do Azure no oeste dos EUA e, em seguida, replicada para o leste dos EUA:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Backups completos periódicos de todas as entidades do Cosmos DB no Armazenamento do Azure GRS" border="false":::

* Os backups são feitos sem afetar o desempenho ou a disponibilidade de seu aplicativo. O Azure Cosmos DB executa backup de dados em segundo plano sem consumir nenhuma taxa de transferência provisionada (RUs) adicional ou afetar o desempenho e a disponibilidade de seu banco de dados.

## <a name="options-to-manage-your-own-backups"></a>Opções para gerenciar seus próprios backups

Com as contas da API do Azure Cosmos DB SQL, você também pode manter seus próprios backups usando uma das seguintes abordagens:

* Use o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados periodicamente para um armazenamento de sua escolha.

* Use Azure Cosmos DB [feed de alterações](change-feed.md) para ler dados periodicamente para backups completos ou para alterações incrementais e armazená-los em seu próprio armazenamento.

## <a name="modify-the-backup-interval-and-retention-period"></a>Modificar o intervalo de backup e o período de retenção

Azure Cosmos DB faz automaticamente um backup de seus dados para cada 4 horas e a qualquer momento, os dois backups mais recentes são armazenados. Essa configuração é a opção padrão e é oferecida sem nenhum custo adicional. Você pode alterar o intervalo de backup padrão e o período de retenção durante a criação da conta do Azure Cosmos ou após a criação da conta. A configuração de backup é definida no nível da conta do Azure Cosmos e você precisa configurá-la em cada conta. Depois de configurar as opções de backup para uma conta, ela é aplicada a todos os contêineres dentro dessa conta. No momento, você pode alterar as opções de backup somente de portal do Azure.

Se você acidentalmente excluiu ou danificou seus dados, **antes de criar uma solicitação de suporte para restaurar os dados, certifique-se de aumentar a retenção de backup para sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas desse evento.** Dessa forma, a equipe de Azure Cosmos DB tem tempo suficiente para restaurar sua conta.

Use as etapas a seguir para alterar as opções de backup padrão para uma conta existente do Azure Cosmos:

1. Entre no [portal do Azure.](https://portal.azure.com/)
1. Navegue até sua conta do Azure Cosmos e abra o painel **Backup & restauração** . Atualize o intervalo de backup e o período de retenção de backup conforme necessário.

   * **Intervalo de backup** – é o intervalo no qual Azure Cosmos DB tenta fazer um backup dos dados. O backup leva um período de tempo diferente de zero e, em algum caso, poderia falhar possivelmente devido a dependências de downstream. O Azure Cosmos DB tenta o melhor fazer um backup no intervalo configurado, no entanto, não garante que o backup seja concluído dentro desse intervalo de tempo. Você pode configurar esse valor em horas ou minutos. O intervalo de backup não pode ser inferior a 1 hora e maior que 24 horas. Quando você altera esse intervalo, o novo intervalo entra em vigor a partir da hora em que o último backup foi feito.

   * **Retenção de backup** – representa o período em que cada backup é retido. Você pode configurá-lo em horas ou dias. O período de retenção mínimo não pode ser menor que duas vezes o intervalo de backup (em horas) e não pode ser maior que 720 horas.

   * **Cópias de dados retidas** -por padrão, duas cópias de backup de seus dados são oferecidas gratuitamente. Se você precisar de cópias adicionais, deverá criar uma solicitação de suporte por meio do portal do Azure e as cópias adicionais serão cobradas. Consulte a seção armazenamento consumido na [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o preço exato de cópias adicionais.

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Configurar o intervalo de backup e a retenção de uma conta existente do Azure Cosmos" border="true":::

Se você configurar as opções de backup durante a criação da conta, poderá configurar a **política de backup**, que é **periódica** ou **contínua**. A política periódica permite que você configure o intervalo de backup e a retenção de backup. Atualmente, a política contínua está disponível apenas para inscrição. A equipe de Azure Cosmos DB avaliará sua carga de trabalho e aprovará sua solicitação.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Configurar a política de backup periódica ou contínua para novas contas do Azure Cosmos" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Restaurar dados de um backup online

Você pode excluir ou modificar seus dados acidentalmente em um dos seguintes cenários:  

* Exclua toda a conta do Azure Cosmos.

* Exclua um ou mais bancos de dados do Azure Cosmos.

* Exclua um ou mais contêineres de Cosmos do Azure.

* Exclua ou modifique os itens Cosmos do Azure (por exemplo, documentos) dentro de um contêiner. Esse caso específico é normalmente chamado de corrupção de dados.

* Um banco de dados de oferta compartilhado ou contêineres em um banco de dados de oferta compartilhado são excluídos ou corrompidos.

O Azure Cosmos DB pode restaurar dados em todos os cenários acima. Durante a restauração, uma nova conta do Azure cosmos é criada para armazenar os dados restaurados. O nome da nova conta, se não for especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1` . O último dígito é incrementado quando várias restaurações são tentadas. Não é possível restaurar dados para uma conta do Azure Cosmos criada previamente.

Quando você exclui acidentalmente uma conta do Azure Cosmos, podemos restaurar os dados em uma nova conta com o mesmo nome, desde que o nome da conta não esteja em uso. Portanto, recomendamos que você não crie novamente a conta depois de excluí-la. Porque ele não apenas impede que os dados restaurados usem o mesmo nome, mas também torna a descoberta da conta correta para restaurar de difícil.

Quando você exclui acidentalmente um banco de dados Cosmos do Azure, podemos restaurar todo o banco de dados ou um subconjunto dos contêineres dentro desse banco de dados. Também é possível selecionar contêineres específicos em bancos de dados e restaurá-los para uma nova conta do Azure Cosmos.

Quando você exclui ou modifica acidentalmente um ou mais itens dentro de um contêiner (o caso de corrupção de dados), precisa especificar a hora de restauração. O tempo é importante se houver dados corrompidos. Como o contêiner está ativo, o backup ainda está em execução. portanto, se você aguardar além do período de retenção (o padrão é de oito horas), os backups serão substituídos. **Para impedir que o backup seja substituído, aumente a retenção de backup da sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas a partir da corrupção de dados.**

Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups. Dessa forma, a equipe de suporte do Azure Cosmos DB terá tempo suficiente para restaurar sua conta.

Se você provisionar a taxa de transferência no nível do banco de dados, o processo de backup e restauração, nesse caso, ocorrerá em todo o nível do banco de dados, e não no nível de contêineres individuais. Nesses casos, você não pode selecionar um subconjunto de contêineres para restaurar.

## <a name="migrate-data-to-the-original-account"></a>Migrar dados para a conta original

O objetivo principal da restauração de dados é recuperar os dados que você excluiu ou modificou acidentalmente. Portanto, recomendamos que você primeiro inspecione o conteúdo dos dados recuperados para garantir que ele contenha o que você está esperando. Posteriormente, você pode migrar os dados de volta para a conta primária. Embora seja possível usar a conta restaurada como sua nova conta ativa, ela não será uma opção recomendada se você tiver cargas de trabalho de produção.  

Estas são maneiras diferentes de migrar dados de volta para a conta original do Azure Cosmos:

* Use a [ferramenta de migração de dados Azure Cosmos DB](import-data.md).
* Use o [Azure data Factory](../data-factory/connector-azure-cosmos-db.md).
* Use o [feed de alterações](change-feed.md) no Azure Cosmos DB.
* Você pode escrever seu próprio código personalizado.

Certifique-se de excluir as contas restauradas assim que tiver migrado seus dados, pois eles incorrerão em encargos contínuos.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como restaurar dados de uma conta do Azure Cosmos ou aprender como migrar dados para uma conta do Azure Cosmos

* Para fazer uma solicitação de restauração, entre em contato com o Suporte do Azure, [arquive um ticket no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados de uma conta do Azure Cosmos](how-to-backup-and-restore.md)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para o Azure Cosmos DB.
* [Use o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.

