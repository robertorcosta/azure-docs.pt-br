---
title: Preparar computadores para migração com as Migrações para Azure
description: Saiba como preparar computadores locais para migração com as Migrações para Azure.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927483"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparar computadores locais para migração para Azure

Este artigo descreve como preparar computadores locais antes de começar a migrá-los para o Azure com as [Migrações para Azure: Migração de Servidor](migrate-services-overview.md#azure-migrate-server-migration-tool).

Neste artigo você:
> [!div class="checklist"]
> * Verificará limitações de migração.
> * Verificará os requisitos do sistema operacional e as limitações de suporte.
> * Examinará o acesso a URL e à porta dos computadores que deseja migrar.
> * Examinará alterações que você pode precisar fazer antes de começar a migração.
> * Definirá as configurações, de modo que as letras da unidade sejam preservadas após a migração.
> * Vai preparar os computadores, de modo que você possa se conectar às VMs do Azure após a migração.

## <a name="verify-migration-limitations"></a>Verificará limitações de migração

- Você pode acessar até 35 mil VMs do VMware/VMs do Hyper-V em um só projeto das Migrações para Azure usando a Migração de Servidor das Migrações para Azure. Um projeto pode combinar VMs do VMware e VMs do Hyper-V, até os limites de cada um.
- Você pode selecionar até dez VMs ao mesmo tempo para migração. Se precisar replicar mais, replique em grupos de 10.
- Para migração sem agente de VMware, você pode executar até 100 replicações simultaneamente.

## <a name="verify-operating-system-requirements"></a>Verificar requisitos do sistema operacional

- Verifique se seus [sistemas operacionais Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são compatíveis com o Azure.
- Verifique se suas [distribuições Linux](../virtual-machines/linux/endorsed-distros.md) são compatíveis com o Azure.

## <a name="see-whats-supported"></a>Veja o que é compatível

Para VMs do VMware, a Migração de Servidor dá suporte à [migração sem agente ou baseada em agente](server-migrate-overview.md).

- **VMs do VMware**: verifique [os requisitos de migração e o suporte](migrate-support-matrix-vmware-migration.md) para as VMs do VMware.
- **VMs do Hyper-V**: Verifique os [requisitos de migração e suporte](migrate-support-matrix-hyper-v-migration.md) para VMs Hyper-V.
- **Computadores físicos**: verifique [os requisitos de migração e o suporte](migrate-support-matrix-physical-migration.md) para computadores físicos locais e outros servidores virtualizados. 

## <a name="review-url-and-port-access"></a>Examinar o acesso a URL e à porta

Talvez os computadores precisem de acesso à Internet durante a migração.

- **Dispositivo de Migrações para Azure**: examine as [URLs](migrate-appliance.md#url-access) e as [portas](migrate-support-matrix-vmware-migration.md#agentless-ports) que o dispositivo das Migrações para Azure precisa acessar durante a migração sem agente.
- **Migração baseada em agente da VM do VMware**: examine as [URLs](migrate-replication-appliance.md#url-access) e as [portas](migrate-replication-appliance.md#port-access) usadas pelo dispositivo de replicação durante a migração baseada em agente da VM do VMware. 
- **Hosts do Hyper-V**: examine [as URLs e as portas](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) que os hosts do Hyper-V precisam acessar durante a migração. 
- **Servidores físicos**: examine as [URLs](migrate-replication-appliance.md#url-access) e as [portas](migrate-replication-appliance.md#port-access) usadas pelo dispositivo de replicação durante a migração do servidor físico.

## <a name="verify-required-changes-before-migrating"></a>Verificar as alterações necessárias antes da migração

Algumas VMs podem precisar de alterações, de modo que possam ser executadas no Azure. As Migrações para Azure fazem essas alterações automaticamente nas VMs que executam estes sistemas operacionais:

- Red Hat Enterprise Linux 7.0 e posterior, 6.5 e posterior
- CentOS 7.0 e posterior, 6.5 e posterior
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

Para outros sistemas operacionais, é necessário preparar os computadores manualmente antes da migração. 

### <a name="prepare-windows-machines"></a>Preparar computadores Windows

Se você estiver migrando um computador Windows, faça as alterações a seguir antes da migração. Se você migrar a VM antes de realizar as alterações, talvez ela não seja inicializada no Azure.

1. [Habilite o console serial do Azure](../virtual-machines/troubleshooting/serial-console-windows.md) para a VM do Azure. A habilitação do console ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada com a imagem de disco. A inicialização da imagem de disco é equivalente a uma reinicialização para a nova VM. 
2. Se você estiver migrando computadores que executam o Windows Server 2003, instale o Integration Services de Convidado do Hyper-V no sistema operacional da VM. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Preparar computadores Linux

1. Instalar o Integration Services do Linux para Hyper-V. A maioria das novas versões das distribuições do Linux inclui o Integration Services do Hyper-V Linux por padrão.
2. Recompile a imagem de inicialização do Linux, de modo que ela contenha os drivers necessários do Hyper-V. A recompilação da imagem de inicialização garante que a VM será inicializada no Azure (necessário somente em algumas distribuições).
3. [Habilite o log do console serial do Azure](../virtual-machines/troubleshooting/serial-console-linux.md). A habilitação do log do console ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada com a imagem de disco. A inicialização da imagem de disco é equivalente a uma reinicialização para a nova VM.
4. Atualize o arquivo do mapa do dispositivo com as associações nome do dispositivo/volume, a fim de usar identificadores de dispositivo persistentes.
5. Atualize as entradas no arquivo fstab para usar identificadores de volume persistentes.
6. Remova as regras do Udev que reservam nomes de interface com base no endereço MAC etc.
7. Atualize adaptadores de rede para receber um endereço IP do DHCP.

Saiba mais sobre as [etapas necessárias para executar uma VM do Linux no Azure](../virtual-machines/linux/create-upload-generic.md) e obtenha instruções para algumas distribuições populares do Linux.

## <a name="preserve-drive-letters-after-migration"></a>Preservar letras da unidade após a migração

Quando você migra um computador local para o Microsoft Azure, as letras da unidade de discos de dados adicionais podem ter os respectivos valores originais alterados. 

Por padrão, é atribuída a unidade D às VMs do Azure para uso como armazenamento temporário. Essa atribuição de unidades faz todas as outras atribuições de unidade de armazenamento anexadas serem incrementadas em uma letra. Por exemplo, se a instalação local usar um disco de dados atribuído à unidade D para instalações de aplicativos, a atribuição dessa unidade será incrementada para a unidade E após você migrar a VM para o Azure. Para evitar essa atribuição automática e para garantir que o Azure atribua a próxima letra da unidade livre ao volume temporário, defina a política de SAN (rede de área de armazenamento) como **OnlineAll**:

1. No computador local (não no servidor host), abra um prompt de comandos com privilégios elevados.
2. Insira **diskpart**.
3. Insira **SAN**. Se a letra da unidade do sistema operacional convidado não for mantida, **Todos Offline** ou **Compartilhados Offline** será retornado.
4. No prompt **DISKPART**, insira **SAN Policy=OnlineAll**. Essa configuração garante que os discos sejam colocados online e que você possa fazer leituras e gravações nos dois discos.
5. Durante essa migração de teste, você poderá verificar se as letras da unidade foram preservadas.

## <a name="check-azure-vm-requirements"></a>Verificar os requisitos de VM do Azure

Os computadores locais replicados no Azure precisam atender aos requisitos da VM do Azure de sistema operacional e arquitetura, discos, configurações de rede e nomenclatura da VM. Verifique os requisitos de [VMs do VMware e servidores físicos](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) e de [VMs do Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) antes da migração.

## <a name="prepare-to-connect-after-migration"></a>Prepare-se para se conectar após a migração

As VMs do Azure são criadas durante a migração para o Azure. Após a migração, você precisará conseguir se conectar às novas VMs do Azure. Várias etapas são necessárias para uma conexão bem-sucedida.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Preparar a conexão com as VMs do Azure para Windows

Em computadores Windows locais:

1. Defina as configurações do Windows. As configurações incluem a remoção de rotas persistentes estáticas ou do proxy WinHTTP.
2. Verifique se todos os [serviços necessários](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.
3. Habilite a RDP (Área de Trabalho Remota) para permitir conexões remotas com o computador local. Saiba como [usar o PowerShell para habilitar o RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Para acessar uma VM do Azure pela Internet após a migração, no Firewall do Windows, no computador local, dê permissão ao TCP e UDP no Perfil público e defina RDP como um aplicativo com permissão para todos os perfis.
5. Se desejar acessar uma VM do Azure por um VPN site a site após a migração, no Firewall do Windows no computador local, dê permissão ao RDP para os perfis Domínio e Privado. Saiba como [permitir o tráfego RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Verifique se não há nenhuma atualização do Windows pendente na VM local quando você fizer a migração. Se houver, a instalação das atualizações poderá ser iniciada na VM do Azure após a migração e você não poderá entrar na VM até a conclusão das atualizações.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Preparar-se para se conectar às VMs do Azure para Linux

Em computadores Linux locais:

1. Verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma conexão SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurar VMs do Azure após a migração

Após a migração, conclua estas etapas nas VMs do Azure criadas:

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. É necessário usar um endereço IP público diferente para a VM do Azure daquele que usou para o computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada à porta RDP ou SSH.
3. Confira [Diagnóstico de inicialização](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para visualizar a VM.

> [!NOTE]
> O serviço Azure Bastion oferece o acesso privado RDP e SSH a VMs do Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="next-steps"></a>Próximas etapas

Decida qual método você deseja usar para [migrar as VMs do VMware](server-migrate-overview.md) para o Azure ou comece a migrar [VMs do Hyper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizadas ou de nuvem](tutorial-migrate-physical-virtual-machines.md).
