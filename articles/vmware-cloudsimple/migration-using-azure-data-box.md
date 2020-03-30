---
title: Solução Azure VMware - migração usando a caixa de dados do Azure
description: Como usar a Caixa de Dados do Azure para migrar em massa dados para a solução Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019750"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migração de dados para a solução Azure VMware usando a Caixa de Dados Do Azure

A solução em nuvem microsoft azure Data Box permite enviar terabytes (TBs) de dados para o Azure de forma rápida, barata e confiável. A transferência de dados segura é acelerada pelo envio de um dispositivo de armazenamento Data Box proprietário. Cada dispositivo de armazenamento tem uma capacidade máxima de armazenamento utilizável de 80 TB e é transportado para o seu datacenter por uma operadora regional. O dispositivo tem uma caixa reforçada para proteger os dados durante o transporte.

Ao usar a Data Box, você pode migrar em massa seus dados do VMware para sua nuvem privada. Os dados do ambiente VMware vSphere no local são copiados para a Caixa de Dados através do protocolo NFS (Network File System, sistema de arquivos de rede). A migração em massa de dados envolve salvar uma cópia point-in-time de máquinas virtuais, configuração e dados associados à Data Box e, em seguida, enviá-los manualmente para o Azure.

Neste artigo, você aprende sobre:

* Configurando data box.
* Copiando dados do ambiente VMware local para a Caixa de Dados através do NFS.
* Preparando-se para o retorno da Data Box.
* Preparando dados blob para copiar para a Solução Azure VMware.
* Copiando os dados do Azure para sua nuvem privada.

## <a name="scenarios"></a>Cenários

Use a Data Box nos seguintes cenários para migração de dados em massa:

* Para migrar uma grande quantidade de dados de on-premises para a Azure VMware Solution. Este método estabelece uma linha de base e sincroniza diferenças sobre a rede.
* Para migrar um grande número de máquinas virtuais que estão desligadas (máquinas virtuais frias).
* Migrar dados de máquinas virtuais para configurar ambientes de desenvolvimento e teste.
* Para migrar um grande número de modelos de máquinas virtuais, arquivos ISO e discos de máquinavirtual.

## <a name="before-you-begin"></a>Antes de começar

* Verifique os pré-requisitos e [peça data box](../databox/data-box-deploy-ordered.md) através do seu portal Azure. Durante o processo de pedido, você deve selecionar uma conta de armazenamento que habilite o armazenamento Blob. Depois de receber o dispositivo Data Box, conecte-o à sua rede local e [configure o dispositivo](../databox/data-box-deploy-set-up.md) com um endereço IP acessível a partir de sua rede de gerenciamento vSphere.

* Crie uma rede virtual e uma conta de armazenamento na mesma região onde a solução VMware do Azure é provisionada.

* Crie uma conexão de [rede virtual do Azure](cloudsimple-azure-network-connection.md) da sua nuvem privada para a rede virtual onde a conta de armazenamento é criada seguindo as etapas da [rede virtual Connect Azure para o CloudSimple usando o ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Configurar caixa de dados para NFS

Conecte-se à sua Web UI local da Data Box seguindo as etapas da seção "Conecte-se ao seu dispositivo" do [Tutorial: Cabo e conecte-se à sua Caixa de Dados Azure](../databox/data-box-deploy-set-up.md).  Configurar a Caixa de Dados para permitir o acesso aos clientes NFS:

1. Na web ui local, vá para a página **Conectar e copiar.** Em **configurações NFS,** selecione **acesso ao cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/nfs-client-access.png)

2. Digite o endereço IP dos hosts VMware ESXi e **selecione Adicionar**. Você pode configurar o acesso para todos os hosts em seu cluster vSphere repetindo esta etapa. Selecione **OK**.

    ![Configurar o acesso do cliente NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Você não pode copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

Em compartilhamentos de blob de blocos e blob de páginas, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em ações para arquivos Do Azure, entidades de primeiro nível são compartilhamentos e entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Use blobs do Bloco Azure para copiar dados VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Monte o compartilhamento nfs como um armazenamento de dados no cluster vCenter local e copie os dados

O compartilhamento nfs da sua Data Box deve ser montado como um armazenamento de dados no seu cluster vCenter ou no host VMware ESXi no local, a fim de copiar os dados para o armazenamento de dados NFS:

1. Faça login no servidor vCenter no local.

2. Clique com o botão direito do **mouse Datacenter,** selecione **Armazenamento,** selecione **Novo Datastore**e selecione **Next**.

   ![Adicionar novo datastore](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente Adicionar datastore, selecione **NFS** em **Tipo**.

   ![Adicionar novo datastore - tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2 do assistente, selecione **NFS 3** como a versão NFS e, em seguida, selecione **Next**.

   ![Adicionar novo datastore - versão NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3 do assistente, especifique o nome do datastore, do caminho e do servidor. Você pode usar o endereço IP da sua Caixa de Dados para o servidor. O caminho da pasta `/<StorageAccountName_BlockBlob>/<ContainerName>/` será no formato.

   ![Adicionar nova configuração datastore - NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4 do assistente, selecione os hosts ESXi onde deseja que o datastore seja montado e selecione **Next**.  Em um cluster, selecione todos os hosts para garantir a migração das máquinas virtuais.

   ![Adicionar novo datastore - Selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5 do assistente, revise o resumo e selecione **Concluir**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiar dados para o data box NFS datastore

As máquinas virtuais podem ser migradas ou clonadas para o novo datastore.  Quaisquer máquinas virtuais não utilizadas que você deseja migrar podem ser migradas para o data box NFS datastore usando a opção **de armazenamento vMotion.** Máquinas virtuais ativas podem ser clonadas no data box NFS datastore.

* Identifique e liste as máquinas virtuais que podem ser **movidas**.
* Identifique e liste as máquinas virtuais que devem ser **clonadas**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Mova uma máquina virtual para um data box

1. Clique com o botão direito do mouse na máquina virtual que deseja mover para o databox databox e, em seguida, selecione **Migrar**.

    ![Migrar máquina virtual](media/databox-migration-vm-migrate.png)

2. Selecione **Alterar armazenamento somente** para o tipo de migração e, em seguida, selecione **Next**.

    ![Migrar máquina virtual - somente armazenamento](media/databox-migration-vm-migrate-change-storage.png)

3. Selecione **Databox-Datastore** como destino e selecione **Next**.

    ![Migrar máquina virtual - selecionar datastore](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Revise as informações e selecione **Terminar**.

5. Repita as etapas 1 a 4 para máquinas virtuais adicionais.

> [!TIP]
> Você pode selecionar várias máquinas virtuais que estão no mesmo estado de energia (ligadaou ou desligou) e migrar em massa.

A máquina virtual será migrada para o armazenamento de dados NFS da Data Box. Depois que todas as máquinas virtuais forem migradas, você pode desativar (desligar) as máquinas virtuais ativas em preparação para a migração de dados para a Solução VMware do Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clonar uma máquina virtual ou um modelo de máquina virtual para o data box datastore

1. Clique com o botão direito do mouse em uma máquina virtual ou em um modelo de máquina virtual que você deseja clonar. Selecione **Clone** > **Clone para Máquina Virtual**.

    ![Clone de máquina virtual](media/databox-migration-vm-clone.png)

2. Selecione um nome para a máquina virtual clonada ou o modelo de máquina virtual.

3. Selecione a pasta onde deseja colocar o objeto clonado e selecione **Next**.

4. Selecione o cluster ou o pool de recursos onde deseja colocar o objeto clonado e selecione **Next**.

5. Selecione **Databox-Datastore** como o local de armazenamento e, em seguida, selecione **Next**.

    ![Clone de máquina virtual - selecione datastore](media/databox-migration-vm-clone-select-datastore.png)

6. Se você quiser personalizar qualquer opção para o objeto clonado, selecione as opções de personalização e selecione **Next**.

7. Revise as configurações e selecione **Concluir**.

8. Repita as etapas 1 a 7 para modelos adicionais de máquinas virtuais ou de máquinas virtuais.

As máquinas virtuais serão clonadas e armazenadas no armazenamento de dados NFS da Data Box. Depois que as máquinas virtuais forem clonadas, certifique-se de que elas sejam desligadas em preparação para a migração de dados para a Solução VMware do Azure.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copiar arquivos ISO para o data box datastore

1. A partir de sua iu web vCenter no local, vá para **Armazenamento**.  Selecione **Databox-Datastore** e, em seguida, selecione **Arquivos**. Crie uma nova pasta para armazenar arquivos ISO.

    ![Copiar ISO - criar nova pasta](media/databox-migration-create-folder.png)

2. Forneça um nome para a pasta onde os arquivos ISO serão armazenados.

3. Clique duas vezes na pasta recém-criada para abri-la.

4. Selecione **'Upload Files'** e selecione os arquivos ISO que deseja carregar.
    
    ![Copiar ISO - enviar arquivos](media/databox-migration-upload-iso.png)

> [!TIP]
> Se você já tiver arquivos ISO em seu armazenamento de dados no local, você pode selecionar os arquivos e **copiar para** copiar os arquivos para o data box nfs armazenamento de dados.


## <a name="prepare-data-box-for-return"></a>Prepare a Caixa de Dados para o retorno

Depois de todos os dados da máquina virtual, dados de modelo de máquina virtual e quaisquer arquivos ISO serem copiados para o data box NFS, você pode desconectar o armazenamento de dados do seu vCenter. Todas as máquinas virtuais e modelos de máquinas virtuais devem ser removidos do inventário antes de desconectar o datastore.

### <a name="remove-objects-from-inventory"></a>Remover objetos do inventário

1. A partir de sua iu web vCenter no local, vá para **Armazenamento**. Selecione **Databox-Datastore** e selecione **VMs**.

    ![Remover máquinas virtuais do inventário - desligado](media/databox-migration-select-databox-vm.png)

2. Certifique-se de que todas as máquinas virtuais estão desligadas.

3. Selecione todas as máquinas virtuais, clique com o botão direito do mouse e **selecione Remover do inventário**.

    ![Remover máquinas virtuais do inventário](media/databox-migration-remove-vm-from-inventory.png)

4. Selecione **modelos de VM em Pastas** e repita a etapa 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Remova o data box NFS do vCenter

O data box NFS datastore deve ser desconectado dos hosts VMware ESXi antes de se preparar para o retorno.

1. A partir de sua iu web vCenter no local, vá para **Armazenamento**.

2. Clique com o botão direito do **mouse Databox-Datastore** e selecione **Desmontar datastore**.

    ![Desmontar data box data store](media/databox-migration-unmount-datastore.png)

3. Selecione todos os hosts ESXi onde o datastore está montado e selecione **OK**.

    ![Desmontar data box datastore - selecionar hosts](media/databox-migration-unmount-datastore-select-hosts.png)

4. Revise e aceite quaisquer avisos e selecione **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Prepare a Caixa de Dados para o retorno e depois devolva-a

Siga as etapas descritas no artigo [Return Azure Data Box e verifique o upload de dados para o Azure](../databox/data-box-deploy-picked-up.md) para retornar a Data Box. Verifique o status da cópia de dados na sua conta de armazenamento Do Zure. Depois que o status for concluído, você pode verificar os dados em sua conta de armazenamento do Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiar dados do armazenamento Do Azure para a solução Azure VMware

Os dados copiados para o seu dispositivo Data Box estarão disponíveis em sua conta de armazenamento do Azure após o status do pedido da sua Data Box ser concluído. Os dados agora podem ser copiados para a solução Azure VMware. Os dados na conta de armazenamento devem ser copiados para o armazenamento de dados vSAN da sua nuvem privada usando o protocolo NFS. 

Primeiro, copie os dados de armazenamento blob para um disco gerenciado em uma máquina virtual Linux no Azure usando **o AzCopy**. Disponibilize o disco gerenciado através do NFS, monte o compartilhamento nfs como um armazenamento de dados em sua nuvem privada e, em seguida, copie os dados. Este método permite uma cópia mais rápida dos dados para sua nuvem privada.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copie dados para sua nuvem privada usando uma máquina virtual Linux e discos gerenciados e, em seguida, exporte como compartilhamento NFS

1. Crie uma [máquina virtual Linux](../virtual-machines/linux/quick-create-portal.md) no Azure na mesma região onde sua conta de armazenamento é criada e tem uma conexão de rede virtual do Azure com sua nuvem privada.

2. Crie um disco gerenciado cuja capacidade de armazenamento seja maior do que a quantidade de dados blob e [anexe-os à sua máquina virtual Linux](../virtual-machines/linux/attach-disk-portal.md).  Se a quantidade de dados blob for maior do que a capacidade do maior disco gerenciado disponível, os dados devem ser copiados em várias etapas ou usando vários discos gerenciados.

3. Conecte-se à máquina virtual Linux e monte o disco gerenciado.

4. Instale [o AzCopy na sua máquina virtual Linux](../storage/common/storage-use-azcopy-v10.md).

5. Baixe os dados do armazenamento Do Azure Blob no disco gerenciado usando o AzCopy.  Sintaxe `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`de comando: .  Substitua pelo `<storage-account-name>` nome da sua `<container-name>` conta de armazenamento Azure e pelo contêiner que contém os dados copiados através da Data Box.

6. Instale o servidor NFS na sua máquina virtual Linux:

    - Em uma distribuição Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    - Em uma distribuição `sudo yum install nfs-utils`Enterprise Linux: .

7. Altere a permissão da pasta em seu disco gerenciado onde os dados do armazenamento Azure Blob foram copiados.  Altere as permissões para todas as pastas que deseja exportar como uma parte NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Atribua permissões para que os endereços IP `/etc/exports` do cliente acessem o compartilhamento nfs editando o arquivo.

    ```bash
    sudo vi /etc/exports
    ```
    
    Digite as seguintes linhas no arquivo para cada IP host ESXi da sua nuvem privada.  Se você estiver criando compartilhamentos para várias pastas, adicione todas as pastas.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporte as ações da `sudo exportfs -a` NFS usando o comando.

10. Reinicie o servidor de `sudo systemctl restart nfs-kernel-server` kernel NFS usando o comando.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Monte o compartilhamento de NFS da máquina virtual Linux como um armazenamento de dados em um cluster privado de vCenter em nuvem e, em seguida, copie dados

O compartilhamento de NFS da sua máquina virtual Linux deve ser montado como um datastore no seu cluster vCenter de nuvem privada. Depois de montado, os dados podem ser copiados do armazenamento de dados NFS para o armazenamento de dados vSAN em nuvem privada.

1. Faça login no servidor vCenter da nuvem privada.

2. Clique com o botão direito do **mouse Datacenter,** selecione **Armazenamento,** selecione **Novo Datastore**e selecione **Next**.

   ![Adicionar novo datastore](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente Adicionar datastore, selecione o tipo **NFS.**

   ![Adicionar novo datastore - tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2 do assistente, selecione **NFS 3** como a versão NFS e, em seguida, selecione **Next**.

   ![Adicionar novo datastore - versão NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3 do assistente, especifique o nome do datastore, do caminho e do servidor.  Você pode usar o endereço IP da sua máquina virtual Linux para o servidor.  O caminho da pasta `/<folder>/<subfolder>/` será no formato.

   ![Adicionar nova configuração datastore - NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4 do assistente, selecione os hosts ESXi onde deseja que o datastore seja montado e selecione **Next**.  Em um cluster, selecione todos os hosts para garantir a migração das máquinas virtuais.

   ![Adicionar novo datastore - Selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5 do assistente, revise o resumo e selecione **Concluir**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Adicione máquinas virtuais e modelos de máquinas virtuais de um armazenamento de dados NFS ao inventário

1. A partir de sua iu web vCenter em nuvem privada, vá para **Armazenamento**.  Selecione um armazenamento de dados NFS da máquina virtual Linux e, em seguida, selecione **Arquivos**.

    ![Selecione arquivos do armazenamento de dados NFS](media/databox-migration-datastore-select-files.png)

2. Selecione uma pasta que contenha uma máquina virtual ou um modelo de máquina virtual.  No painel de detalhes, selecione um arquivo .vmx para uma máquina virtual ou um arquivo .vmtx para um modelo de máquina virtual.

3. Selecione **Registrar VM** para registrar a máquina virtual em seu vCenter em nuvem privada.

    ![Registre máquina virtual](media/databox-migration-datastore-register-vm.png)

4. Selecione o data center, pasta e cluster/resource pool onde deseja que a máquina virtual seja registrada.

4. Repita as etapas 3 e 4 para todas as máquinas virtuais e modelos de máquinas virtuais.

5. Vá para a pasta que contém os arquivos ISO.  Selecione os arquivos ISO e selecione **Copiar para** copiar os arquivos para uma pasta no seu datastore vSAN.

As máquinas virtuais e os modelos de máquinas virtuais estão agora disponíveis em seu vCenter em nuvem privado. Essas máquinas virtuais devem ser movidas do armazenamento de dados NFS para o armazenamento de dados vSAN antes de atilá-las. Você pode usar a opção **vMotion de armazenamento** e selecionar o datastore vSAN como o destino das máquinas virtuais.

Os modelos de máquina saem da sua máquina virtual Linux NFS para o seu armazenamento de dados vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Limpe sua máquina virtual Linux

Depois que todos os dados forem copiados para sua nuvem privada, você pode remover o armazenamento de dados NFS da sua nuvem privada:

1. Certifique-se de que todas as máquinas e modelos virtuais sejam movidos e clonados para o seu datastore vSAN.

2. Remova do inventário todos os modelos de máquinas virtuais do armazenamento de dados NFS.

3. Desmonte o armazenamento de dados da máquina virtual Linux do seu vCenter de nuvem privada.

4. Exclua a máquina virtual e o disco gerenciado do Azure.

5. Se você não quiser manter os dados transferidos pela Data Box em sua conta de armazenamento, exclua a conta de armazenamento do Azure.  
    


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [data box](../databox/data-box-overview.md).
* Saiba mais sobre [diferentes](migrate-workloads.md)opções para migrar cargas de trabalho para sua nuvem privada.
