---
title: Matriz de suporte do Backup do Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273261"
---
# <a name="support-matrix-for-azure-backup"></a>Matriz de suporte para backup do Azure

Você pode usar [o Azure Backup](backup-overview.md) para fazer backup de dados na plataforma de nuvem do Microsoft Azure. Este artigo resume as configurações e limitações gerais de suporte para cenários e implantações do Azure Backup.

Outras matrizes de suporte estão disponíveis:

- Matriz de suporte para [backup da máquina virtual Azure (VM)](backup-support-matrix-iaas.md)
- Matriz de suporte para backup usando [o DPM (System Center Data Protection Manager) /Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para backup usando o [agente Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Suporte de cofre

O Azure Backup usa cofres do Recovery Services para orquestrar e gerenciar backups. Ele também usa cofres para armazenar dados de backup.

A tabela a seguir descreve as características dos cofres dos Serviços de Recuperação:

**Recurso** | **Detalhes**
--- | ---
**Cofres na assinatura** | Até 500 cofres dos Serviços de Recuperação em uma assinatura única.
**Computadores em um cofre** | Até 1.000 VMs Azure em um único cofre.<br/><br/> Até 50 servidores MABS podem ser registrados em um único cofre.
**Fontes de dados** | O tamanho máximo de uma fonte de [dados](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) individual é de 54.400 GB. Esse limite não se aplica aos backups do Azure VM. Nenhum limite se aplica à quantidade total de dados que você pode fazer backup para o cofre.
**Backups no cofre** | **VMs azure:** Uma vez por dia.<br/><br/>**Máquinas protegidas pelo DPM/MABS:** Duas vezes por dia.<br/><br/> **Máquinas apoiadas diretamente usando o agente MARS:** Três vezes por dia.
**Backups entre cofres** | O backup é dentro de uma região.<br/><br/> Você precisa de um cofre em cada região do Azure que contenha VMs que você deseja fazer backup. Você não pode fazer backup em uma região diferente.
**Mova cofres** | Você pode [mover cofres](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) através de assinaturas ou entre grupos de recursos na mesma assinatura. No entanto, a movimentação de cofres entre regiões não é suportada.
**Mover dados entre cofres** | A movimentação de dados de backup entre cofres não é suportada.
**Modificar o tipo de armazenamento do cofre** | Você pode modificar o tipo de replicação de armazenamento (armazenamento geo-redundante ou armazenamento localmente redundante) para um cofre antes que os backups sejam armazenados. Após o início dos backups no cofre, o tipo de replicação não poderá ser modificado.

## <a name="on-premises-backup-support"></a>Suporte de backup local

Aqui está o que é suportado se você quiser fazer backup de máquinas no local:

**Computador** | **O que está em backup** | **Local** | **Características**
--- | --- | --- | ---
**Backup direto de computador Windows com o agente MARS** | Arquivos, pastas, estado do sistema | Volte para o cofre dos Serviços de Recuperação. | Recue três vezes por dia<br/><br/> Sem backup com reconhecimento de aplicativos<br/><br/> Restaurar arquivo, pasta, volume
**Backup direto de computador Linux com o agente MARS** | Backup não suportado
**Volte para o DPM** | Arquivos, pastas, volumes, estado do sistema, dados do aplicativo | Fazer backup em armazenamento local do DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade total para backup e recuperação<br/><br/> Suporte ao Linux para VMs (Hyper-V/VMware)<br/><br/> Oracle não suportado
**Volte para o MABS** | Arquivos, pastas, volumes, estado do sistema, dados do aplicativo | Fazer backup no armazenamento local do MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade total para backup e recuperação<br/><br/> Suporte ao Linux para VMs (Hyper-V/VMware)<br/><br/> Oracle não suportado

## <a name="azure-vm-backup-support"></a>Suporte de backup de VM do Azure

### <a name="azure-vm-limits"></a>Limites de VM do Azure

**Limite** | **Detalhes**
--- | ---
**Discos de dados de VM do Azure** | Limite de 16 <br> Para se inscrever para a versão prévia privada de VMs com mais de 16 discos (até 32 discos), escreva para nós em AskAzureBackupTeam@microsoft.com
**Tamanho do disco de dados de VM do Azure** | O tamanho do disco individual pode ser de até 32 TB e um máximo de 256 TB combinados para todos os discos em uma VM.

### <a name="azure-vm-backup-options"></a>Opções de backup de VM do Azure

Aqui está o que é suportado se você quiser fazer backup das VMs do Azure:

**Computador** | **O que está em backup** | **Local** | **Características**
--- | --- | --- | ---
**Backup do Azure VM usando extensão VM** | Toda a VM | Volte para o cofre. | Extensão instalada quando você habilita o backup de uma VM.<br/><br/> Recue uma vez por dia.<br/><br/> Backup com reconhecimento de aplicativos para VMs do Windows; backup consistente com arquivos para VMs Linux. Você pode configurar a consistência do aplicativo para máquinas Linux usando scripts personalizados.<br/><br/> Restaurar VM ou disco.<br/><br/> Não é possível fazer backup de uma VM Azure para um local no local.
**Backup do Azure VM usando o agente MARS** | Arquivos, pastas, estado do sistema | Volte para o cofre. | Recue três vezes por dia.<br/><br/> Se você quiser fazer backup de arquivos ou pastas específicas em vez de toda a VM, o agente MARS pode ser executado ao lado da extensão VM.
**VM do Azure com o DPM** | Arquivos, pastas, volumes, estado do sistema, dados do aplicativo | Volte para o armazenamento local da Azure VM que está executando o DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativos.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.
**VM do Azure com o MABS** | Arquivos, pastas, volumes, estado do sistema, dados do aplicativo | Volte para o armazenamento local da Azure VM que está executando o MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativos.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.

## <a name="linux-backup-support"></a>Suporte de backup do Linux

Aqui está o que é suportado se você quiser fazer backup de máquinas Linux:

**Tipo de backup** | **Linux (endossado pelo Azure)**
--- | ---
**Backup direto da máquina local que está executando o Linux** | Sem suporte. O agente MARS só pode ser instalado em máquinas Windows.
**Usando a extensão do agente para fazer backup da VM do Azure que está executando o Linux** | Backup consistente com aplicativos usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação em nível de arquivo.<br/><br/> Faça a restauração com a criação de uma VM de um ponto de recuperação ou um disco.
**Usando o DPM para fazer backup de máquinas locais executando o Linux** | Backup consistente de arquivos de VMs convidados linux no Hyper-V e VMWare.<br/><br/> VM restauração de VM de VMs hyper-V e VMWare Linux Guest VMs.
**Usando o MABS para fazer backup de máquinas locais executando o Linux** | Backup consistente de arquivos de VMs convidados linux no Hyper-V e VMWare.<br/><br/> VM restauração de VM de VMs convidados Hyper-V e VMWare Linux.
**Usando MABS ou DPM para fazer backup de VMs Linux Azure** | Sem suporte.

## <a name="daylight-saving-time-support"></a>Suporte ao horário de verão

O Azure Backup não suporta ajuste automático do relógio para o horário de verão para backups do Azure VM. Ele não muda a hora do backup para frente ou para trás. Para garantir que o backup seja executado no momento desejado, modifique as políticas de backup manualmente conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte de eliminação de duplicação de disco

O suporte de eliminação de duplicação de disco ocorre da seguinte maneira:

- A deduplicação de disco é suportada no local quando você usa DPM ou MABs para fazer backup de VMs Hyper-V que estão executando o Windows. O Windows Server executa a deduplicação de dados (no nível do host) em discos rígidos virtuais (VHDs) que são anexados à VM como armazenamento de backup.
- Não há suporte para a eliminação de duplicação no Azure para qualquer componente de Backup. Quando o DPM e o MABS são implantados no Azure, os discos de armazenamento conectados à VM não podem ser duplicados.

## <a name="security-and-encryption-support"></a>Suporte de segurança e criptografia

O Azure Backup suporta criptografia para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para Azure

- O tráfego de backup de servidores para o cofre dos Serviços de Recuperação é criptografado usando o Advanced Encryption Standard 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação em forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

    > [!WARNING]
    > Depois de configurar o cofre, só você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

### <a name="data-security"></a>Segurança de dados

- Quando você está fazendo backup das VMs do Azure, você precisa configurar criptografia *dentro* da máquina virtual.
- O Backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais Windows e **dm-crypt** em máquinas virtuais Linux.
- No back-end, o Azure Backup usa [o Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), que protege os dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
**Computadores Windows locais sem o DPM/MABS** | ![Sim][green] | ![Sim][green]
**Azure VMs** | ![Sim][green] | ![Sim][green]
**Máquinas Windows ou VMs Azure com DPM** | ![Sim][green] | ![Sim][green]
**Máquinas Windows ou VMs Azure com MABS** | ![Sim][green] | ![Sim][green]

## <a name="compression-support"></a>Suporte à compactação

O backup suporta a compactação do tráfego de backup, conforme resumido na tabela a seguir.

- Para as VMs do Azure, a extensão VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento, portanto não é necessário comprimir esse tráfego.
- Se você estiver usando DPM ou MABS, você pode economizar largura de banda comprimindo os dados antes que ele seja feito backup.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar para cofre (HTTPS)**
--- | --- | ---
**Backup direto em computadores locais do Windows** | NA | ![Sim][green]
**Backup de VMs do Azure usando extensão VM** | NA | NA
**Backup em máquinas on-premises/Azure usando MABS/DPM** | ![Sim][green] | ![Sim][green]

## <a name="retention-limits"></a>Limites de retenção

**Configuração** | **Limites**
--- | ---
**Máximo de pontos de recuperação por instância protegida (máquina ou carga de trabalho)** | 9,999
**Tempo máximo de expiração para um ponto de recuperação** | Sem limite
**Frequência máxima de backup para o DPM/MABS** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência máxima de backup para o cofre** | **Máquinas Windows ou VMs Azure em execução de MARS:** Três por dia.<br/><br/> **DPM/MABS:** Dois por dia.<br/><br/> **Backup do Azure VM:** Um por dia
**Retenção de ponto de recuperação** | Diária, semanal, mensal, anual
**Período de retenção máximo** | Depende da frequência de backup
**Pontos de recuperação no disco do DPM/MABS** | 64 para servidores de arquivos; 448 para servidores de aplicativos <br/><br/>Pontos de recuperação de fita ilimitados para DPM no local

## <a name="cross-region-restore"></a>Restauração da região cruzada

O Azure Backup adicionou o recurso De restauração de região cruzada para fortalecer a disponibilidade de dados e a capacidade de resiliência, dando aos clientes controle total para restaurar dados em uma região secundária. Para configurar esse recurso, visite [o artigo Definir restauração de região cruzada.](backup-create-rs-vault.md#set-cross-region-restore). . Este recurso é suportado para os seguintes tipos de gerenciamento:

| Tipo de gerenciamento de backup | Com suporte                                                    | Regiões com Suporte |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| VM do Azure               | Sim. Visualização limitada pública suportada para VMs e VMs criptografadas com discos menores que 4 TB | Centro-Oeste dos EUA   |
| Agente MARS/Nas instalações | Não                                                           | N/D               |
| SQL /SAP HANA          | Não                                                           | N/D               |
| Afs                    | Não                                                           | N/D               |

## <a name="next-steps"></a>Próximas etapas

- [Examinar matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
