---
title: Suporte para migração de Hiper-V no Azure Migrate
description: Saiba mais sobre o suporte para migração de Hiper-V com o Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538181"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração de Hiper-V

Este artigo resume as configurações de suporte e as limitações para migrar Hiper-VVs com [o Azure Migrate: Migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você está procurando informações sobre como avaliar Hiper-V VMs para migração para o Azure, revise a [matriz de suporte de avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações da migração

Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, replique-se em grupos de 10.


## <a name="hyper-v-hosts"></a>Hosts do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. <br/>O software de replicação Azure Migrate (provedor de replicação Hyper-V) está instalado nos hosts Hyper-V.|
| **Permissões**           | Você precisa de permissões de administrador no host Hyper-V. |
| **Sistema operacional host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso portuário** |  Conexões de saída na porta HTTPS 443 para enviar dados de replicação de VM.

### <a name="url-access-public-cloud"></a>Acesso à URL (nuvem pública)

O software do provedor de replicação nos hosts Hyper-V precisará ter acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.com | Controle de acesso e gerenciamento de identidade usando o Active Directory.
backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Usado para migração.
*.blob.core.windows.net | Faça upload de dados para contas de armazenamento. 
dc.services.visualstudio.com | Faça upload de registros de aplicativos usados para monitoramento interno.
time.windows.com | Verifica a sincronização de tempo entre o sistema e o tempo global.

### <a name="url-access-azure-government"></a>Acesso à URL (Governo Azure)

O software do provedor de replicação nos hosts Hyper-V precisará ter acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.us | Controle de acesso e gerenciamento de identidade usando o Active Directory.
backup.windowsazure.us | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.us | Usado para migração.
*.blob.core.usgovcloudapi.net | Faça upload de dados para contas de armazenamento.
dc.services.visualstudio.com | Faça upload de registros de aplicativos usados para monitoramento interno.
time.nist.gov | Verifica a sincronização de tempo entre o sistema e o tempo global.


## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure. |
| **Mudanças necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. Faça ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se /boot estiver em uma partição dedicada, ele deve residir no disco do SISTEMA OPERACIONAL e não ser espalhado por vários discos.<br/> Se /boot faz parte da partição raiz (/), então a partição '/' deve estar no disco do SISTEMA OPERACIONAL e não abranger outros discos. |
| **Inicialização UEFI**                  | A VM migrada no Azure será automaticamente convertida em uma VM de inicialização do BIOS. O VM deve estar executando o Windows Server 2012 e mais tarde apenas. O disco do SO deve ter até cinco partições ou menos e o tamanho do disco do SO deve ser inferior a 300 GB.
  |
| **Tamanho do disco**                  | 2 TB para o disco do sistema operacional, 4 TB para discos de dados.
| **Número do disco** | Um máximo de 16 discos por VM.
| **Discos/volumes criptografados**    | Não suportado para migração. |
| **Discos RDM/passagem**      | Não suportado para migração. |
| **Disco compartilhado** | As VMs que usam discos compartilhados não são suportadas para migração.
| **NFS**                        | Os volumes NFS montados como volumes nas VMs não serão replicados. |
| **Iscsi**                      | As VMs com metas iSCSI não são suportadas para migração.
| **Disco de destino**                | Você pode migrar para As VMs do Azure apenas com discos gerenciados. |
| **IPv6** | Sem suporte.
| **Equipe NIC** | Sem suporte.
| **Azure Site Recovery** | Você não pode replicar usando a migração do servidor Azure Migrate se a VM estiver habilitada para replicação com o Azure Site Recovery.
| **Portas** | Conexões de saída na porta HTTPS 443 para enviar dados de replicação de VM.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs no local replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 16 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conecte-se após a migração - Windows | Para conectar-se às VMs do Azure executando o Windows após a migração:<br/> - Antes que a migração habilite o RDP na VM no local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso vpn de site para site, habilite rdp e permita rdP no **Windows Firewall** -> **Permitido aplicativos e recursos** para redes de domínio e **privado.** Além disso, verifique se a política de SAN do sistema operacional está definida **como OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após a migração-Linux | Para conectar-se às VMs do Azure após a migração usando o SSH:<br/> Antes da migração, na máquina local, verifique se o serviço Secure Shell está definido como Start e que as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões recebidas na porta SSH para as regras do grupo de segurança de rede sobre a falha sobre a VM e para a sub-rede Azure à qual está conectada. Além disso, adicione um endereço IP público para a VM. |  

## <a name="next-steps"></a>Próximas etapas

[Migrar Hiper-VVs](tutorial-migrate-hyper-v.md) para migração.
