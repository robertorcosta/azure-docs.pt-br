---
title: Matriz de suporte para o agente MARS
description: Este artigo resume o suporte ao Azure Backup quando você faz backup de máquinas que estão executando o agente MARS (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247859"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente MARS (Serviços de Recuperação do Microsoft Azure)

Você pode usar o serviço de backup do [Azure](backup-overview.md) para fazer backup de máquinas e aplicativos no local e fazer backup de máquinas virtuais (VMs) do Azure. Este artigo resume as configurações e limitações de suporte quando você usa o agente Microsoft Azure Recovery Services (MARS) para fazer backup de máquinas.

## <a name="the-mars-agent"></a>O agente da MARS

O Azure Backup usa o agente MARS para fazer backup de dados de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. O agente da MARS pode:

- Execute em máquinas Windows no local para que eles possam fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
- Execute em VMs windows para que eles possam fazer backup diretamente para um cofre.
- Execute no Microsoft Azure Backup Server (MABS) ou em um servidor DPM (System Center Data Protection Manager, gerente de proteção de dados do centro do sistema). Neste cenário, máquinas e cargas de trabalho voltam para o MABS ou para o servidor DPM. O agente da MARS então apoia este servidor para um cofre no Azure.

> [!NOTE]
>O Azure Backup não suporta ajuste automático do relógio para o horário de verão (DST). Modifique a política para garantir que o horário de verão seja levado em conta para evitar discrepâncias entre o horário real e o horário de backup programado.

Suas opções de backup dependem de onde o agente está instalado. Para obter mais informações, consulte [a arquitetura Azure Backup usando o agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup MABS e DPM, consulte [Fazer backup do DPM ou do MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Veja também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Baixe o mais recente agente MARS | Você pode baixar do cofre a versão mais recente do agente ou [baixá-lo diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente em um computador | Você pode instalar o agente MARS diretamente em um servidor Windows no local ou em uma VM do Windows que está executando qualquer um dos [sistemas operacionais suportados](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar em um servidor de backup | Ao configurar o DPM ou MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. Você pode instalar o agente em [sistemas operacionais suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão, as VMs do Azure habilitadas para backup têm uma instalação de extensão do Azure Backup. Essa extensão faz backup de toda a VM. Você poderá instalar e executar o agente MARS em uma VM do Azure junto com a extensão se quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente MARS em uma VM Azure, ele faz backup de arquivos ou pastas que estão em armazenamento temporário na VM. Os backups falham se os arquivos ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.

## <a name="cache-folder-support"></a>Suporte a pasta de cache

Quando você usa o agente MARS para fazer backup de dados, o agente tira um instantâneo dos dados e armazena-os em uma pasta de cache local antes de enviar os dados para o Azure. A pasta cache (scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  O espaço livre na pasta de cache deve ser de pelo menos 5 a 10% do tamanho total dos seus dados de backup.
Location | A pasta de cache deve ser armazenada localmente na máquina que está sendo backup, e deve estar on-line. A pasta de cache não deve estar em um compartilhamento de rede, em mídia removível ou em um volume offline.
Pasta | A pasta de cache não deve ser criptografada em um volume duplicado ou em uma pasta compactada, que seja esparsa ou que tenha um ponto de reparsa.
Alterações de localização | Você pode alterar o local do`net stop bengine`cache parando o mecanismo de backup ( ) e copiando a pasta de cache para uma nova unidade. (Certifique-se de que a nova unidade tenha espaço suficiente.) Em seguida, atualize duas entradas de registro em **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** e **Config/CloudBackupProvider/ScratchLocation)** para o novo local e reinicie o mecanismo.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-and-ip-access"></a>Acesso a URL e IP

O agente MARS precisa de acesso a estas URLs:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

E para esses endereços IP:

- 20.190.128.0/18
- 40.126.0.0/18

O acesso a todos os URLs e endereços IP listados acima usa o protocolo HTTPS na porta 443.

### <a name="azure-expressroute-support"></a>Suporte ao Azure ExpressRoute

Você pode fazer backup de seus dados no Azure ExpressRoute com peering público (disponível para circuitos antigos) e peering da Microsoft. O backup sobre peering privado não é suportado.

Com peering público: Garanta o acesso aos seguintes domínios/endereços:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Com o peering da Microsoft, selecione os seguintes serviços/regiões e os valores relevantes da comunidade:

- Diretório Ativo azure (12076:5060)
- Região do Microsoft Azure (de acordo com a localização do cofre do Serviço de Recuperação)
- Armazenamento azure (de acordo com a localização do cofre dos Serviços de Recuperação)

Para obter mais informações, consulte os [requisitos de roteamento expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>O Public Peering é preterido por novos circuitos.

### <a name="throttling-support"></a>Suporte de limitação

**Recurso** | **Detalhes**
--- | ---
Controle de Largura de Banda |  Com suporte. No agente MARS, use **'Alterar propriedades'** para ajustar a largura de banda.
Limitação de rede | Não está disponível para máquinas de backup que executam o Windows Server 2008 R2, o Windows Server 2008 SP2 ou o Windows 7.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

>[!NOTE]
> O agente MARS não suporta SKUs do Windows Server Core.

Você pode usar o agente MARS para fazer backup diretamente para o Azure nos sistemas operacionais listados abaixo que são executados em:

1. Servidores Windows no local
2. VMs do Azure que executam o Windows

Os sistemas operacionais devem ter 64 bits e devem estar executando os pacotes de serviços mais recentes e atualizações. A tabela a seguir resume estes sistemas operacionais:

**Sistema Operacional** | **Arquivos/pastas** | **Estado do sistema** | **Requisitos de software/módulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não |  Verifique a versão correspondente do servidor para os requisitos de software/módulo
Windows 8.1 (Enterprise, Pro)| Sim |Não | Verifique a versão correspondente do servidor para os requisitos de software/módulo
Windows 8 (Enterprise, Pro) | Sim | Não | Verifique a versão correspondente do servidor para os requisitos de software/módulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim |- .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0 <br> - Implantação de Manutenção e Gerenciamento de Imagens (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Padrão, Grupo de Trabalho) | Sim | Não | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0

Para obter mais informações, consulte [os sistemas operacionais MABS e DPM suportados.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Sistemas Operacionais no final do suporte

Os seguintes sistemas operacionais estão no final do suporte e é fortemente recomendável atualizar o sistema operacional para continuar protegido.

Se os compromissos existentes impedirem a atualização do sistema operacional, considere migrar os servidores do Windows para as VMs do Azure e aproveite os backups do Azure VM para continuar protegido. Visite a [página de migração aqui](https://azure.microsoft.com/migration/windows-server/) para obter mais informações sobre a migração do seu servidor Windows.

Para ambientes locais ou hospedados, onde você não pode atualizar o sistema operacional ou migrar para o Azure, ative as Atualizações de Segurança Estendidas para que as máquinas continuem protegidas e suportadas. Observe que apenas edições específicas são elegíveis para atualizações de segurança estendidas. Visite a [página de perguntas frequentes](https://www.microsoft.com/cloud-platform/extended-security-updates) para saber mais.

| **Sistema Operacional**                                       | **Arquivos/pastas** | **Estado do sistema** | **Requisitos de software/módulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Sim               | Não                 | Verifique a versão correspondente do servidor para os requisitos de software/módulo |
| Windows Server 2008 R2 (Padrão, Empresa, Datacenter, Fundação) | Sim               | Sim                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatível Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Implantação de Manutenção e Gerenciamento de Imagens (DISM.exe) |
| Windows Server 2008 SP2 (Padrão, Datacenter, Fundação)  | Sim               | Não                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatível Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Implantação de Manutenção e Gerenciamento de Imagens (DISM.exe) <br>  - Base do Servidor Virtual 2005 + KB KB948515 |

## <a name="backup-limits"></a>Limites do Backup

### <a name="size-limits"></a>Limites de tamanho

O backup do Azure limita o tamanho de uma fonte de dados de arquivo ou pasta que pode ser backup. Os itens que você faz backup de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema Operacional** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |54.400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 ou superior| 54.400 GB
Windows 7| 1.700 GB

### <a name="other-limitations"></a>Outras limitações

- A MARS não suporta proteção de várias máquinas com o mesmo nome para um único cofre.

## <a name="supported-file-types-for-backup"></a>Tipos de arquivo compatíveis para backup

**Tipo** | **Suporte**
--- | ---
Criptografado<sup>*</sup>|  Com suporte.
Compressed |  Com suporte.
Esparsos |  Com suporte.
Compactados e esparsos | Com suporte.
Links físicos| Sem suporte. Ignorado.
Ponto de nova análise| Sem suporte. Ignorado.
Criptografados e esparsos |Sem suporte. Ignorado.
Fluxo compactado| Sem suporte. Ignorado.
Fluxo esparso| Sem suporte. Ignorado.
OneDrive (arquivos sincronizados são fluxos esparsos)| Sem suporte.
Pastas com replicação DFS habilitadas | Sem suporte.

\*Certifique-se de que o agente MARS tenha acesso aos certificados necessários para acessar os arquivos criptografados. Arquivos inacessíveis serão ignorados.

## <a name="supported-drives-or-volumes-for-backup"></a>Drives ou volumes suportados para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura| Sem suporte | O VSS (Volume Copy Shadow Service, serviço de sombra de cópia de volume) funciona somente se o volume estiver gravável.
Volumes offline| Sem suporte |O VSS só funciona se o volume estiver on-line.
Compartilhamento de rede| Sem suporte |O volume deve ser local no servidor.
Volumes bloqueados pelo BitLocker| Sem suporte |O volume deve ser desbloqueado antes do backup começar.
Identificação do sistema de arquivos| Sem suporte |Apenas o NTFS é suportado.
Mídia removível| Sem suporte |Todas as fontes de itens de backup devem ter um status *fixo.*
Unidades com eliminação de duplicação | Com suporte | O Backup do Azure converte dados com eliminação de duplicação em dados normais. Otimiza, criptografa, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para o backup offline inicial

O Azure Backup suporta *semeamento off-line* para transferir dados iniciais de backup para o Azure usando discos. Este suporte é útil se o seu backup inicial provavelmente estiver na faixa de tamanho de tabytes (TBs). O backup offline é compatível com:

- Backup direto de arquivos e pastas em máquinas locais que estão executando o agente MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.

O backup off-line não pode ser usado para arquivos de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para restauração de dados

Usando o recurso [de restauração instantânea](backup-instant-restore-capability.md) do Azure Backup, você pode restaurar os dados antes que sejam copiados para o cofre. A máquina que você está fazendo backup deve estar executando o .NET Framework 4.5.2 ou superior.

Os backups não podem ser restaurados para uma máquina de destino que está executando uma versão anterior do sistema operacional. Por exemplo, um backup retirado de um computador que está executando o Windows 7 pode ser restaurado no Windows 8 ou posterior. Mas um backup tirado de um computador que está executando o Windows 8 não pode ser restaurado em um computador que está executando o Windows 7.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a arquitetura de backup que usa o agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Saiba o que é suportado quando você [executa o agente MARS no MABS ou em um servidor DPM](backup-support-matrix-mabs-dpm.md).
