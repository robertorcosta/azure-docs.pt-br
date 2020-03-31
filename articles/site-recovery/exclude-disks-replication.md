---
title: Exclua discos da replicação com a recuperação do site do Azure
description: Como excluir discos da replicação para o Azure com o Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281841"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Excluir discos da recuperação de desastres

Este artigo descreve como excluir discos da replicação durante a recuperação de desastres de locais para o Azure com [o Azure Site Recovery](site-recovery-overview.md). Você pode excluir discos da replicação por uma série de razões:

- Para que os dados sem importância no disco excluído não sejam replicados.
- Para otimizar a largura de banda de replicação consumida ou os recursos do lado do alvo.
- Para salvar recursos de armazenamento e rede, não replicando dados que você não precisa.
- As VMs do Azure atingiram os limites de replicação do Site Recovery.


## <a name="supported-scenarios"></a>Cenários com suporte

Você pode excluir discos da replicação conforme resumido na tabela.

**Azure para o Azure** | **VMware no Azure** | **Hyper-V para Azure** 
--- | --- | ---
Sim (usando powershell) | Sim | Sim 

## <a name="exclude-limitations"></a>Excluir limitações

**Limitação** | **Azure VMs** | **VMs VMware** | **VMs Hyper-V**
--- | --- | ---
**Tipos de disco** | Você pode excluir discos básicos da replicação.<br/><br/> Você não pode excluir discos do sistema operacional ou discos dinâmicos. Os discos temporários são excluídos por padrão. | Você pode excluir discos básicos da replicação.<br/><br/> Você não pode excluir discos do sistema operacional ou discos dinâmicos. | Você pode excluir discos básicos da replicação.<br/><br/> Você não pode excluir os discos do sistema operacional. É recomendável que você não exclua discos dinâmicos. A Recuperação do Site não pode identificar qual VHS é básico ou dinâmico na VM convidada. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco dinâmico protegido se tornará um disco com falha em uma VM failover e os dados desse disco não estão acessíveis.
**Réplica de disco** | Você não pode excluir um disco que está se replicando.<br/><br/> Desativar e reativar a replicação para a VM. |  Você não pode excluir um disco que está se replicando. |  Você não pode excluir um disco que está se replicando.
**Serviço de mobilidade (VMware)** | Não relevante | Você pode excluir discos somente em VMs que tenham o serviço de mobilidade instalado.<br/><br/> Isso significa que você tem que instalar manualmente o serviço de mobilidade nas VMs para as quais você deseja excluir discos. Você não pode usar o mecanismo de instalação push porque ele instala o serviço de mobilidade somente após a replicação ser ativada. | Não é relevante.
**Adicionar/Remover** | Você pode adicionar e remover discos em VMs do Azure com discos gerenciados. | Não é possível adicionar ou remover discos após a replicação ser ativada. Desativar e, em seguida, reativar a replicação para adicionar um disco. | Não é possível adicionar ou remover discos após a replicação ser ativada. Desativar e, em seguida, reativar a replicação.
**Failover** | Se um aplicativo precisar de um disco que você excluiu, após failover você precisa criar o disco manualmente para que o aplicativo replicado possa ser executado.<br/><br/> Alternativamente, você pode criar o disco durante o failover da VM, integrando a automação do Azure em um plano de recuperação. | Se você excluir um disco que um aplicativo precisa, crie-o manualmente no Azure após failover. | Se você excluir um disco que um aplicativo precisa, crie-o manualmente no Azure após failover.
**Discos de failback no local criados manualmente** | Não relevante | **Windows VMs**: Os discos criados manualmente no Azure não são falhados de volta. Por exemplo, se você falhar em três discos e criar dois discos diretamente em uma VM do Azure, apenas os três discos que foram falhados serão então retumam a reprodução.<br/><br/> **VMs Linux**: Os discos criados manualmente no Azure são falhados de volta. Por exemplo, se você falhar em três discos e criar dois discos em uma VM Azure, todos os cinco serão falhados de volta. Você não pode excluir do failback discos que foram criados manualmente. | Os discos criados manualmente no Azure não são falhados de volta. Por exemplo, se você falhar em três discos e criar dois discos diretamente em uma VM do Azure, apenas três discos que foram falhados serão retumados.
**Discos excluídos por failback no local** | Não relevante | Se você falhar de volta à máquina original, a configuração do disco VM failback não inclui os discos excluídos. Discos que foram excluídos da replicação do VMware para o Azure não estão disponíveis na VM failback. | Quando o failback é para a localização original do Hyper-V, a configuração do disco VM failback permanece a mesma do disco VM de origem original. Os discos que foram excluídos do site Hyper-V para a replicação do Azure estão disponíveis na VM failback.



## <a name="typical-scenarios"></a>Cenários típicos

Exemplos de churn de dados que são grandes candidatos à exclusão incluem gravações em um arquivo de paginação (pagefile.sys) e grava no arquivo tempdb do Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, os arquivos de paginação e tempdb podem registrar uma quantidade significativa de churn. Replicar esse tipo de dados para o Azure é intensivo em recursos.

- Para otimizar a replicação de uma VM com um único disco virtual que inclui tanto o sistema operacional quanto o arquivo de paginação, você poderia:
    1. Divida o disco virtual em dois discos virtuais. Um disco virtual tem o sistema operacional e o outro tem o arquivo de paginação.
    2. Exclua o disco do arquivo de paginação da replicação.

- Para otimizar a replicação de um disco que inclua tanto o arquivo tempdb do Microsoft SQL Server quanto o arquivo do banco de dados do sistema, você poderia:
    1. Manter o banco de dados do sistema e tempdb em dois discos diferentes.
    2. Excluir o disco de tempdb da replicação.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: Excluir o disco tempdb do SQL Server

Vamos ver como lidar com a exclusão de disco, failover e failover para uma fonte SQL Server Windows VM - **SalesDB***, para a qual queremos excluir tempdb. 

### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Temos esses discos na fonte Windows VM SalesDB.

**Nome do disco** | **Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operacional.
DB-Disk1| Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuários1.
DB-Disco2 (disco excluído da proteção) | Disk2 | E:\ | Arquivos temporários.
DB-Disco3 (disco excluído da proteção) | Disk3 | F:\ | Banco de dados tempdb SQL.<br/><br/> Caminho da pasta - F:\MSSQL\Data\. Anote o caminho da pasta antes do failover.
DB-Disk4 | Disk4 |G:\ | User Database2

1. Nós habilitamos a replicação para o VM SalesDB.
2. Excluímos o Disk2 e o Disk3 da replicação porque a rotatividade de dados nesses discos é temporária. 


### <a name="handle-disks-during-failover"></a>Manuseie discos durante o failover

Como os discos não são replicados, quando você falha no Azure, esses discos não estão presentes na VM do Azure criada após failover. O Azure VM tem os discos resumidos nesta tabela.

**Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário<br/><br/>O Azure adiciona este disco. Como o Disk2 e o Disk3 foram excluídos da replicação, E: é a primeira letra de unidade da lista disponível. O Azure atribui e volume de armazenamento temporário. Outras letras de unidade para discos replicados permanecem as mesmas.
Disk2 | D:\ | Banco de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Em nosso exemplo, uma vez que o Disk3, o disco tempdb SQL, foi excluído da replicação e não está disponível no VM do Azure, o serviço SQL está em um estado parado e precisa do caminho F:\MSSQL\Data. Você pode criar esse caminho de algumas maneiras: 

- Adicione um novo disco após o failover e atribua o caminho da pasta tempdb.
- Use um disco de armazenamento temporário existente para o caminho da pasta tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Adicionar um novo disco após failover

1. Anote os caminhos do SQL de tempdb.mdf e tempdb.ldf antes do failover.
2. A partir do portal Azure, adicione um novo disco ao Failover Azure VM. O disco deve ter o mesmo tamanho (ou maior) que o disco tempdb SQL de origem (Disk3).
3. Faça login na VM Azure.
4. No console de gerenciamento (diskmgmt.msc) do disco, inicialize e formate o disco recém-adicionado.
5. Atribuir a mesma letra de unidade que foi usada pelo disco tempdb SQL (F:)
6. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
7. Inicie o serviço SQL por meio do console de serviço.

#### <a name="use-an-existing-temporary-storage-disk"></a>Use um disco de armazenamento temporário existente 

1. Abra um prompt de comando.
2. Execute o SQL Server no modo de recuperação do prompt de comando.

        Net start MSSQLSERVER /f / T3608

3. Execute o seguinte sqlcmd para alterar o caminho de tempdb para o novo caminho.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Parar o serviço do Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Inicie o serviço do Microsoft SQL Server.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VMs: Discos durante o failback para a localização original

Agora vamos ver como lidar com discos em VMms VMware quando você falhar de volta ao seu local original no local.

- **Discos criados no Azure**: Como nosso exemplo usa uma VM do Windows, os discos que você cria manualmente no Azure não são replicados de volta ao seu site quando você falha ou reprotege uma VM.
- **Disco de armazenamento temporário no Azure**: O disco de armazenamento temporário não é replicado de volta aos hosts locais.
- **Discos excluídos**: Os discos que foram excluídos da replicação do VMware para o Azure não estão disponíveis na VM local após failback.

Antes de recompor as VMs vMware para o local original, as configurações do disco Azure VM são as seguintes.

**Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuários1.
Disk3 | G:\ | Banco de dados do usuário2.

Após failback, o VMware VM no local original tem os discos resumidos na tabela.

**Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operacional.
Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuários1.
Disk2 | G:\ | Banco de dados do usuário2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hiper-VMs: Discos durante o failback para a localização original

Agora vamos ver como lidar com discos em VMs Hyper-V quando você falhar de volta ao seu local original no local.

- **Discos criados no Azure**: Discos que você cria manualmente no Azure não são replicados de volta ao seu site quando você falha ou reprotege uma VM.
- **Disco de armazenamento temporário no Azure**: O disco de armazenamento temporário não é replicado de volta aos hosts locais.
- **Discos excluídos**: Após o failback, a configuração do disco VM é a mesma da configuração original do disco VM. Os discos que foram excluídos da replicação do Hyper-V para o Azure estão disponíveis na VM failback.

Antes de recompor as VMs Hyper-V para o local original, as configurações do disco Azure VM são as seguintes.

**Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuários1.
Disk3 | G:\ | Banco de dados do usuário2.

Após failover planejado (failback) do Azure para o Hyper-V no local, o VM Hyper-V no local original tem os discos resumidos na tabela.

**Nome do disco** | **Nº do Disco do Sistema Operacional Convidado** | **Letra da unidade** | **Tipo de dados de disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disco0 |   C:\ | Disco do sistema operacional.
DB-Disk1 | Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuários1.
DB-disco 2 (disco excluídos) | Disk2 | E:\ | Arquivos temporários.
DB-Disk3 (disco excluídos) | Disk3 | F:\ | Banco de dados tempdb SQL<br/><br/> Caminho da pasta (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | User Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exemplo 2: Exclua o disco de arquivo de paginação

Vamos ver como lidar com a exclusão de disco, failover e failover para uma VM de origem do Windows, para a qual queremos excluir o disco de arquivo pagefile.sys na unidade D e uma unidade alternativa.


### <a name="paging-file-on-the-d-drive"></a>Arquivo de paginação na unidade D

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operacional
DB-Disk1 (Exclua da replicação) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Nossas configurações de arquivo de paginação na VM de origem são as seguintes:

![Configurações do arquivo de paginação na máquina virtual de origem](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Nós habilitamos a replicação para a VM.
2. Excluímos o DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após failover

Após failover, o Azure VM tem os discos resumidos na tabela.

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário/pagefile.sys <br/><br/> Porque DB-Disk1 (D:) foi excluído, D: é a primeira letra de unidade da lista disponível.<br/><br/> O Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Como D: está disponível, a configuração do arquivo de paginação vm permanece a mesma).
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Nossas configurações de arquivo de paginação na VM do Azure são as seguintes:

![Configurações do arquivo de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Arquivo de paginação em outra unidade (não D:)

Vamos olhar para o exemplo em que o arquivo de paginação não está na unidade D.  

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do Sistema Operacional convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operacional
DB-Disk1 (Exclua da replicação) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Nossas configurações de arquivo de paginação na VM local são as seguintes:

![Configurações do arquivo de paginação na máquina virtual local](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Nós habilitamos a replicação para a VM.
2. Excluímos o DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após failover

Após failover, o Azure VM tem os discos resumidos na tabela.

**Nome do disco** | **Nº do Disco do Sistema Operacional Convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0  |C:\ | Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br/><br/> Como D: é a primeira letra de unidade de disponível na lista, o Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Para todos os discos replicados, a letra da unidade permanece igual.<br/><br/> Como o disco G: não está disponível, o sistema usará o c: unidade para o arquivo de paginação.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Nossas configurações de arquivo de paginação na VM do Azure são as seguintes:

![Configurações do arquivo de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as diretrizes para o disco de armazenamento temporário:
    - [Saiba mais sobre](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) o uso de SSDs em VMs do Azure para armazenar extensões de pool de servidores SQL e buffer
    - [Revise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) as práticas recomendadas de desempenho para SQL Server em VMs Azure.
- Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.

