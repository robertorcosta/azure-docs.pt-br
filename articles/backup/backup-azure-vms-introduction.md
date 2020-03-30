---
title: Sobre o backup de VM do Azure
description: Neste artigo, saiba como o serviço de backup do Azure faz backup das máquinas Virtuais do Azure e como seguir as melhores práticas.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415977"
---
# <a name="an-overview-of-azure-vm-backup"></a>Uma visão geral do backup do Azure VM

Este artigo descreve como o serviço de backup do [Azure](backup-introduction-to-azure-backup.md) faz backup de máquinas virtuais (VMs) do Azure.

O Azure Backup fornece backups independentes e isolados para se proteger contra a destruição não intencional dos dados em suas VMs. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação. A configuração e o dimensionamento são simples, os backups são otimizados e você pode facilmente restaurar conforme necessário.

Como parte do processo de backup, um [instantâneo é retirado](#snapshot-creation), e os dados são transferidos para o cofre dos Serviços de Recuperação sem impacto nas cargas de trabalho de produção. O instantâneo fornece diferentes níveis de consistência, como descrito [aqui](#snapshot-consistency).

O Azure Backup também possui ofertas especializadas para cargas de trabalho de banco de dados como [SQL Server](backup-azure-sql-database.md) e [SAP HANA](sap-hana-db-about.md) que são conscientes da carga de trabalho, oferecem RPO de 15 minutos (objetivo de ponto de recuperação) e permitem backup e restauração de bancos de dados individuais.

## <a name="backup-process"></a>Processo de backup

Veja como o Azure Backup completa um backup para VMs do Azure:

1. Para As VMs do Azure selecionadas para backup, o Azure Backup inicia um trabalho de backup de acordo com o cronograma de backup especificado.
1. Durante o primeiro backup, uma extensão de backup é instalada na VM se a VM estiver em execução.
    - Para VMs do Windows, a [extensão VMSnapshot](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) está instalada.
    - Para VMs Linux, a [extensão VMSnapshotLinux](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) está instalada.
1. Para VMs do Windows que estão sendo executados, o Backup coordena com o Windows Volume Shadow Copy Service (VSS) para tirar um instantâneo consistente do aplicativo da VM.
    - Por padrão, o backup recebe backups VSS completos.
    - Se o Backup não conseguir tirar um instantâneo consistente com o aplicativo, ele levará um instantâneo consistente com o arquivo do armazenamento subjacente (porque nenhuma gravação do aplicativo ocorre enquanto a VM é interrompida).
1. Para VMs Linux, o backup requer um backup consistente com o arquivo. Para instantâneos consistentes com aplicativos, você precisa personalizar manualmente scripts pré/post.
1. Depois que o backup tira o instantâneo, ele transfere os dados para o cofre.
    - O backup é otimizado fazendo backup de cada disco VM em paralelo.
    - Para cada disco que está sendo feito backup, o Azure Backup lê os blocos no disco e identifica e transfere apenas os blocos de dados que mudaram (o delta) desde o backup anterior.
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Esse processo pode levar algumas horas em horários de pico. O tempo total de backup de uma VM será inferior a 24 horas para políticas diárias de backup.
1. As alterações feitas em uma VM do Windows depois que o Backup do Azure estiver ativado nele são:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 está instalado na VM
    - Tipo de inicialização do serviço de cópia de sombra de volume (VSS) alterado para automático de manual
    - O serviço IaaSVmProvider Windows é adicionado

1. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Criptografia de backups do Azure VM

Quando você faz backup de VMs do Azure com o Backup do Azure, máquinas virtuais são criptografadas em repouso com SSE (Criptografia do Serviço de Armazenamento). O Azure Backup também pode fazer backup de VMs do Azure que são criptografadas usando o Azure Disk Encryption.

**Criptografia** | **Detalhes** | **Suporte**
--- | --- | ---
**Criptografia de Disco do Azure** | O Azure Disk Encryption criptografa os discos de tv e dados do Azure.<br/><br/> A Criptografia de Disco Do Azure integra-se às chaves de criptografia BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. A Criptografia de disco do Azure também se integra com as chaves de criptografia do Azure Key Vault (KEKs). | O Azure Backup suporta backup de VMs Azure gerenciados e não gerenciados criptografados apenas com BEKs, ou com BEKs em conjunto com KEKs.<br/><br/> Tanto BEKs quanto KEKs são copiados e criptografados.<br/><br/> Como os KEKs e beks estão em backup, os usuários com as permissões necessárias podem restaurar chaves e segredos de volta ao cofre de chaves, se necessário. Esses usuários também podem recuperar a VM criptografada.<br/><br/> Chaves e segredos criptografados não podem ser lidos por usuários não autorizados ou pelo Azure.
**SSE** | Com o SSE, o Azure Storage fornece criptografia em repouso, criptografando automaticamente os dados antes de armazená-los. O Azure Storage também descriptografa dados antes de recuperá-los. | O Azure Backup usa sse para criptografia em repouso de VMs do Azure.

Para VMs Azure gerenciados e não gerenciados, o Backup suporta ambas as VMs criptografadas apenas com BEKs ou VMs criptografados com BEKs juntamente com KEKs.

Os BEKs (segredos) e KEKs (chaves) de backup são criptografados. Eles só podem ser lidos e usados quando forem restaurados de volta ao cofre de chaves por usuários autorizados. Nem usuários não autorizados, nem O Zure, podem ler ou usar chaves ou segredos de backup.

Os BEKs também estão em backup. Assim, se os BEKs forem perdidos, os usuários autorizados podem restaurar os BEKs no cofre de chaves e recuperar as VMs criptografadas. Somente usuários com o nível necessário de permissões podem fazer backup e restaurar VMs criptografadas ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação de instantâneos

O Azure Backup tira instantâneos de acordo com o cronograma de backup.

- **VMs do Windows:** Para VMs do Windows, o serviço de backup coordena com o VSS para tirar um instantâneo consistente com o aplicativo dos discos VM.  Por padrão, o Azure Backup faz um backup VSS completo (ele trunca os logs do aplicativo, como o SQL Server no momento do backup, para obter backup consistente no nível do aplicativo).  Se você estiver usando um banco de dados SQL Server no backup do Azure VM, então você pode modificar a configuração para fazer um backup do VSS Copy (para preservar logs). Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **VMs Linux:** Para tirar instantâneos consistentes com aplicativos de VMs Linux, use a estrutura de pré-script e pós-script do Linux para escrever seus próprios scripts personalizados para garantir a consistência.

  - O Azure Backup invoca apenas os scripts pré/post escritos por você.
  - Se os pré-scripts e pós-scripts forem executados com sucesso, o Azure Backup marca o ponto de recuperação como consistente com o aplicativo. No entanto, quando você está usando scripts personalizados, você é, em última análise, responsável pela consistência do aplicativo.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar scripts.

## <a name="snapshot-consistency"></a>Consistência do instantâneo

A tabela a seguir explica os diferentes tipos de consistência do instantâneo:

**Instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Consistente com o aplicativo** | Backups consistentes com o aplicativo capturam o conteúdo da memória e operações de E/S pendentes. Instantâneos consistentes com aplicativos usam um escritor VSS (ou scripts pré/post para Linux) para garantir a consistência dos dados do aplicativo antes que um backup ocorra. | Quando você está recuperando uma VM com um instantâneo consistente com o aplicativo, o VM inicializa. Não há perda ou corrupção de dados. O aplicativo é iniciado em um estado consistente. | Windows: Todos os escritores VSS tiveram sucesso<br/><br/> Linux: Scripts pré/post são configurados e bem sucedidos
**Sistema de arquivos consistente** | Backups consistentes do sistema de arquivos fornecem consistência tirando um instantâneo de todos os arquivos ao mesmo tempo.<br/><br/> | Quando você está recuperando uma VM com um instantâneo consistente do sistema de arquivos, o VM inicializa. Não há perda ou corrupção de dados. Os aplicativos precisam implementar seus próprios mecanismos de "correção" para garantir a consistência dos dados restaurados. | Windows: Alguns escritores VSS falharam <br/><br/> Linux: Padrão (se os scripts pré/post não forem configurados ou falharem)
**Controle de falhas** | Instantâneos consistentes com falhas geralmente ocorrem se uma VM do Azure for desligada no momento do backup. Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup. | Começa com o processo de inicialização da VM seguido de uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou operações de gravação que não foram transferidas para o disco antes da falha serem perdidas. Os aplicativos implementam sua própria verificação de dados. Por exemplo, um aplicativo de banco de dados pode usar seu registro de transações para verificação. Se o registro de transações tiver entradas que não estão no banco de dados, o software do banco de dados reverte as transações até que os dados sejam consistentes. | A VM está em estado de desligamento (parado/desalocado).

## <a name="backup-and-restore-considerations"></a>Considerações de backup e restauração

**Consideração** | **Detalhes**
--- | ---
**Disco** | O backup de discos VM é paralelo. Por exemplo, se uma VM tiver quatro discos, o serviço de backup tentará fazer backup dos quatro discos em paralelo. O backup é incremental (somente dados alterados).
**Agendamento** |  Para reduzir o tráfego de backup, faça backup de diferentes VMs em diferentes horas do dia e certifique-se de que os horários não se sobreponham. Fazer backup de VMs ao mesmo tempo provoca congestionamentos de tráfego.
**Preparando backups** | Tenha em mente o tempo necessário para preparar o backup. O tempo de preparação inclui a instalação ou atualização da extensão de backup e o acionamento de um instantâneo de acordo com o cronograma de backup.
**Transferência de dados** | Considere o tempo necessário para o Backup do Azure identificar as alterações incrementais do backup anterior.<br/><br/> Em um backup incremental, o Azure Backup determina as alterações calculando a soma de verificação do bloco. Se um bloco for alterado, está marcado para transferência para o cofre. O serviço analisa os blocos identificados para tentar minimizar ainda mais a quantidade de dados a serem transferidos. Depois de avaliar todos os blocos alterados, o Azure Backup transfere as alterações para o cofre.<br/><br/> Pode haver um retardo entre a captura do instantâneo e a sua cópia para o cofre.<br/><br/> Nos horários de pico, pode levar até oito horas para que os backups sejam processados. O tempo de backup para uma VM será menor que 24 horas para o backup diário.
**Backup inicial** | Embora o tempo total de backup para backups incrementais seja inferior a 24 horas, esse pode não ser o caso para o primeiro backup. O tempo necessário para o backup inicial dependerá do tamanho dos dados e quando o backup for processado.
**Restaurar a fila** | Os processos de backup do Azure restauram trabalhos de várias contas de armazenamento ao mesmo tempo e colocam solicitações de restauração em uma fila.
**Restaurar a cópia** | Durante o processo de restauração, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo total de restauração depende das operações de I/O por segundo (IOPS) e do throughput da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras leituras e gravações de aplicativo.

### <a name="backup-performance"></a>Desempenho do backup

Esses cenários comuns podem afetar o tempo total de backup:

- **Adicionando um novo disco a uma VM Azure protegida:** Se uma VM estiver sendo submetida a backup incremental e um novo disco for adicionado, o tempo de backup aumentará. O tempo total de backup pode durar mais de 24 horas devido à replicação inicial do novo disco, juntamente com a replicação delta dos discos existentes.
- **Discos fragmentados:** As operações de backup são mais rápidas quando as alterações de disco são contíguas. Se as alterações forem distribuídas e fragmentadas em um disco, o backup será mais lento.
- **Churn de disco:** Se os discos protegidos que estão em backup incremental tiverem um churn diário de mais de 200 GB, o backup pode levar muito tempo (mais de oito horas) para ser concluído.
- **Versões de backup:** A versão mais recente do Backup (conhecida como versão de Restauração Instantânea) usa um processo mais otimizado do que a comparação de resumo sustais para identificar alterações. Mas se você estiver usando o Instant Restore e tiver excluído um instantâneo de backup, o backup muda para comparação de soma de verificação. Neste caso, a operação de backup excederá 24 horas (ou falhará).

## <a name="best-practices"></a>Práticas recomendadas

Quando você está configurando backups em VM, sugerimos seguir essas práticas:

- Modifique os tempos de agendamento padrão definidos em uma diretiva. Por exemplo, se o tempo padrão na diretiva for 12:00 AM, aumente o tempo em vários minutos para que os recursos sejam usados de forma ideal.
- Se você está restaurando VMs de um único cofre, recomendamos que você use diferentes [contas de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para garantir que a conta de armazenamento alvo não seja estrangulada. Por exemplo, cada VM deve ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restauradas, use 10 contas de armazenamento diferentes.
- Para backup de VMs que estão usando armazenamento premium, com Restauração Instantânea, recomendamos alocar *50%* de espaço livre do espaço total alocado de armazenamento, que é necessário **apenas** para o primeiro backup. O espaço livre de 50% não é um requisito para backups depois que o primeiro backup estiver concluído
- O limite no número de discos por conta de armazenamento é relativo ao quão fortemente os discos estão sendo acessados por aplicativos que estão sendo executados em uma VM de infra-estrutura como serviço (IaaS). Como uma prática geral, se 5 a 10 discos ou mais estiverem presentes em uma única conta de armazenamento, equilibre a carga movendo alguns discos para contas de armazenamento separadas.

## <a name="backup-costs"></a>Custos de backup

VMs do Azure cujo backup foi feito com o Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

O faturamento não começa até que o primeiro backup bem sucedido termine. Neste ponto, a cobrança de VMs protegidas e de armazenamento é iniciada. A cobrança continua enquanto quaisquer dados de backup da VM forem armazenados em um cofre. Se você interromper a proteção para uma VM, mas os dados de backup para a VM existirem em um cofre, a cobrança continuará.

A cobrança para uma VM especificada parará somente se a proteção for interrompida e os dados de backup forem excluídos. Quando a proteção for interrompida e não houver nenhum trabalho de backup ativo, o tamanho do último backup bem-sucedido de VM se tornará o tamanho da instância protegida usada para a fatura mensal.

O cálculo de tamanho de instância protegida é baseado no tamanho *real* da VM. O tamanho da VM é a soma de todos os dados da VM, excluindo o armazenamento temporário. Os preços são baseados nos dados reais armazenados nos discos de dados, não no tamanho máximo suportado para cada disco de dados que está conectado à VM.

Da mesma forma, a conta de armazenamento de backup é baseada na quantidade de dados armazenados no Azure Backup, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, pegue uma VM de tamanho A2 que tenha dois discos de dados adicionais com um tamanho máximo de 32 TB cada. A tabela a seguir mostra os dados reais armazenados em cada um desses discos:

**Disco** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do sistema operacional | 32 TB | 17 GB
Disco local/temporário | 135 GB | 5 GB (não incluído no backup)
Disco de dados 1 | 32 TB| 30 GB
Disco de dados 2 | 32 TB | 0 GB

O tamanho real da VM neste caso é de 17 GB + 30 GB + 0 GB = 47 GB. Esse tamanho de instância protegida (47 GB) torna-se a base para a conta mensal. À medida que a quantidade de dados na VM cresce, o tamanho de instância protegida usado para alterações de cobrança para corresponder.

## <a name="next-steps"></a>Próximas etapas

Agora, [prepare-se para o backup do Azure VM](backup-azure-arm-vms-prepare.md).
