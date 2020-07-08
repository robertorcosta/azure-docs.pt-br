---
title: Preços do Backup do Azure
description: Saiba como estimar seus custos para orçar os preços do backup do Azure.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 274a61ff5a98fa1291f9d8917af9ab1d1b3da2fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391104"
---
# <a name="azure-backup-pricing"></a>Preços do Backup do Azure

Para saber mais sobre os preços do backup do Azure, visite a [página de preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Baixar estimativas detalhadas de preços do backup do Azure

Se você pretende estimar os custos para fins de comparação de orçamento ou de custo, baixe o [avaliador de preço detalhado do backup do Azure](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>O que o estimador contém?

A folha de estimação de custo do backup do Azure tem uma opção para estimar todas as cargas de trabalho possíveis que você está procurando fazer backup usando o backup do Azure. Essas cargas de trabalho incluem:

- VMs do Azure
- Servidores locais
- SQL em VMs do Azure
- SAP HANA em VMs do Azure
- Compartilhamentos de arquivos do Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Estimar os custos para fazer backup de VMs do Azure ou de servidores locais

Para estimar os custos de backup de VMs do Azure ou de servidores locais usando o backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho das VMs ou servidores locais dos quais você está tentando fazer backup
  - Insira o "tamanho usado" dos discos ou servidores necessários para o backup

- Número de servidores com esse tamanho

- Qual é a quantidade de variação de dados esperada nesses servidores?<br>
  A rotatividade refere-se à quantidade de alterações nos dados. Por exemplo, se você tivesse uma VM com 200 GB de dados para fazer backup e se 10 GB de ti forem alterados todos os dias, a rotatividade diária será de 5%.

  - A variação mais alta significa que você faz backup de mais dados

  - Selecione **baixo** ou **moderado** para servidores de arquivos e **alto** se você estiver executando bancos de dados

  - Se você conhece o **% de rotatividade**, pode usar a opção **inserir seu próprio%**

- Escolher a política de backup

  - Por quanto tempo você espera manter os backups "diários"? (em dias)

  - Por quanto tempo você espera manter backups "semanais"? (em semanas)

  - Por quanto tempo você espera manter os backups "mensais"? (em meses)

  - Por quanto tempo você espera manter os backups "anuais"? (em anos)

  - Por quanto tempo você espera manter "instantâneos de restauração instantânea"? (1-5 dias)

    - Essa opção permite que você restaure de até sete dias de maneira rápida usando instantâneos armazenados em discos

- **Opcional** – backup de disco seletivo

  - Se você estiver usando a opção de **backup de disco seletivo** ao fazer backup de VMs do Azure, escolha a opção **excluir disco** e insira a porcentagem de discos excluídos do backup em termos de tamanho. Por exemplo, se você tiver uma VM conectada a três discos com 200 GB usados em cada disco e se quiser excluir duas delas do backup, digite 66,7%.

- **Opcional** – redundância de armazenamento de backup

  - Isso indica a redundância da conta de armazenamento na qual os dados de backup entram. É recomendável usar **grs** para a maior disponibilidade. Como garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ela ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se você estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisam de um backup de nível corporativo. Selecione a opção **RAGRS** na planilha se desejar entender os custos quando a [restauração entre regiões](backup-azure-arm-restore-vms.md#cross-region-restore) estiver habilitada para seus backups.

- **Opcional** – modificar preços regionais ou aplicar tarifas com desconto

  - Se você quiser verificar suas estimativas para uma região diferente ou taxas com desconto, selecione **Sim** para a opção **experimentar estimativas para uma região diferente?** e insira as taxas com as quais você deseja executar as estimativas.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Estimar os custos para fazer backup de SQL Servers em VMs do Azure

Para estimar os custos de backup de SQL Servers em execução em VMs do Azure usando o backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho dos SQL Servers dos quais você está tentando fazer backup

- Número de servidores SQL com o tamanho acima

- Qual é a compactação esperada para os dados de backup dos seus servidores SQL?

  - A maioria dos clientes de backup do Azure vê que os dados de backup têm 80% de compactação em comparação com o tamanho do SQL Server quando a compactação do SQL está **habilitada**.

  - Se você espera ver uma compactação diferente, insira o número neste campo

- Qual é o tamanho esperado de backups de log?

  - O% indica o tamanho diário do log como um% do tamanho do SQL Server

- Qual é a quantidade esperada de rotatividade de dados diária nesses servidores?

  - Normalmente, os bancos de dados têm rotatividade "alta"

  - Se você conhece o **% de rotatividade**, pode usar a opção **inserir seu próprio%**

- Escolher a política de backup

  - Tipo de backup

    - A política mais eficaz que você pode escolher é **diferencial diária** com backups completos semanais/mensais/anuais. O backup do Azure também pode ser restaurado de diferenciais por meio de um único clique.

    - Você também pode optar por ter uma política com backups completos diários/semanais/mensais/anuais. Esta opção consumirá um pouco mais de armazenamento do que a primeira opção.

  - Por quanto tempo você espera manter os backups de "log"? (em dias) [7-35]

  - Por quanto tempo você espera manter os backups "diários"? (em dias)

  - Por quanto tempo você espera manter backups "semanais"? (em semanas)

  - Por quanto tempo você espera manter os backups "mensais"? (em meses)

  - Por quanto tempo você espera manter os backups "anuais"? (em anos)

- **Opcional** – redundância de armazenamento de backup

  - Isso indica a redundância da conta de armazenamento na qual os dados de backup entram. É recomendável usar **grs** para a maior disponibilidade. Como garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ela ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se você estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisam de um backup de nível corporativo.

- **Opcional** – modificar preços regionais ou aplicar tarifas com desconto

  - Se você quiser verificar suas estimativas para uma região diferente ou taxas com desconto, selecione **Sim** para a opção **experimentar estimativas para uma região diferente?** e insira as taxas com as quais você deseja executar as estimativas.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Estimar os custos para fazer backup de servidores de SAP HANA em VMs do Azure

Para estimar os custos de backup de SAP HANA servidores em execução em VMs do Azure usando o backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho total dos bancos de dados SAP HANA que você está tentando fazer backup. Essa deve ser a soma do tamanho de backup completo de cada um dos bancos de dados, conforme relatado pelo SAP HANA.
- Número de servidores de SAP HANA com o tamanho acima
- Qual é o tamanho esperado de backups de log?
  - O% indica o tamanho médio diário do log como um% do tamanho total dos bancos de dados SAP HANA que você está fazendo backup no servidor SAP HANA
- Qual é a quantidade esperada de rotatividade de dados diária nesses servidores?
  - O% indica o tamanho médio de rotatividade diária como um% do tamanho total dos bancos de dados SAP HANA que você está fazendo backup no servidor SAP HANA
  - Normalmente, os bancos de dados têm rotatividade "alta"
  - Se você conhece o **% de rotatividade**, pode usar a opção **inserir seu próprio%**
- Escolher a política de backup
  - Tipo de backup
    - A política mais eficaz que você pode escolher é **diferencial diária** com backups completos **semanais/mensais/anuais** . O backup do Azure também pode ser restaurado de diferenciais por meio de um único clique.
    - Você também pode optar por ter uma política com backups completos **diários/semanais/mensais/anuais** . Esta opção consumirá um pouco mais de armazenamento do que a primeira opção.
  - Por quanto tempo você espera manter os backups de "log"? (em dias) [7-35]
  - Por quanto tempo você espera manter os backups "diários"? (em dias)
  - Por quanto tempo você espera manter backups "semanais"? (em semanas)
  - Por quanto tempo você espera manter os backups "mensais"? (em meses)
  - Por quanto tempo você espera manter os backups "anuais"? (em anos)
- **Opcional** – redundância de armazenamento de backup
  - Isso indica a redundância da conta de armazenamento na qual os dados de backup entram. É recomendável usar **grs** para a maior disponibilidade. Como garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ela ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se você estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisam de um backup de nível corporativo.
- **Opcional** – modificar preços regionais ou aplicar tarifas com desconto
  - Se você quiser verificar suas estimativas para uma região diferente ou taxas com desconto, selecione **Sim** para a opção **experimentar estimativas para uma região diferente?** e insira as taxas com as quais você deseja executar as estimativas.

## <a name="next-steps"></a>Próximas etapas

[O que é o serviço de Backup do Azure?](backup-overview.md)
