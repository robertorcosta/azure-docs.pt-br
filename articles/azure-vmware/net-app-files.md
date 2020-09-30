---
title: Arquivos da NetApp para solução do Azure VMware
description: Use Azure NetApp Files com VMs do Azure VMware para migrar e sincronizar dados entre servidores locais, VMs de solução do Azure VMware e infraestruturas de nuvem.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573915"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>Arquivos da NetApp para solução do Azure VMware

Neste artigo, percorreremos as etapas de integração de Azure NetApp Files com cargas de trabalho baseadas em soluções VMware do Azure. [Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) é um serviço do Azure para migrar e executar cargas de trabalho de arquivo corporativo na nuvem sem alterações de código. Ele permite a fácil migração de dados entre infraestruturas locais e na nuvem. A sincronização rápida de dados com segurança aprimorada simplifica os cenários de migração e DevOps com recursos como instantâneo, restauração e integração de Active Directory instantâneas.

## <a name="topology"></a>Topologia

Nesse cenário, para testar e verificar o compartilhamento do pool de Azure NetApp Files, o Azure NetApp Files está configurado no Azure com pool de capacidade, pool de volumes e sub-rede. Nós usamos o protocolo NFS. Os Azure NetApp Files e a solução VMware do Azure são criados na mesma região do Azure, leste dos EUA. A conexão com a solução Azure VMware é por meio do Azure ExpressRoute.

![Arquivos da NetApp para a topologia de solução do Azure VMware.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Pré-requisitos 

> [!div class="checklist"]
> * Assinatura do Azure com Azure NetApp Files
> * Sub-rede para o arquivo do Azure NetApp
> * VM do Linux na solução VMware do Azure
> * VM do Windows na solução VMware do Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Verificar configuração de Azure NetApp Files 

Primeiro, verifique a configuração de Azure NetApp Files, criada no Azure em um disco Premium.

1. Na portal do Azure, em **armazenamento**, selecione **Azure NetApp files**. Uma lista de seus Azure NetApp Files configurados será mostrada.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Lista de Azure NetApp Files."::: 

2. Se você selecionar uma conta do NetApp, poderá exibir várias configurações. Por exemplo, se você selecionar **contoso-anf2**, verá suas configurações. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Lista de Azure NetApp Files."::: 

3. Selecione **pools de capacidade** para verificar o pool configurado. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Lista de Azure NetApp Files.":::

    Você pode ver que o pool de armazenamento está configurado como 4 TiB com o disco Premium.

4. Selecione **volumes** (consulte a captura de tela da etapa 2) para exibir os volumes criados no pool de capacidade.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Lista de Azure NetApp Files.":::

5. Selecione um volume para exibir sua configuração.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Lista de Azure NetApp Files.":::

    Você pode ver que o pool de volumes anfpool, com 200 GiB, foi criado como um compartilhamento de arquivos do NetApp. Uma rede virtual IP privada foi criada para Azure NetApp Files que fornece o caminho NFS para montagem nas VMs da solução Azure VMware. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protocolos com suporte pelo Azure NetApp Files  

O Azure NetApp Files dá suporte ao protocolo SMB e ao mapeamento de compartilhamento NFS (sistema de arquivos de rede). 

- **Compartilhamento SMB**: para implantar um volume SMB, primeiro você precisa criar uma conexão Active Directory. Os controladores de domínio especificados precisam estar acessíveis pela sub-rede delegada do Azure NetApp Files para uma conexão bem-sucedida. Quando Active Directory estiver configurada na conta de Azure NetApp Files, ela será exibida como um item selecionável durante a criação de volumes SMB. 

- **Compartilhamento NFS**: Azure NetApp files contribui para criar volumes usando NFS (NFSv3 e nfsv 4.1), SMBv3 ou um protocolo duplo (NFSV3 e SMB). O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. O NFS pode ser montado em um servidor Linux usando uma linha de comando.

## <a name="create-azure-netapp-files"></a>Criar Azure NetApp Files 

1. Faça logon no [Portal do Azure](https://rc.portal.azure.com/#home). Em serviços do Azure, selecione **Azure NetApp files**. 

2. Selecione **+ Adicionar** para criar um novo Volume de Azure NetApp files. 

3. Preencha os campos obrigatórios (nome, assinatura, grupo de recursos e local) e selecione **criar**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Adicionar pools de capacidade em Azure NetApp Files 

1. Na portal do Azure, selecione **Azure NetApp files**, selecione **pools de capacidade** e **+ Adicionar pool**. 

2. Insira os detalhes necessários para o nome do pool de volumes, nível de serviço e tamanho do disco (FQDN) ou IP e peso. Selecione **Adicionar**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Lista de Azure NetApp Files.":::

3. Para criar volumes sob o pool de capacidade, selecione **volumes** no painel de pesquisa e selecione **+ Adicionar volume**. 
 
4. Preencha os campos obrigatórios, conforme mostrado na captura de tela a seguir.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Lista de Azure NetApp Files.":::

5. Na página seguinte, selecione o tipo de protocolo compartilhamento. Selecione as versões se for um compartilhamento NFS e selecione o domínio Active Directory se for um compartilhamento SMB.  

6. Se for um compartilhamento NFS, adicione o endereço IP de origem de onde o compartilhamento de tipo de protocolo será acessado. Selecione **Examinar + criar**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Lista de Azure NetApp Files.":::
 
    Em Azure NetApp Files na portal do Azure, seu compartilhamento NFS está pronto para uso.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Lista de Azure NetApp Files.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Montar um compartilhamento de arquivos NFS em suas VMs de solução do Azure VMware

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Montar um compartilhamento de arquivos NFS em uma VM do Windows da solução do Azure VMware

- Abra um prompt de comando e execute o comando para mapear o compartilhamento de arquivos NFS. As capturas de tela a seguir mostram a unidade de compartilhamento mapeada em uma VM do Windows na solução VMware do Azure.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Lista de Azure NetApp Files.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Lista de Azure NetApp Files.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Montar um compartilhamento de arquivos NFS em uma VM Linux da solução Azure VMware

#### <a name="setting-up-your-azure-instance"></a>Configurando sua instância do Azure

1. Em seu portal do Azure, associe sua instância do Azure a uma sub-rede definida na mesma rede virtual que o volume.

    > [!NOTE]
    > A sub-rede precisa de uma regra de grupo de segurança de rede que permita o tráfego nas portas NFS (2049, 111), UDP e TCP.

2. Abra um cliente SSH e conecte-se à instância do Azure. Para obter mais informações, consulte [como usar chaves SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Instale o cliente NFS em sua instância do Azure:
   - Na instância do Red Hat Enterprise Linux ou SUSE Linux: `sudo yum install -y nfs-utils`
   - Em uma instância do Ubuntu ou Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Montando o sistema de arquivos

1. Crie um novo diretório em sua instância do Azure: `sudo mkdir ANFPOOL`

2. Selecione um endereço IP de destino de montagem.

3. Monte seu sistema de arquivos: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Lista de Azure NetApp Files.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Criar uma conexão Active Directory para um compartilhamento SMB

1. Selecione uma conta do NetApp no portal do Azure.

2. Em Azure NetApp Files, selecione **Active Directory conexões**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Lista de Azure NetApp Files."::: 

3. Selecione **ingressar** para ingressar no compartilhamento SMB para Active Directory. A captura de tela a seguir mostra os detalhes de domínio do compartilhamento SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Lista de Azure NetApp Files."::: 

    O compartilhamento de arquivos SMB do Azure está pronto para uso.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Lista de Azure NetApp Files."::: 

4. Mapeie o compartilhamento SMB para sua VM de solução do Windows Azure VMware. Use o caminho de montagem SMB, conforme mostrado na captura de tela anterior. Para obter mais informações, consulte [mapear uma unidade de rede no Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [hierarquia de armazenamento do Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Consulte [Gerenciamento de ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md)
- Consulte [integrar a solução do Azure VMware em uma arquitetura de Hub e spoke](concepts-hub-and-spoke.md)
