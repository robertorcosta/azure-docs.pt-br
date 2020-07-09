---
title: Perguntas frequentes sobre a migração do Armazenamento do Microsoft Azure | Microsoft Docs
description: Respostas para perguntas comuns sobre a migração do Armazenamento do Azure
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: f0272d53c5fc4c565baf5d7105bd6e1b4a0ef535
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114594"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas frequentes sobre a migração do Armazenamento do Azure

Este artigo responde perguntas frequentes sobre a migração do Armazenamento do Azure.

## <a name="copy-upload-or-download"></a>Copiar, carregar ou baixar

**Como criar um script para copiar arquivos de um contêiner para outro?**

Para copiar arquivos entre contêineres, use o AzCopy. Consulte o seguinte exemplo:

```azurepowershell-interactive
AzCopy /Source:https://xxx.blob.core.windows.net/xxx
/Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
/S
```

O AzCopy usa a [API do Blob de Cópia](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para realizar a cópia de cada arquivo no contêiner.  

Use qualquer máquina virtual ou máquina local com acesso à Internet para executar o AzCopy. Você também pode usar o agendamento do Lote do Azure para fazer isso automaticamente, mas isso é mais complicado.  

O script de automação destina-se à implantação do Azure Resource Manager, não à manipulação do conteúdo de armazenamento. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Há algum encargo para cópia de dados entre dois compartilhamentos de arquivo na mesma conta de armazenamento dentro da mesma região?**

Não. Não há encargos para esse processo.

**Como baixar 1 a 2 TB de dados do portal do Azure?**

Use o AzCopy para baixar os dados. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso baixar um VHD em uma máquina local, além de usar a opção de download no portal?**

Use o [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) para baixar um VHD.

**Como fazer download dos dados para um computador baseado em Linux, de uma conta de armazenamento do Azure, ou carregar dados de um computador com Linux?**

Use a CLI do Azure.

- Baixar um blob individual:

    ```azurecli-interactive
    azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"
    ```

- Carregar um blob individual:

    ```azurecli-interactive
    azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"
    ```

**Como fazer para migrar Blobs de uma conta de armazenamento para outra?**

 Você pode fazer isso usando nosso [Script de migração de Blob](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migração ou backup

**Como mover dados de um contêiner de armazenamento para outro?**

Siga estas etapas:

1.  Crie o contêiner (pasta) no blob de destino.

2.  Use o [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) para copiar o conteúdo do contêiner do blob original para o contêiner de um blob diferente.

**Como criar um script do PowerShell para mover dados de um compartilhamento de arquivos do Azure para outro no Armazenamento do Azure?**

Use o AzCopy para mover os dados de um compartilhamento de arquivo do Azure para outro no Armazenamento do azure. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como carregar arquivos .csv grandes no Armazenamento do Azure?**

Use o AzCopy para carregar arquivos .csv grandes no armazenamento do Azure. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Preciso mover os logs da unidade D para a minha conta de armazenamento do Azure todos os dias. Como faço para automatizar isso?**

Você pode usar o AzCopy e criar uma tarefa no Agendador de Tarefas. Carregue arquivos em uma conta de armazenamento do Azure usando um script em lotes do AzCopy. Para obter mais informações, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](../../cloud-services/cloud-services-startup-tasks.md).

**Como faço para mover minha conta de armazenamento entre as assinaturas?**

Use o AzCopy para mover suas conta de armazenamento entre as assinaturas. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover 10 TB de dados para um armazenamento em outra região?**

Use o AzCopy para mover os dados. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como copiar dados do local para o Armazenamento do Azure?**

Use o AzCopy para copiar os dados. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover dados do local para os Arquivos do Azure?**

Use o AzCopy para mover os dados. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover discos gerenciados para outra conta de armazenamento?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Siga estas etapas:

1.  Pare a máquina virtual à qual o disco gerenciado está conectado.

2.  Copie o VHD do disco gerenciado de uma área para outra executando o seguinte script do Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerenciado usando o arquivo VHD em outra região na qual você copiou o VHD. Para fazer isso, execute este script do Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Para saber mais sobre como implantar uma máquina virtual de um disco gerenciado, veja [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Como mover ou baixar dados de uma conta de armazenamento?**

Use o AzCopy para baixar os dados. Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como fazer para mudar de uma conta de Armazenamento Premium para Armazenamento Standard?**

Siga estas etapas:

1.  Crie uma conta de armazenamento padrão. (Ou use uma conta de armazenamento padrão existente na sua assinatura.)

2.  Faça o download do AzCopy. Execute um dos seguintes comandos do AzCopy.

    Para copiar os discos inteiros na conta de armazenamento:

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /S
    ```

    Para copiar somente um disco, forneça o nome do disco em **Padrão**:

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd
    ```

A operação pode levar várias horas para ser concluída.

Para verificar se a transferência foi concluída com êxito, examine o contêiner da conta de armazenamento de destino no Portal do Azure. Após os discos serem copiados para a conta de armazenamento padrão, você poderá anexá-los à máquina virtual como um disco existente. Para saber mais, confira [Como anexar um disco de dados gerenciados a uma VM do Windows no Portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Como mover de uma conta de armazenamento clássico para uma conta de armazenamento do Azure Resource Manager?**

Use o cmdlet **Move-AzureStorageAccount**. Esse cmdlet tem várias etapas (validar, preparar, confirmar). Você pode validar a movimentação antes de concluí-la.

Se você tiver máquinas virtuais, precisará executar etapas adicionais antes de migrar os dados da conta de armazenamento. Para saber mais, confira [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como fazer backup de toda minha conta de armazenamento para outra conta de armazenamento?**

Não há opção para fazer backup direto de uma conta de armazenamento inteira. Mas você pode mover manualmente o contêiner nessa conta de armazenamento para outra conta usando o AzCopy ou o Gerenciador de Armazenamento. As etapas a seguir mostram a você como usar o AzCopy para mover o contêiner:  

1.  Instale a ferramenta de linha de comando [AzCopy](storage-use-azcopy.md). Essa ferramenta ajuda você a mover o arquivo VHD entre as contas de armazenamento.

2.  Após a instalação do AzCopy no Windows usando o instalador, abra uma janela do Prompt de Comando e navegue até a pasta de instalação do AzCopy em seu computador. Por padrão, o AzCopy está instalado em **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Execute o comando a seguir para mover o contêiner. Substitua o texto pelos valores reais.   

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /S
    ```

    - `/Source`: Fornece o URI para a conta de armazenamento de origem (até o contêiner).  
    - `/Dest`: Fornece o URI para a conta de armazenamento de destino (até o contêiner).  
    - `/SourceKey`: Fornece a chave primária para a conta de armazenamento de origem. Você pode copiar essa chave do portal do Azure selecionando a conta de armazenamento.  
    - `/DestKey`: Fornece a chave primária para a conta de armazenamento de destino. Você pode copiar essa chave do portal selecionando a conta de armazenamento.

Após a execução desse comando, os arquivos do contêiner vão para a conta de armazenamento de destino.

> [!NOTE]
> A CLI do AzCopy não funciona junto com a opção **Pattern** quando você copia de um blob do Azure para outro.
>
> Você pode copiar e editar o comando do AzCopy diretamente, e verificar se **Pattern** corresponde à fonte. Verifique também se os curingas **/S** estão em vigor. Para saber mais, veja [Parâmetros do AzCopy](storage-use-azcopy.md).

**Como fazer backup do meu armazenamento de arquivos do Azure?**

Não há solução de backup. No entanto, Os Arquivos do Azure também dão suporte a cópia assíncrona. Portanto, você pode copiar arquivos:

- De um compartilhamento para outro dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

- De um compartilhamento para um contêiner de blobs dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md).

## <a name="configuration"></a>Configuração

**Como alterar o local secundário de uma conta de armazenamento para a região da Europa?**

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A seleção da região secundária se baseia na região primária e não pode ser alterada. Para obter mais informações sobre o GRS, consulte [Armazenamento com redundância geográfica (GRS): Replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy.md).

**Onde posso obter mais informações sobre a criptografia de armazenamento do Azure?**  

Veja os artigos a seguir:

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Usar chaves gerenciadas pelo cliente com Azure Key Vault para gerenciar a criptografia de armazenamento do Azure](encryption-customer-managed-keys.md)
- [Fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs (visualização)](encryption-customer-provided-keys.md)

**Como posso criptografar dados em uma conta de armazenamento?**

Depois que você habilita a criptografia em uma conta de armazenamento, os dados existentes não são criptografados. Para criptografar os dados existentes, carregue-os novamente na conta de armazenamento.

Use o AzCopy para copiar os dados em uma conta de armazenamento diferente e mova-os de volta. Também é possível usar a [criptografia em repouso](storage-service-encryption.md).

**Existem pré-requisitos para alterar a replicação de uma conta de armazenamento, de armazenamento com redundância geográfica para armazenamento com redundância local?**

Não.

**Como converter um compartilhamento de arquivos em Armazenamento Premium do Azure?**

O Armazenamento Premium não é permitido em um Compartilhamento de arquivos do Azure.

**Como fazer para atualizar de uma conta de Armazenamento Standard para uma conta de Armazenamento Premium? Como fazer downgrade de uma conta de Armazenamento Premium para uma conta de Armazenamento Standard?**

Você deve criar a conta de armazenamento de destino, copiar os dados da conta de origem para a conta de destino e excluir a conta de origem. Você pode usar uma ferramenta como o AzCopy para executar a cópia dos dados.

Se você tiver máquinas virtuais, precisará executar etapas adicionais antes de migrar os dados da conta de armazenamento. Para saber mais, confira [Migrar para o Armazenamento Premium do Azure (discos não gerenciados)](storage-migration-to-premium-storage.md).

**Como posso dar a outras pessoas acesso aos meus recursos de armazenamento?**

Para dar a outras pessoas acesso aos meus recursos de armazenamento:

- Use um token SAS (assinatura de acesso compartilhado) para fornecer acesso a um recurso.

- Forneça a um usuário a chave primária ou secundária da conta de armazenamento. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](storage-account-keys-manage.md).

- Altere a política de acesso para permitir o acesso anônimo. Para saber mais, confira [Conceder permissões de usuário anônimo a contêineres e blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde o AzCopy é instalado?**

- Se você acessar o AzCopy da linha de comando do Armazenamento do Microsoft Azure, digite **AzCopy**. A linha de comando é instalada junto com o AzCopy.

- Se você tiver instalado a versão de 32 bits, ela estará aqui: **%ProgramFiles(x86)%\\SDKs do Microsoft\\Azure\\AzCopy**.

- Se você tiver instalado a versão de 64 bits, ela estará aqui: **%ProgramFiles%\\SDKs do Microsoft\\Azure\\AzCopy**.

**Como usar um domínio personalizado de HTTPS com minha conta de armazenamento? Por exemplo, como faço para "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" aparecer como "https:\//www.contoso.com/images/image.gif"?**

No momento, o TLS/SSL não é compatível com contas de armazenamento com domínios personalizados.
Mas você pode usar domínios personalizados não HTTPS. Para saber mais, confira [Configurar um nome de domínio personalizado para seu ponto de extremidade do Armazenamento de Blobs](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Acesso ao armazenamento

**Como mapear uma pasta de contêiner em uma máquina virtual?**

Use um compartilhamento de arquivos do Azure.

**Como acessar o armazenamento com redundância dos Arquivos do Azure?**

O armazenamento com redundância geográfica com acesso de leitura é necessário para acessar o armazenamento redundante. No entanto, os Arquivos do Azure dão suporte apenas a armazenamento com redundância local e a armazenamento com redundância geográfica padrão que não permite acesso somente leitura.

**Para uma conta de armazenamento replicado (como armazenamento com redundância de zona, armazenamento com redundância geográfica ou armazenamento com redundância geográfica com acesso de leitura), como fazer para acessar dados armazenados na região secundária?**

- Se você estiver usando um armazenamento com redundância de zona ou armazenamento com redundância geográfica, não poderá acessar dados da região secundária, a menos que você inicie um failover a uma região. Para saber mais sobre o processo de failover, confira [Recuperação de desastre e failover da conta de armazenamento](storage-disaster-recovery-guidance.md).

- Se você estiver usando armazenamento com redundância geográfica com acesso de leitura, poderá acessar dados da região secundária a qualquer hora. Use um dos métodos a seguir:  

  - **AzCopy**: Acrescente **-secundário** ao nome da conta de armazenamento na URL para acessar o ponto de extremidade secundário. Por exemplo:  

      `https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd`

  - **Token SAS**: Use um Token SAS para acessar dados do ponto de extremidade. Para obter mais informações, confira [Como usar assinaturas de acesso compartilhado](storage-sas-overview.md).

**Como usar o FTP para acessar dados que estão em uma conta de armazenamento?**

Não é possível acessar uma conta de armazenamento diretamente por meio de FTP. No entanto, você pode configurar uma máquina virtual do Azure e, depois, instalar um servidor FTP na máquina virtual. Você pode fazer com que o servidor FTP armazene arquivos em um compartilhamento dos Arquivos do Azure ou em um disco de dados que está disponível para a máquina virtual.

Se você quiser só baixar os dados sem a necessidade de usar o Gerenciador de Armazenamento ou um aplicativo semelhante, talvez seja possível usar um token SAS. Para obter mais informações, confira [Como usar assinaturas de acesso compartilhado](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
