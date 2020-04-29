---
title: Como montar Armazenamento de Blobs do Azure como um sistema de arquivos no Linux | Microsoft Docs
description: Montar um contêiner de Armazenamento de Blobs do Azure com FUSE no Linux
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061421"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o Armazenamento de Blobs como um sistema de arquivos com blobfuse

## <a name="overview"></a>Visão geral
O [Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para o Armazenamento de Blob do Azure. O blobfuse permite que você acesse os dados de blob de blocos existentes em sua conta de armazenamento por meio do sistema de arquivos Linux. Blobfuse usa o esquema de diretório virtual com a barra "/" como um delimitador.  

Este guia mostra como usar o blobfuse e montar um contêiner de Armazenamento de Blobs no Linux e acessar dados. Para saber mais sobre blobfuse, leia os detalhes no [repositório do blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> O blobfuse não garante 100% de conformidade com POSIX, pois simplesmente move solicitações em [APIs REST de Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de renomeação são atômicas no POSIX, mas não em blobfuse.
> Para obter uma lista completa de diferenças entre um sistema de arquivos nativo e blobfuse, visite o [repositório de código-fonte do blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar o blobfuse no Linux
Os binários do Blobfuse estão disponíveis em [repositórios de software da Microsoft para Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) para distribuições Ubuntu e RHEL. Para instalar o blobfuse nessas distribuições, configure um dos repositórios da lista. Você também pode criar os binários a partir do código-fonte seguindo as [etapas de instalação do Armazenamento do Microsoft Azure](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source), se não houver binários disponíveis para sua distribuição.

O Blobfuse dá suporte à instalação no Ubuntu 14, 4, 16, 4 e 18, 4. Execute este comando para garantir que uma dessas versões seja implementada:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configurar o repositório de pacotes da Microsoft
Configure o [Repositório de Pacotes do Linux para Produtos da Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Como exemplo, em uma distribuição Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da forma similar, altere a URL para `.../rhel/7/...` a fim de apontar para uma distribuição Enterprise Linux 7.

Outro exemplo em uma distribuição Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere a `.../ubuntu/16.04/...` URL `.../ubuntu/18.04/...` para ou para fazer referência a outra versão do Ubuntu.

### <a name="install-blobfuse"></a>Instalar blobfuse

Em uma distribuição do Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Em uma distribuição Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para a montagem
O blobfuse fornece um desempenho nativo, exigindo um caminho temporário no sistema de arquivos para armazenar em buffer e cache todos os arquivos abertos. Para esse caminho temporário, escolha o disco de alto desempenho ou use um ramdisk para obter melhor desempenho. 

> [!NOTE]
> O blobfuse armazena todos os conteúdos do arquivo aberto no caminho temporário. Certifique-se de haver espaço suficiente para acomodar todos os arquivos abertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Use um ramdisk para o caminho temporário
O exemplo a seguir cria um ramdisk de 16 GB e um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Esse ramdisk permite que o blobfuse abra arquivos com até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Usar um SSD como um caminho temporário
No Azure, é possível usar os discos efêmeros (SSD) disponíveis em suas VMs para fornecer um buffer de baixa latência para blobfuse. Nas distribuições Ubuntu, esse disco efêmero é montado em '/mnt'. Nas distribuições Red Hat e CentOS, o disco é montado em '/mnt/resource/'.

Certifique-se de que seu usuário tenha acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar as credenciais da conta de armazenamento
O Blobfuse exige que suas credenciais sejam armazenadas em um arquivo de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
O `accountName` é o prefixo para sua conta de armazenamento, não a URL completa.

Crie este arquivo usando:

```
touch ~/fuse_connection.cfg
```

Depois de criar e editar esse arquivo, certifique-se de restringir o acesso para que nenhum outro usuário possa lê-lo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se você criou o arquivo de configuração no Windows, certifique-se de executar `dos2unix` para higienizar e converter o arquivo para o formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Criar um diretório vazio para montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa de opções de montagem, verifique o [repositório do blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar o blobfuse, execute o comando a seguir com seu usuário. Esse comando monta o contêiner especificado em '/path/to/fuse_connection.cfg' no local '/mycontainer'.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Agora você deverá ter acesso aos blobs de blocos através das APIs do sistema de arquivos regulares. O usuário que monta o diretório é a única pessoa que pode acessá-lo, por padrão, o que garante o acesso. Para permitir acesso a todos os usuários, é possível montar através da opção ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Próximas etapas

* [Home page do blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Relatório de problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

