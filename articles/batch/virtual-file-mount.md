---
title: Montar um sistema de arquivos virtual em um pool - Azure Batch | Microsoft Docs
description: Aprenda a montar um sistema de arquivos virtual em um pool de lotes.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918998"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Monte um sistema de arquivos virtual em um pool de lotes

O Azure Batch agora suporta a montagem do armazenamento em nuvem ou um sistema de arquivos externo sustanos no Windows ou nos nós de computação Linux em seus pools Batch. Quando um nó de computação se junta a um pool, o sistema de arquivos virtual é montado e tratado como uma unidade local nesse nó. Você pode montar sistemas de arquivos como Arquivos Azure, Armazenamento Azure Blob, Sistema de Arquivos de Rede (NFS), incluindo um [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)ou CIFS (Common Internet File System).

Neste artigo, você aprenderá como montar um sistema de arquivos virtual em um pool de nodos de computação usando a [Biblioteca de Gerenciamento de Lotes para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> A montagem de um sistema de arquivos virtuais é suportada em pools batch criados em ou após 2019-08-19. Os pools de lotes criados antes de 2019-08-19 não suportam esse recurso.
> 
> As APIs para montagem de sistemas de arquivos em um nó de computação fazem parte da biblioteca [Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Benefícios da montagem em uma piscina

A montagem do sistema de arquivos no pool, em vez de permitir que as tarefas recuperem seus próprios dados de um grande conjunto de dados, torna mais fácil e eficiente para as tarefas acessaros os dados necessários.

Considere um cenário com múltiplas tarefas que requerem acesso a um conjunto comum de dados, como renderizar um filme. Cada tarefa renderiza um ou mais quadros de cada vez a partir dos arquivos de cena. Ao montar uma unidade que contenha os arquivos de cena, é mais fácil para nós computados acessar dados compartilhados. Além disso, o sistema de arquivos subjacente pode ser escolhido e dimensionado independentemente com base no desempenho e escala (throughput e IOPS) exigidos pelo número de nomes de computação que acessam simultaneamente os dados. Por exemplo, um cache de memória distribuído [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) pode ser usado para suportar renderizações em grande escala de filme com milhares de álos de renderização simultâneas, acessando dados de origem que residem no local. Alternativamente, para dados que já residem no armazenamento Blob baseado em nuvem, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) pode ser usado para montar esses dados como um sistema de arquivos local. Blobfuse só está disponível em nós Linux, no entanto, [o Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) fornece um fluxo de trabalho semelhante e está disponível tanto no Windows quanto no Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Monte um sistema de arquivos virtual em um pool  

A montagem de um sistema de arquivos virtual em um pool torna o sistema de arquivos disponível para todos os nós de computação no pool. O sistema de arquivos é configurado quando um nó de computação entra em um pool ou quando o nó é reiniciado ou regravado.

Para montar um sistema de arquivos `MountConfiguration` em uma piscina, crie um objeto. Escolha o objeto que se `AzureBlobFileSystemConfiguration`encaixa `AzureFileShareConfiguration` `NfsMountConfiguration`no `CifsMountConfiguration`seu sistema de arquivos virtual: , , ou .

Todos os objetos de configuração de montagem precisam dos seguintes parâmetros de base. Algumas configurações de montagem têm parâmetros específicos para o sistema de arquivos que está sendo usado, que são discutidos com mais detalhes nos exemplos de código.

- **Nome da conta ou fonte**: Para montar um compartilhamento de arquivos virtuais, você precisa do nome da conta de armazenamento ou de sua fonte.
- **Caminho de montagem relativo ou Fonte**: A localização do sistema de `fsmounts` arquivos montado no nó `AZ_BATCH_NODE_MOUNTS_DIR`de computação, em relação ao diretório padrão acessível no nó via . A localização exata varia de acordo com o sistema operacional usado no nó. Por exemplo, a localização física em um nó `mnt\batch\tasks\fsmounts`Ubuntu é mapeada para `mnt\resources\batch\tasks\fsmounts`, e em um nó CentOS ele é mapeado para .
- **Opções de montagem ou opções blobfuse**: Essas opções descrevem parâmetros específicos para a montagem de um sistema de arquivos.

Uma `MountConfiguration` vez que o objeto é criado, atribua o objeto à `MountConfigurationList` propriedade quando você criar o pool. O sistema de arquivos é montado quando um nó entra em uma piscina ou quando o nó é reiniciado ou recapturado.

Quando o sistema de arquivos é `AZ_BATCH_NODE_MOUNTS_DIR` montado, uma variável de ambiente é criada que aponta para a localização dos sistemas de arquivos montados, bem como arquivos de registro, que são úteis para solução de problemas e depuração. Os arquivos de log são explicados com mais detalhes na seção [Diagnosticar erros de montagem.](#diagnose-mount-errors)  

> [!IMPORTANT]
> O número máximo de sistemas de arquivos montados em um pool é de 10. Consulte [Cotas de serviço em lote e limites](batch-quota-limit.md#other-limits) para detalhes e outros limites.

## <a name="examples"></a>Exemplos

Os exemplos de código a seguir demonstram a montagem de uma variedade de compartilhamentos de arquivos em um pool de nós de computação.

### <a name="azure-files-share"></a>Compartilhamento de arquivos azure

O Azure Files é o sistema padrão de arquivos em nuvem do Azure. Para saber mais sobre como obter qualquer um dos parâmetros na amostra de código de configuração de montagem, consulte [Usar um compartilhamento de arquivos Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Sistema de arquivos Azure Blob

Outra opção é usar o armazenamento Azure Blob via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). A montagem de um `AccountKey` sistema `SasKey` de arquivos blob requer um ou para sua conta de armazenamento. Para obter informações sobre como obter essas chaves, consulte Gerenciar chaves de [acesso de conta de armazenamento](../storage/common/storage-account-keys-manage.md)ou usar assinaturas de acesso compartilhada [(SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Para obter mais informações sobre o uso de blobfuse, consulte o blobfuse [Troubles FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Para obter acesso padrão ao diretório montado blobfuse, execute a tarefa como **administrador**. Blobfuse monta o diretório no espaço do usuário, e na criação da piscina ele é montado como raiz. No Linux todas as tarefas **do Administrador** são raiz. Todas as opções para o módulo FUSE estão descritas na [página de referência FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Além do guia de solução de problemas, os problemas do GitHub no repositório blobfuse são uma maneira útil de verificar os problemas e resoluções atuais do blobfuse. Para obter mais informações, consulte [problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS

Os SISTEMAS de Arquivos de Rede (NFS) também podem ser montados em nós de pool permitindo que sistemas de arquivos tradicionais sejam facilmente acessados pelos álos do Azure Batch. Este pode ser um único servidor NFS implantado na nuvem ou um servidor NFS local acessado em uma rede virtual. Alternativamente, aproveite a solução de cache de memória distribuída [do Avere vFXT,](../avere-vfxt/avere-vfxt-overview.md) que fornece conectividade perfeita ao armazenamento no local, lendo dados sob demanda em seu cache e fornece alto desempenho e escala para os nós computacionais baseados em nuvem.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Sistema comum de arquivos de Internet

Os CIFS (Common Internet File Systems, sistemas comuns de arquivos de internet) também podem ser montados em nós de pool que permitem que sistemas de arquivos tradicionais sejam facilmente acessados pelos nodes do Azure Batch. CIFS é um protocolo de compartilhamento de arquivos que fornece um mecanismo aberto e multiplataforma para solicitar arquivos e serviços de servidor de rede. O CIFS é baseado na versão aprimorada do protocolo SMB (Server Message Block, bloco de mensagens do servidor) da Microsoft para compartilhamento de arquivos de internet e intranet e é usado para montar sistemas de arquivos externos em nomes windows. Para saber mais sobre o SMB, consulte [File Server e SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticar erros de montagem

Se uma configuração de montagem falhar, o nó de computação na piscina falhará e o estado do nó ficará inutilizável. Para diagnosticar uma falha de [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) configuração de montagem, inspecione a propriedade para obter detalhes sobre o erro.

Para obter os arquivos de log para depuração, use [OutputFiles](batch-task-output-files.md) para carregar os `*.log` arquivos. Os `*.log` arquivos contêm informações sobre `AZ_BATCH_NODE_MOUNTS_DIR` a montagem do sistema de arquivos no local. Os arquivos de registro `<type>-<mountDirOrDrive>.log` de montagem têm o formato: para cada montagem. Por exemplo, `cifs` uma montagem em `test` um diretório de montagem `cifs-test.log`chamado terá um arquivo de registro de montagem chamado: .

## <a name="supported-skus"></a>SKUs com suporte

| Publicador | Oferta | SKU | Compartilhamento de arquivos azure | Blobfuse | Montagem NFS | Montagem CIFS |
|---|---|---|---|---|---|---|
| lote | rendering-centos73 | renderização | :heavy_check_mark: <br>Nota: Compatível com o CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canônico | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatível com o CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Nota: Suporta armazenamento A_8 ou 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais detalhes sobre a montagem de um compartilhamento de Arquivos Azure com [Windows](../storage/files/storage-how-to-use-files-windows.md) ou [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Saiba mais sobre o uso e montagem de sistemas de arquivos virtuais [blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Consulte a [visão geral do Sistema de Arquivos de Rede](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) para saber mais sobre o NFS e seus aplicativos.
- Consulte o protocolo SMB da Microsoft e a [visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para saber mais sobre o CIFS.
