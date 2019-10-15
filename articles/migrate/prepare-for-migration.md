---
title: Preparar computadores locais para migração para Azure com as Migrações para Azure
description: Este artigo descreve como preparar computadores locais para migração para Azure com as Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d2eeada500de0f174178234ab92b51bcf16b3502
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176742"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparar computadores locais para migração para Azure

Este artigo descreve como preparar um computador local e um computador baseado em nuvem antes de começar a migrá-los para o Azure com a [Migração de Servidor de Migrações para Azure](migrate-services-overview.md#azure-migrate-server-migration).


Neste artigo você:
> [!div class="checklist"]
> * Verificará limitações de migração.
> * Verificará os requisitos do sistema operacional e as limitações de suporte.
> * Examinará o acesso a URL/porta para computadores que você deseja migrar.
> * Examinará alterações que você pode precisar fazer antes de começar a migração.
> * Definirá configurações para que as letras da unidade sejam preservadas após a migração.
> * Preparará computadores para poder se conectar às VMs do Azure após a migração.


## <a name="verify-migration-limitations"></a>Verificará limitações de migração

- Você pode acessar até 35 mil VMs VMware/Hyper-V em um único projeto de Migrações para Azure usando a Migração de Servidor de Migrações para Azure. Um projeto pode combinar VMs VMware e Hyper-V, até os limites para cada um.
- Você pode selecionar até 10 VMs ao mesmo tempo para migração. Se precisar replicar mais, replique em grupos de 10.
- Para migração sem agente de VMware, você pode executar até 100 replicações simultaneamente.

## <a name="verify-operating-system-requirements"></a>Verificar requisitos do sistema operacional

- Verifique se seus [sistemas operacionais Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são compatíveis com o Azure.
- Verifique se suas [distribuições Linux](../virtual-machines/linux/endorsed-distros.md) são compatíveis com o Azure.


## <a name="check-whats-supported"></a>Verifique o que tem suporte

- Para VMs VMware, a Migração de Servidor de Migrações para Azure dá suporte à [migração sem agente ou baseada em agente](server-migrate-overview.md). Verifique os requisitos de migração/suporte da VM VMware para migrações [sem agente](migrate-support-matrix-vmware.md#migration---limitations) e [baseadas em agente](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements).
- Verifique os [requisitos de migração e suporte](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) para VMs Hyper-V.
- Verifique [requisitos de migração e suporte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) for computadores físicos locais ou servidores virtualizados. Esses requisitos são semelhantes aos requisitos de VM VMware.





## <a name="review-urlport-access"></a>Examinar acesso à URL/porta

Talvez os computadores precisem de acesso à Internet durante a migração.

- Examine URLs que as VMs VMware precisam acessar durante as migrações [sem agente](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) ou [baseadas em agente](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
- Examine as URLs que os hosts Hyper-V precisam acessar durante a migração. As VMs Hyper-V não precisam de acesso à Internet.
- [Examine as URLs](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) que computadores físicos ou outros servidores virtualizados precisam acessar durante a migração.
- Na migração baseada em agente de VMs VMware/servidores físicos, o Serviço de Mobilidade em execução nos computadores precisa de acesso a componentes de Migrações para Azure. Para o gerenciamento de replicação, o serviço em execução no computador se comunica com o dispositivo de replicação de Migrações para Azure local na porta HTTPS 443 de entrada. Os computadores enviam dados de replicação para o servidor de processo de Migrações para Azure na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.


## <a name="verify-required-changes-before-migration"></a>Verificar alterações necessárias antes da migração

Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. As Migrações para Azure realizam essas alterações automaticamente para VMs que executam esses sistemas operacionais:
- Red Hat Enterprise Linux 6.5+, 7.0+
- CentOS 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
- Debian 7, 8

Para outros sistemas operacionais, você precisa preparar computadores manualmente antes da migração. 

### <a name="prepare-windows-machines"></a>Preparar computadores Windows

Se você estiver migrando um computador Windows, realize essas alterações antes da migração. Se você migrar a VM antes de realizar as alterações, talvez ela não seja inicializada no Azure.

1. [Habilite o console de acesso serial do Azure](../virtual-machines/troubleshooting/serial-console-windows.md) para a VM do Azure. Isso ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada usando a imagem do disco. Isso é equivalente a uma reinicialização da nova VM. 
2. Se você estiver migrando computadores que executam o Windows Server 2003, instale os Serviços de Integração de Convidado do Hyper-V no sistema operacional da VM. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Preparar computadores Linux

1. Instalar o Integration Services do Linux para Hyper-V. A maioria das novas versões de distribuições do Linux inclui isso por padrão.
2. Recompile a imagem de inicialização do Linux para que ela contenha os drivers necessários do Hyper-V. Com isso, a VM será inicializada no Azure e só será necessária em algumas distribuições.
3. [Habilite o registro em log do console serial do Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Isso ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada usando a imagem do disco. Isso é equivalente a uma reinicialização da nova VM.
4. Atualize o arquivo do mapa do dispositivo com o nome do dispositivo para associações de volume, a fim de usar identificadores de dispositivo persistentes.
5. Atualize entradas fstab para usar identificadores de volume persistentes.
6. Remova regras udev que reservam nomes de interface com base no endereço MAC etc.
7. Atualize adaptadores de rede para receber um endereço IP do DHCP.
8. [Saiba mais](../virtual-machines/linux/create-upload-generic.md) sobre as etapas necessárias para executar uma VM Linux no Azure e obter instruções para algumas distribuições populares do Linux.

## <a name="preserve-drive-letters-after-migration"></a>Preservar letras da unidade após a migração

Quando você migra um computador local para o Microsoft Azure, as letras da unidade de discos de dados adicionais podem ter seus valores anteriores alterados. Por padrão, é atribuída a unidade D às VMs do Azure para uso como armazenamento temporário. Essa atribuição de unidades faz todas as outras atribuições de unidade de armazenamento anexadas serem incrementadas em uma letra.

Por exemplo, se a instalação local usar um disco de dados atribuído à unidade D para instalações de aplicativos, a atribuição dessa unidade será incrementada para a unidade E após você migrar a VM para o Azure. Para evitar essa atribuição automática e para verificar se o Azure atribui a próxima letra da unidade livre ao seu volume temporário, defina a política de SAN (rede de área de armazenamento) como OnlineAll, da seguinte maneira:

1. No computador local (não no servidor host), abra um prompt de comandos com privilégios elevados.
2. Digite **diskpart**.
3. Digite **SAN**. Se a letra da unidade do sistema operacional convidado não for mantida, **Todos Offline** ou **Compartilhados Offline** será retornado.
4. No prompt **DISKPART**, digite **SANPOLICY=ONLINEALL**. Essa configuração verifica se os discos foram colocados online e se os dois estão legíveis e graváveis.
5. Durante essa migração de teste, você poderá verificar se as letras da unidade foram preservadas.


## <a name="check-azure-vm-requirements"></a>Verificar os requisitos de VM do Azure

Os computadores locais que você replica para o Azure devem atender aos requisitos de VM do Azure para sistema operacional e arquitetura, discos, configurações de rede e nomenclatura de VM. Verifique os requisitos de [VMs VMware/servidores físicos](migrate-support-matrix-vmware.md#azure-vm-requirements) e de [VMs Hyper-V](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) antes da migração.


## <a name="prepare-to-connect-after-migration"></a>Prepare-se para se conectar após a migração

As VMs do Azure são criadas durante a migração para o Azure. Após a migração, você precisará estar apto a se conectar às novas VMs do Azure. Há várias etapas necessárias para se conectar com êxito.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Preparar-se para se conectar às VMs do Azure para Windows

Em computadores Windows locais, faça o seguinte:

1. Defina as configurações do Windows. Elas incluem a remoção de rotas persistentes estáticas ou do proxy WinHTTP.
2. Verifique se [esses serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.
3. Habilite a RDP (Área de Trabalho Remota) para permitir conexões remotas com o computador local. [Saiba como](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) habilitar o RDP com o PowerShell.
4. Para acessar uma VM do Azure pela Internet após a migração, no Firewall do Windows, no computador local, dê permissão ao TCP e UDP no Perfil público e defina RDP como um aplicativo com permissão para todos os perfis.
5. Se desejar acessar uma VM do Azure por um VPN site a site após a migração, no Firewall do Windows no computador local, dê permissão ao RDP para os perfis Domínio e Privado. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como dar permissão ao tráfego RDP. 
6. Verifique se não há nenhuma atualização do Windows pendente na VM local quando você migrar. Se houver, a instalação das atualizações poderá ser iniciada na VM do Azure após a migração e você não poderá entrar na VM até a conclusão das atualizações.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Preparar-se para se conectar às VMs do Azure para Linux

Em computadores Linux locais, faça o seguinte:

1. Verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma conexão SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurar VMs do Azure após a migração

Após a migração, faça o seguinte nas VMs do Azure criadas.

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. Você não pode usar o mesmo endereço IP público para a VM do Azure que você usou para seu computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada à porta RDP ou SSH.
3. Confira [Diagnóstico de inicialização](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para exibir a VM.

> [!NOTE]
> O serviço Azure Bastion oferece o acesso privado RDP e SSH a VMs do Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.



## <a name="next-steps"></a>Próximas etapas

Decida qual método você deseja usar para [migrar VMs VMware](server-migrate-overview.md) para o Azure ou comece a migrar as [VMs Hyper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizadas/de nuvem](tutorial-migrate-physical-virtual-machines.md).
