---
title: Suporte para migração do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para migração do Hyper-V com migrações para Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538181"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração do Hyper-V

Este artigo resume as configurações de suporte e as limitações para migrar VMs do Hyper-V com [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar as VMs do Hyper-V para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações da migração

Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, replique em grupos de 10.


## <a name="hyper-v-hosts"></a>Hosts do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. <br/>O software de replicação de migrações para Azure (provedor de replicação do Hyper-V) está instalado nos hosts do Hyper-V.|
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso à porta** |  Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.

### <a name="url-access-public-cloud"></a>Acesso à URL (nuvem pública)

O software do provedor de replicação nos hosts Hyper-V precisará de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.com | Gerenciamento de acesso e controle de identidade usando o Active Directory.
backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Usado para migração.
*.blob.core.windows.net | Carregar dados para contas de armazenamento. 
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
time.windows.com | Verifica a sincronização de hora entre o sistema e o horário global.

### <a name="url-access-azure-government"></a>Acesso à URL (Azure governamental)

O software do provedor de replicação nos hosts Hyper-V precisará de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.us | Gerenciamento de acesso e controle de identidade usando o Active Directory.
backup.windowsazure.us | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.us | Usado para migração.
*.blob.core.usgovcloudapi.net | Carregar dados para contas de armazenamento.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
time.nist.gov | Verifica a sincronização de hora entre o sistema e o horário global.


## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Alterações necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. Faça os ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Inicialização UEFI**                  | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS. A VM deve estar executando apenas o Windows Server 2012 e posterior. O disco do sistema operacional deve ter até cinco partições ou menos e o tamanho do disco do sistema operacional deve ser inferior a 300 GB.
  |
| **Tamanho do disco**                  | 2 TB para o disco do sistema operacional, 4 TB para discos de dados.
| **Número do disco** | Um máximo de 16 discos por VM.
| **Discos/volumes criptografados**    | Sem suporte para migração. |
| **Discos de RDM/PassThrough**      | Sem suporte para migração. |
| **Disco compartilhado** | As VMs que usam discos compartilhados não têm suporte para migração.
| **NFS**                        | Volumes NFS montados como volumes nas VMs não serão replicados. |
| **ISCSI**                      | As VMs com destinos iSCSI não têm suporte para migração.
| **Disco de destino**                | Você pode migrar para VMs do Azure somente com o Managed disks. |
| **Protocolo** | Não há suporte.
| **Agrupamento NIC** | Não há suporte.
| **Azure Site Recovery** | Não é possível replicar usando a migração de servidor de migrações para Azure se a VM estiver habilitada para replicação com Azure Site Recovery.
| **Portas** | Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 16 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de Rede | Há suporte para vários adaptadores. |
VHD compartilhado | Não há suporte. | A verificação falha se não tiver suporte.
Disco FC | Não há suporte. | A verificação falha se não tiver suporte.
BitLocker | Não há suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall** -> do Windows tenha**aplicativos e recursos permitidos** para redes privadas e de **domínio** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  

## <a name="next-steps"></a>Próximas etapas

[Migre VMs do Hyper-V](tutorial-migrate-hyper-v.md) para migração.
