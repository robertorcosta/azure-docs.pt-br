---
title: Conecte-se ao failover de VMs do Azure no local com a recuperação do site do Azure
description: Descreve como se conectar às VMs do Azure após failover de on-premises para Azure usando a Recuperação do Site do Azure
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281984"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Conecte-se às VMs do Azure após failover de no local 


Este artigo descreve como configurar a conectividade para que você possa se conectar com sucesso às VMs do Azure após o failover.

Quando você configura a recuperação de desastres de máquinas virtuais (VMs) e servidores físicos no Azure, o [Azure Site Recovery](site-recovery-overview.md) começa a replicar máquinas para o Azure. Então, quando ocorrerem paralisações, você pode falhar no Azure a partir do seu site no local. Quando ocorre failover, o Site Recovery cria As VMs do Azure, usando dados replicou no local. Como parte do planejamento de recuperação de desastres, você precisa descobrir como se conectar a aplicativos em execução nessas VMs do Azure após o failover.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Prepare as máquinas no local antes do failover.
> * Prepare as VMs do Azure após o failover. 
> * Retenha endereços IP em VMs do Azure após failover.
> * Atribua novos endereços IP às VMs do Azure após failover.

## <a name="prepare-on-premises-machines"></a>Prepare máquinas no local

Para garantir a conectividade com as VMs do Azure, prepare suas máquinas no local antes do failover.

### <a name="prepare-windows-machines"></a>Preparar computadores Windows

Em computadores Windows locais, faça o seguinte:

1. Defina as configurações do Windows. Isso inclui a remoção de quaisquer rotas estáticas persistentes ou proxy WinHTTP e a definição da diretiva de SAN de disco para **OnlineAll**. [Siga](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) estas instruções.

2. Verifique se [esses serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.

3. Habilite a RDP (Área de Trabalho Remota) para permitir conexões remotas com o computador local. [Saiba como](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) habilitar o RDP com o PowerShell.

4. Para acessar uma VM do Azure pela internet após o failover, no Firewall do Windows na máquina local, permita TCP e UDP no perfil Público e defina rdp como um aplicativo permitido para todos os perfis.

5. Se você quiser acessar uma VM do Azure através de uma VPN site-to-site após failover, no Firewall do Windows na máquina local, permita RDP para os perfis Domínio e Privado. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como dar permissão ao tráfego RDP.
6. Certifique-se de que não há atualizações do Windows pendentes na VM no local quando você acionar um failover. Se houver, as atualizações podem começar a ser instaladas na VM do Azure após o failover, e você não poderá entrar na VM até que as atualizações terminem.

### <a name="prepare-linux-machines"></a>Preparar computadores Linux

Em computadores Linux locais, faça o seguinte:

1. Verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma conexão SSH.


## <a name="configure-azure-vms-after-failover"></a>Configure As VMs do Azure após failover

Após failover, faça o seguinte nas VMs do Azure criadas.

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. Você não pode usar o mesmo endereço IP público para a VM do Azure que você usou para seu computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md)
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada à porta RDP ou SSH.
3. Confira [Diagnóstico de inicialização](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para exibir a VM.


> [!NOTE]
> O serviço Azure Bastion oferece o acesso privado RDP e SSH a VMs do Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="set-a-public-ip-address"></a>Defina um endereço IP público

Como uma alternativa para atribuir um endereço IP público manualmente a uma VM do Azure, você pode atribuir o endereço durante o failover usando um script ou um runbook de automação do Azure em um [plano de recuperação de](site-recovery-create-recovery-plans.md)recuperação de site, ou você pode configurar o roteamento em nível de DNS usando o Azure Traffic Manager. [Saiba mais](concepts-public-ip-address-with-site-recovery.md) sobre como configurar um endereço público.


## <a name="assign-an-internal-address"></a>Atribuir um endereço interno

Para definir o endereço IP interno de uma VM do Azure após o failover, você tem algumas opções:

- **Mantenha o mesmo endereço IP**: Você pode usar o mesmo endereço IP na VM do Azure como aquele alocado na máquina local.
- **Use diferente sion de IP**: Você pode usar um endereço IP diferente para a VM do Azure.


## <a name="retain-ip-addresses"></a>Manter os endereços IP

A Recuperação do Site permite que você retenha os mesmos endereços IP ao falhar no Azure. A retenção do mesmo endereço IP evita possíveis problemas de rede após o failover, mas introduz alguma complexidade.

- Se o Azure VM de destino usar o mesmo endereço/sub-rede IP que seu site local, você não poderá se conectar entre eles usando uma conexão VPN site-site ou ExpressRoute, devido à sobreposição de endereços. Sub-redes devem ser únicas.
- Você precisa de uma conexão do local com o Azure após o failover, para que os aplicativos estejam disponíveis nas VMs do Azure. O Azure não suporta VLANs esticadas, portanto, se você quiser reter endereços IP, você precisa levar o espaço IP para o Azure falhando sobre toda a sub-rede, além da máquina no local.
- O failover de sub-rede garante que uma sub-rede específica não esteja disponível simultaneamente no local e no Azure.

A retenção de endereços IP requer as seguintes etapas:

- Na Rede de & de Computação, propriedades do item replicado, defina a rede e o endereçamento IP para a VM azure de destino para espelhar a configuração no local.
- As sub-redes devem ser gerenciadas como parte do processo de recuperação de desastres. Você precisa de um Azure VNet para corresponder à rede no local e, após o failover, as rotas de rede devem ser modificadas para refletir que a sub-rede foi transferida para o Azure e novos locais de endereço IP.  

### <a name="failover-example"></a>Exemplo de failover

Vamos examinar um exemplo.

- A empresa fictícia Woodgrove Bank hospeda seus aplicativos de negócios no local Eles hospedam seus aplicativos móveis no Azure.
- Eles se conectam de on-premises ao Azure através de VPN site-to-site. 
- Woodgrove está usando o Site Recovery para replicar máquinas no local para o Azure.
- Seus aplicativos locais usam endereços IP codificados, então eles querem manter os mesmos endereços IP no Azure.
- No local, as máquinas que executam os aplicativos estão sendo executados em três sub-redes:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Seus aplicativos em execução no Azure estão localizados na Rede Azure **VNet Azure** em duas sub-redes:
- 172.16.1.0/24
- 172.16.2.0/24.

Para manter os endereços, aqui está o que eles fazem.

1. Quando eles habilitam a replicação, eles especificam que as máquinas devem ser replicadas na **Rede Azure**.
2. Eles criam **a Rede de Recuperação** no Azure. Este VNet espelha a sub-rede 192.168.1.0/24 em sua rede local.
3. Woodgrove configura uma [conexão VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre as duas redes. 

    > [!NOTE]
    > Dependendo dos requisitos do aplicativo, uma conexão VNet-to-VNet pode ser configurada antes do failover, como um runbook manual de passo/script/automação do Azure em um [plano de recuperação de](site-recovery-create-recovery-plans.md)site ou após o failover ser concluído.

4. Antes do failover, nas propriedades da máquina na Recuperação do Site, eles definem o endereço IP de destino para o endereço da máquina no local, conforme descrito no procedimento seguinte.
5. Após failover, as VMs do Azure são criadas com o mesmo endereço IP. O Woodgrove conecta-se do **Azure Network** ao **Recovery Network** VNet usando o peering VNet (com conectividade de trânsito ativada).
6. No local, Woodgrove precisa fazer alterações na rede, incluindo modificar rotas para refletir que 192.168.1.0/24 mudou-se para o Azure.  

**Infraestrutura antes do failover**

![Antes do failover da sub-rede](./media/site-recovery-network-design/network-design7.png)


**Infraestrutura após o failover**

![Após o failover da sub-rede](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Definir configurações de rede de destino

Antes do failover, especifique as configurações de rede e o endereço IP para a VM azure de destino.

1.  No cofre serviços de recuperação - > **itens replicados,** selecione a máquina no local.
2. Na página **Computação e Rede** da máquina, clique em **Editar,** para configurar as configurações de rede e adaptador para a VM Azure de destino.
3. Em **propriedades de rede,** selecione a rede de destino na qual a VM do Azure será localizada quando for criada após failover.
4. Em **interfaces de rede,** configure os adaptadores de rede na rede de destino. Por padrão, a Recuperação do Site mostra todas as NICs detectadas na máquina no local.
    - No **tipo de interface de rede Target,** você pode definir cada NIC como **principal,** **secundário**ou **não criar** se você não precisar dessa NIC específica na rede de destino. Um adaptador de rede deve ser configurado como principal para failover. Observe que modificar a rede de destino afeta todos os NICs da VM azure.
    - Clique no nome NIC para especificar a sub-rede na qual a VM do Azure será implantada.
    - Sobregravação **dinâmica** com o endereço IP privado que você deseja atribuir ao Alvo Azure VM. Se um endereço IP não for especificado, a Recuperação do Site atribuirá o próximo endereço IP disponível na sub-rede à NIC no failover.
    - [Saiba mais](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sobre o gerenciamento de NICs para failover no local para o Azure.


## <a name="get-new-ip-addresses"></a>Obtenha novos endereços IP

Neste cenário, a VM do Azure recebe um novo endereço IP após failover. Uma atualização de DNS para atualizar registros de falhas sobre máquinas para apontar para o endereço IP da VM do Azure.



## <a name="next-steps"></a>Próximas etapas
[Saiba como](site-recovery-active-directory.md) replicar o Active Directory e o DNS no local para o Azure.


