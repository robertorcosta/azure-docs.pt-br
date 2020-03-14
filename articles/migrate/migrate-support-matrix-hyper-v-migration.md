---
title: Suporte para migração do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para migração do Hyper-V com migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245818"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração do Hyper-V

Este artigo resume as configurações de suporte e as limitações para migrar VMs do Hyper-V com [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar as VMs do Hyper-V para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações de migração

Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, replique em grupos de 10.


## <a name="hyper-v-hosts"></a>Hosts do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. <br/>O software de replicação de migrações para Azure (provedor de replicação do Hyper-V) precisa ser instalado nos hosts Hyper-V.|
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso à URL** | O software do provedor de replicação nos hosts Hyper-V precisará de acesso a essas URLS:<br/><br/> -login.microsoftonline.com: controle de acesso e gerenciamento de identidade usando Active Directory.<br/><br/> -*. backup.windowsazure.com: transferência e coordenação de dados de replicação. Migrar URLs de serviço.<br/><br/> -*. blob.core.windows.net: carregar dados em contas de armazenamento.<br/><br/> -dc.services.visualstudio.com: carregar logs de aplicativo usados para monitoramento interno.<br/><br/> -time.windows.com: verifica a sincronização de horário entre o sistema e o horário global.
| **Acesso à porta** |  Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.

## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Alterações necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. Você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
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
| **Protocolo** | Sem suporte.
| **Agrupamento NIC** | Sem suporte.
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
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall do Windows** -> **aplicativos e recursos permitidos** para redes de **domínio e privadas** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  

## <a name="next-steps"></a>Próximas etapas

[Migre VMs do Hyper-V](tutorial-migrate-hyper-v.md) para migração.
