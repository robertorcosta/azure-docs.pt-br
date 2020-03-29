---
title: Funcionalidade de restauração instantânea do Azure
description: Funcionalidade de restauração instantânea do Azure e perguntas frequentes para pilha de backup de VM, modelo de implantação do Gerenciador de Recursos
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705421"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Obter o melhor backup e restaurar o desempenho com a funcionalidade de restauração instantânea do Backup do Azure

> [!NOTE]
> Com base nos comentários dos usuários, estamos renomeando a **pilha de backup de VM V2** como **Restauração Instantânea** para diminuir a confusão com a funcionalidade do Azure Stack.
> Todos os usuários de backup do Azure foram atualizados para **o Instant Restore**.

O novo modelo para Restauração instantânea oferece os seguintes aprimoramentos de recursos:

* Capacidade de usar o instantâneo tirado como parte do trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados para o cofre. Isso reduz o tempo de espera para instantâneos para copiar para o cofre antes de disparar a restauração.
* Reduz os tempos de backup e restauração mantendo os instantâneos localmente. O padrão é de dois dias. Este valor de retenção de instantâneo padrão é configurável para qualquer valor entre 1 e 5 dias.
* Suporta tamanhos de disco de até 32 TB. O redimensionamento de discos não é recomendado pelo Azure Backup.
* Suporta discos SSD padrão, juntamente com discos HDD padrão e discos SSD Premium.
* Capacidade de usar uma vms não gerenciada contas de armazenamento originais (por disco), ao restaurar. Essa capacidade existe mesmo quando a VM possui discos distribuídos em contas de armazenamento. Isso acelera as operações de restauração para uma ampla variedade de configurações de VM.
* Para backup de VMs que estão usando armazenamento premium, com Restauração Instantânea, recomendamos alocar *50%* de espaço livre do espaço total alocado de armazenamento, que é necessário **apenas** para o primeiro backup. O espaço livre de 50% não é um requisito para backups após a conclusão do primeiro backup.

## <a name="whats-new-in-this-feature"></a>Novidades deste recurso

Hoje, o trabalho de backup consiste em duas fases:

1. Obter instantâneo da VM.
2. Transferir o instantâneo da VM para o cofre dos Serviços de Recuperação.

Um ponto de recuperação é considerado criado somente após as fases 1 e 2 estarem concluídas. Como parte dessa atualização, um ponto de recuperação é criado assim que o instantâneo é concluído, e esse ponto de recuperação do tipo de instantâneo pode ser usado para executar uma restauração usando o mesmo fluxo de restauração. Você pode identificar esse ponto de recuperação no portal Azure usando "snapshot" como o tipo de ponto de recuperação, e depois que o snapshot é transferido para o cofre, o tipo de ponto de recuperação muda para "snapshot e vault".

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - armazenar e cofre](./media/backup-azure-vms/instant-rp-flow.png)

Por padrão, os instantâneos são retidos por dois dias. Esse recurso permite restaurar a operação desses instantâneos, reduzindo os tempos de restauração. Reduz o tempo necessário para transformar e copiar dados do cofre.

## <a name="feature-considerations"></a>Considerações do recurso

* Instantâneos são armazenados com os discos para aumentar a criação do ponto de recuperação e acelerar as operações de restauração. Como resultado, você vê os custos de armazenamento correspondentes aos instantâneos obtidos durante esse período.
* Os instantâneos incrementais são armazenados como blobs de página. Todos os usuários de discos não gerenciados são cobrados pelos instantâneos armazenados na conta de armazenamento local. Uma vez que as coleções de ponto de restauração usadas por backups de VM gerenciada usam instantâneos de blob no nível de armazenamento subjacente, para discos gerenciados, você vê os custos correspondentes a preços de instantâneo de blob, e eles são incrementais.
* Para contas de armazenamento premium, os instantâneos obtidos para pontos de recuperação instantâneos contam para o limite de 10 TB de espaço alocado.
* Você consegue configurar a retenção de instantâneos com base nas necessidades de restauração. Dependendo do requisito, você pode definir a retenção de instantâneos como, no mínimo, um dia na folha de política de backup, conforme explicado abaixo. Isso ajudará você a economizar custos para retenção de instantâneos se você não executar restaurações com freqüência.
* É um upgrade one direcional, uma vez atualizado para restauração instantânea, você não pode voltar atrás.

>[!NOTE]
>Com o upgrade da restauração instantânea, a duração da retenção de instantâneos para todos os clientes (**tanto novos quanto existentes**) será definida com um valor padrão de dois dias. No entanto, você pode definir a duração, de acordo com sua necessidade, como qualquer valor entre 1 e 5 dias.

## <a name="cost-impact"></a>Impacto de custo

Os instantâneos incrementais são armazenados na conta de armazenamento da VM, que é usada para recuperação instantânea. Instantâneo incremental significa que o espaço ocupado por um instantâneo é igual ao espaço ocupado por páginas escritas depois que o instantâneo foi criado. O faturamento ainda é para o espaço usado por GB ocupado pelo snapshot, e o preço por GB é o mesmo mencionado na [página de preços](https://azure.microsoft.com/pricing/details/managed-disks/). Para VMs que usam discos não gerenciados, os instantâneos podem ser vistos no menu para o arquivo VHD de cada disco. Para discos gerenciados, os snapshots são armazenados em um recurso de coleta de pontos de restauração em um grupo de recursos designado, e os instantâneos em si não são diretamente visíveis.

>[!NOTE]
> A retenção de instantâneos é fixada em 5 dias para políticas semanais.

## <a name="configure-snapshot-retention"></a>Configurar retenção de instantâneos

### <a name="using-azure-portal"></a>Usando o Portal do Azure

No portal Azure, você pode ver um campo adicionado na lâmina **de diretiva de backup vm** na seção **Restauração instantânea.** Você pode alterar a duração da retenção de instantâneos na folha **Política de Backup da VM** para todas as VMs associadas à política de backup específica.

![Funcionalidade de restauração instantânea](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Usando o PowerShell

>[!NOTE]
> A partir da versão 1.6.0 do Az PowerShell em diante, você pode atualizar o período de retenção instantânea de instantâneos na diretiva usando o PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

A retenção de instantâneo padrão para cada diretiva é definida como de dois dias. O usuário pode alterar o valor para um mínimo de 1 e um máximo de cinco dias. Para políticas semanais, a retenção de instantâneos é fixada em cinco dias.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quais são as implicações de custo da Restauração instantânea?

Instantâneos são armazenados com os discos para acelerar a criação do ponto de recuperação e as operações de restauração. Como resultado, você vê os custos de armazenamento que correspondem à retenção de instantâneo selecionada como parte da política de backup de VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, os instantâneos obtidos para o ponto de Recuperação instantânea ocupam o limite de instantâneos de 10 TB?

Sim, para contas de armazenamento premium, os instantâneos tirados para ponto de recuperação instantânea ocupam 10 TB de espaço de instantâneo alocado.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Como funciona o trabalho de retenção de instantâneo durante o período de cinco dias?

Cada dia um novo instantâneo é obtido. Em um determinado momento, há cinco instantâneos incrementais individuais. O tamanho do snapshot depende do churn de dados, que na maioria dos casos estão em torno de 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo de Restauração instantânea é um instantâneo incremental ou um instantâneo completo?

Os instantâneos tirados como parte da funcionalidade de Restauração instantânea são instantâneos incrementais.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Como é possível calcular o aumento de custo aproximado devido à funcionalidade da Restauração instantânea?

Isso depende da variação da VM. Em um estado estável, é possível assumir que o aumento do custo seja: = Variação diária do período de retenção de instantâneos pelo custo de armazenamento da VM por GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se o tipo de recuperação para um ponto de restauração é "Instantâneo e cofre" e eu posso executar uma operação de restauração, qual tipo de recuperação será usado?

Se o tipo de recuperação é "instantâneo e cofre", a restauração é feita automaticamente a partir do instantâneo local, o que é muito mais rápido comparado à restauração feita do cofre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>O que acontece se eu selecionar o período de retenção do ponto de restauração (Tier2) menor que o período de retenção instantâneo (Tier1)?

O novo modelo não permite excluir o ponto de restauração (Tier2), a menos que o instantâneo (Tier1) seja excluído. Recomendamos agendar o período de retenção do ponto de restauração (Camada 2) de forma a ser mais longo que o período de retenção de instantâneos.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Por que meu instantâneo existe mesmo após o período de retenção definido na política de backup?

Se o ponto de recuperação tem um instantâneo e esse é o período de retenção mais recente disponível, ele é mantido até o momento em que houver um backup bem-sucedido. Isso é de acordo com a política de "coleta de lixo" (GC) projetada hoje que obriga pelo menos um RP mais recente a estar sempre presente no caso de todos os backups mais adiante falharem devido a um problema na VM. Em cenários normais, os períodos de retenção são limpos no máximo 24 horas após sua expiração.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Não preciso da funcionalidade de Restauração Instantânea. Pode ser desativado?

O recurso de restauração instantânea está ativado para todos e não pode ser desativado. Você pode reduzir a retenção de instantâneos para um mínimo de um dia.

>[!NOTE]
> **O Azure Backup agora suporta backup e restauração de disco seletivo usando a solução de backup da Máquina Virtual do Azure.**
>
>Hoje, o Azure Backup suporta backup de todos os discos (Sistema Operacional e dados) em uma VM em conjunto usando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, você tem a opção de fazer backup de um ou poucos dos muitos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda que você tenha um subconjunto de discos restaurado do ponto de recuperação dado durante a operação de restauração. Isso se aplica para restaurar tanto a partir do instantâneo quanto do cofre.
>
>**Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com**
