---
title: Recuperação de desastres para FarmBeats
description: Este artigo descreve como a recuperação de dados protege de perder seus dados.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313108"
---
# <a name="disaster-recovery-for-farmbeats"></a>Recuperação de desastres para FarmBeats

A recuperação de dados protege você de perder seus dados em um evento como o colapso da região do Azure. Nesse caso, você pode iniciar o failover e recuperar os dados armazenados na sua implantação do FarmBeats.

A recuperação de dados não é um recurso padrão no Azure FarmBeats. Você pode configurar esse recurso manualmente configurando os recursos azure necessários que são usados pelo FarmBeats para armazenar dados em uma região emparelhada do Azure. Usar ativo – Abordagem passiva para permitir a recuperação.

As seções a seguir fornecem informações sobre como você pode configurar a recuperação de dados no Azure FarmBeats:

- [Habilite a redundância de dados](#enable-data-redundancy)
- [Restaurar serviço de backup on-line](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Habilite a redundância de dados

A FarmBeats armazena dados em três serviços de primeira parte do Azure, que são **o armazenamento Azure,** **cosmos DB** e Time Series **Insights**. Use as seguintes etapas para habilitar a redundância de dados para esses serviços em uma região Azure emparelhada:

1.  **Armazenamento azure** - Siga esta diretriz para habilitar a redundância de dados para cada conta de armazenamento em sua implantação farmBbeats.
2.  **Azure Cosmos DB** - Siga esta diretriz para habilitar a redundância de dados para a conta Cosmos DB sua implantação farmbeats.
3.  **Azure Time Series Insights (TSI)** - Atualmente, a TSI não oferece redundância de dados. Para recuperar os dados do Time Series Insights, vá para o seu parceiro de sensores/tempo e empurre os dados novamente para a implantação do FarmBeats.

## <a name="restore-service-from-online-backup"></a>Restaurar serviço de backup on-line

Você pode iniciar failover e recuperar dados armazenados para os quais, cada um dos armazenamentos de dados acima mencionados para sua implantação do FarmBeats. Depois de recuperar os dados para armazenamento Azure e Cosmos DB, crie outra implantação do FarmBeats na região emparelhada do Azure e, em seguida, configure a nova implantação para usar dados de armazenamentos de dados restaurados (ou seja, Azure Storage e Cosmos DB) usando as etapas abaixo:

1. [Configurar o Cosmos DB](#configure-cosmos-db)
2. [Configurar a conta de armazenamento](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurar o Cosmos DB

Copie a chave de acesso do Cosmos DB restaurado e atualize o novo FarmBeats Datahub Key Vault.


  ![Recuperação de desastre](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copie a URL do Cosmos DB restaurado e atualize-a na nova configuração do serviço de aplicativo FarmBeats Datahub. Agora você pode excluir a conta Cosmos DB na nova implantação do FarmBeats.

  ![Recuperação de desastre](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Configurar a conta de armazenamento

Copie a chave de acesso da conta de armazenamento restaurada e atualize-a no novo FarmBeats Datahub Key Vault.

![Recuperação de desastre](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Certifique-se de atualizar o nome da conta de armazenamento no novo arquivo de configuração FarmBeats Batch VM.

![Recuperação de desastre](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Da mesma forma, se você habilitou a recuperação de dados para sua conta de armazenamento Do Accelerator, siga o passo 2 para atualizar a chave e o nome da conta de armazenamento do acelerador, na nova instância farmbeats.
