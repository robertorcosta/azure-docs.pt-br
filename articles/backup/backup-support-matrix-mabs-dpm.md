---
title: MABS & Matriz de suporte do Centro de Sistema DPM
description: Este artigo resume o suporte ao Backup do Azure quando você usa o Microsoft Azure Backup Server (MABS) ou o DPM do System Center para fazer backup nos locais e nos recursos do Azure VM.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582646"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o Microsoft Azure Backup Server ou O DPM do Centro de Sistema

Você pode usar o serviço de backup do [Azure](backup-overview.md) para fazer backup de máquinas e cargas de trabalho no local e máquinas virtuais do Azure (VMs). Este artigo resume as configurações de suporte e as limitações para fazer backup de máquinas usando o Microsoft Azure Backup Server (MABS) ou o System Center Data Protection Manager (DPM) e o Azure Backup.

## <a name="about-dpmmabs"></a>Sobre o DPM/MABS

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução corporativa que configura, facilita e gerencia o backup e a recuperação de máquinas e dados corporativos. Ele faz parte do conjunto [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) de produtos.

O MABS é um produto de servidor que pode ser usado para fazer backup de servidores físicos, VMs e aplicativos em execução no local.

O MABS é baseado no DPM do System Center e fornece funcionalidade semelhante com algumas diferenças:

- Não é necessária a licença do System Center para executar o MABS.
- Tanto para O MABS quanto para o DPM, o Azure fornece armazenamento de backup a longo prazo. Além disso, o DPM permite fazer backup de dados para armazenamento de longo prazo em fita. O MABS não possui essa funcionalidade.
- Você pode fazer backup de um servidor DPM principal com um servidor DPM secundário. O servidor secundário protege o banco de dados do servidor primário e as réplicas de fonte de dados armazenadas no servidor primário. Se o servidor primário falhar, o servidor secundário pode continuar a proteger as cargas de trabalho que são protegidas pelo servidor primário, até que o servidor primário esteja disponível novamente.  O MABS não possui essa funcionalidade.

Você baixa o MABS do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Pode ser executado no local ou em uma VM Azure.

O DPM e o MABS são compatíveis com backup de uma ampla variedade de aplicativos e sistemas operacionais de clientes e servidores. Eles fornecem vários cenários de backup:

- É possível fazer backup no nível do computador com o backup de estado do sistema ou bare-metal.
- É possível fazer backup de arquivos, compartilhamentos, pastas e volumes específicos.
- Você pode fazer backup de aplicativos específicos usando configurações otimizadas com reconhecimento de aplicativos.

## <a name="dpmmabs-backup"></a>Backup DPM/MABS

O backup usando o DPM/MABS e o Azure Backup funciona da seguinte forma:

1. O agente de proteção DPM/MABS está instalado em cada máquina que será backup.
1. Máquinas e aplicativos são copiados para armazenamento local no DPM/MABS.
1. O Agente do MARS (Serviços de Recuperação do Microsoft Azure) é instalado no servidor MARS/DPM.
1. O agente MARS faz backup dos discos DPM/MABS para um cofre de serviços de recuperação de backup no Azure usando o Backup do Azure.

Para mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise o que é suportado](backup-support-matrix-mars-agent.md) para o agente da MARS.

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Agente** | **Local**
--- | --- | ---
**Fazer backup das cargas de trabalho/computadores locais** | O agente de proteção DPM/MABS é executado nas máquinas que você deseja fazer backup.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente dos Serviços de Recuperação do Microsoft Azure ou do agente de Backup do Azure necessária para habilitar esse recurso é 2.0.8719.0.  | O DPM/MABS deve estar funcionando no local.

## <a name="supported-deployments"></a>Implantações com suporte

O DPM/MABS pode ser implantado conforme resumido na tabela a seguir.

**Implantação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM do VMware | Se o DPM/MABS for instalado como uma VMware VM, ele só faz backup das VMMs e cargas de trabalho vmware que estão sendo executados nessas VMs.
**Implantado como uma VM do Azure Stack** | Somente no MABS | O DPM não pode ser usado para fazer backup das VMs do Azure Stack.
**Implantado como uma VM do Azure** | Protege as VMs e cargas de trabalho do Azure que estão sendo executados nessas VMs | O DPM/MABS em execução no Azure não pode fazer backup de máquinas no local.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais compatíveis com MABS e DPM

O Azure Backup pode fazer backup das instâncias DPM/MABS que estão executando qualquer um dos seguintes sistemas operacionais. Os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.

**Cenário** | **DPM/MABS**
--- | ---
**MABS na VM do Azure** |  Datacenter do Windows 2016.<br/><br/> Datacenter windows 2019.<br/><br/> Recomendamos que você comece com uma imagem do mercado.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e 8 GB de RAM.
**DPM em uma VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema de operacional Windows conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que você comece com uma imagem do mercado.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e 8 GB de RAM.
**O MABS no local** |  MABS v3 e posterior: Windows Server 2016 ou Windows Server 2019
**O DPM no local** | Servidor físico/Hiper-V VM: Centro do Sistema 2012 SP1 ou posterior.<br/><br/> VMware VM: System Center 2012 R2 com atualização 5 ou posterior.

>[!NOTE]
>A instalação do Azure Backup Server não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

## <a name="management-support"></a>Suporte de gerenciamento

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o DPM/MABS em uma máquina de uso único.<br/><br/> Não instale o DPM/MABS em um controlador de domínio, em uma máquina com a instalação da função Servidor de Aplicativos, em uma máquina que esteja executando o Microsoft Exchange Server ou system Center Operations Manager ou em um nó de cluster.<br/><br/> [Revise todos os requisitos do sistema DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | O DPM/MABS deve ser unido a um domínio. Instale primeiro e, em seguida, associe o MABS/DPM em um domínio. Não é possível mover o MABS/DPM para um novo domínio após a implantação.
**Armazenamento** | O MBS (Modern Backup Storage, armazenamento de backup moderno) é suportado a partir do DPM 2016/MABS v2 e posterior. Ele não está disponível para o MABS v1.
**Atualização do MABS** | Você pode instalar diretamente o MABS v3 ou atualizar o MABS v2 para o MABS v3. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Se você estiver usando o MBS, é possível mover o MABS para um novo servidor mantendo o armazenamento.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Você precisará de um backup do banco de dados MABS pois precisará restaurá-lo.

## <a name="mabs-support-on-azure-stack"></a>Compatibilidade do MABS no Azure Stack

Você pode implantar o MABS em uma VM do Azure Stack para poder gerenciar o backup de VMs do Azure Stack e as cargas de trabalho em um único local.

**Componente** | **Detalhes**
--- | ---
**MABS na VM do Azure Stack** | Pelo menos tamanho A2. Recomendamos que você comece com uma imagem do Windows Server 2012 R2 ou Do Windows Server 2016 do Azure Marketplace.<br/><br/> Não instale mais nada no MABS VM.
**Armazenamento do MABS** | Use uma conta de armazenamento separada para o MABS VM. O agente MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter os dados restaurados da nuvem.
**Pool de armazenamento do MABS** | O tamanho do pool de armazenamento MABS é determinado pelo número e tamanho dos discos que estão conectados ao MABS VM. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo, A2 é para quatro discos.
**Retenção do MABS** | Não retenha dados de backup nos discos MABS locais por mais de cinco dias.
**Escalar verticalmente o MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM do MABS. Por exemplo, você pode mudar de série A para Série D.<br/><br/> Você também pode garantir que está descarregando dados com backup no Azure regularmente. Se necessário, você pode implantar servidores MABS adicionais.
**.NET Framework no MABS** | O MABS VM precisa do Framework .NET 3.3 SP1 ou posteriorinstalado nele.
**Domínio do MABS** | A VM do MABS deve ser associada a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM do Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Compatibilidade de backup** | Esses sistemas operacionais são suportados para VMs que você deseja fazer backup:<br/><br/> Canal Semianual do Windows Server (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte ao SQL Server para VMs Azure Stack** | Fazer backup do SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Recue e recupere um banco de dados.
**Compatibilidade do SharePoint para VMs do Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Fazer backup e recuperar uma fazenda, banco de dados, front-end e servidor web.
**Requisitos de rede para backups de VMs** | Todas as VMs na carga de trabalho do Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma assinatura.

## <a name="dpmmabs-networking-support"></a>Suporte às redes MABS/DPM

### <a name="url-access"></a>acesso à URL

O servidor MABS/DPM precisa de acesso a estas URLs:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Suporte ao Azure ExpressRoute

Você pode fazer backup de seus dados no Azure ExpressRoute com peering público (disponível para circuitos antigos) e peering da Microsoft. O backup sobre peering privado não é suportado.

Com peering público: Garanta o acesso aos seguintes domínios/endereços:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Com o peering da Microsoft, selecione os seguintes serviços/regiões e os valores relevantes da comunidade:

* Diretório Ativo azure (12076:5060)
* Região do Microsoft Azure (de acordo com a localização do cofre do Serviço de Recuperação)
* Armazenamento azure (de acordo com a localização do cofre dos Serviços de Recuperação)

Para obter mais detalhes, consulte os [requisitos de roteamento expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>O Public Peering é preterido por novos circuitos.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do MABS/DPM no Backup do Azure

É necessária conectividade com o serviço de Backup do Azure para os backups funcionarem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS no Azure** | **Assinatura** | **Backup/restauração**
--- | --- | ---
Conectado | Ativo | Fazer backup do disco DPM/MABS.<br/><br/> Volte para Azure.<br/><br/> Restaurar a partir do disco.<br/><br/> Restaure-se do Azure.
Conectado | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura estiver expirada, você pode restaurar a partir do disco ou do Azure.<br/><br/> Se a assinatura for desativada, você não poderá restaurar a partir do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativo | Nenhum backup em disco ou no Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura estiver expirada, você pode restaurar a partir do disco ou do Azure.<br/><br/> Se a assinatura for desativada, você não poderá restaurar a partir do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.

## <a name="dpmmabs-storage-support"></a>Compatível com armazenamento do MABS/DPM

Os dados que são copiados para DPM/MABS são armazenados no armazenamento de disco local.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O MBS (Modern Backup Storage, armazenamento de backup moderno) é suportado a partir do DPM 2016/MABS v2 e posterior. Ele não está disponível para o MABS v1.
**Armazenamento MABS na VM do Azure** | Os dados são armazenados em discos Azure que são anexados ao DPM/MABS VM e que são gerenciados em DPM/MABS. O número de discos que podem ser usados para o pool de armazenamento DPM/MABS é limitado pelo tamanho da VM.<br/><br/> A2 VM: 4 discos; A3 VM: 8 discos; A4 VM: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool total de armazenamento de backup disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS em VM do Azure** | Recomendamos que você retenha dados por um dia no disco DPM/MABS Azure e faça backup do DPM/MABS para o cofre para maior retenção. Assim, você pode proteger uma quantidade maior de dados descarregando-os no Backup do Azure.

### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)

A partir do DPM 2016/MABS v2 (em execução no Windows Server 2016) e posterior, você pode tirar proveito do moderno armazenamento de backup (MBS).

- Os backups do MBS são armazenados no disco ReFS (Sistema de Arquivos Resiliente).
- O MBS usa a clonagem de blocos ReFS para um backup mais rápido e um uso mais eficiente do espaço de armazenamento.
- Quando você adiciona volumes ao pool de armazenamento DPM/MABS local, configure-os com letras de unidade. Em seguida, é possível configurar o armazenamento de carga de trabalho em diferentes volumes.
- Ao criar grupos de proteção para fazer backup dos dados do MABS/DPM, é possível selecionar a unidade que você deseja usar. Por exemplo, você pode armazenar backups para SQL ou outras cargas de trabalho iops altas em uma unidade de alto desempenho e armazenar cargas de trabalho que são apoiadas com menos freqüência em uma unidade de desempenho mais baixa.

## <a name="supported-backups-to-mabs"></a>Backups compatíveis com o MABS

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com o Azure Backup Server, consulte a [Matriz de Proteção do Servidor de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backups compatíveis com o DPM

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com o Data Protection Manager, consulte o artigo O que o [DPM pode fazer backup?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)

- Cargas de trabalho agrupadas apoiadas pelo DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou em um domínio filho/confiável.
- Você pode usar a autenticação NTLM/certificado para fazer backup de dados em grupos de trabalho ou domínios não confiáveis.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configure o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
