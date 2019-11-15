---
title: Manter arquivos em Azure Cloud Shell | Microsoft Docs
description: Passo a passo de como o Azure Cloud Shell persiste arquivos.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: ee68400d000ca823816c8efc6bcbc224d1388832
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082989"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Persistir arquivos no Azure Cloud Shell
O Cloud Shell utiliza o armazenamento dos Arquivos do Azure para persistir os arquivos entre as sessões. No primeiro início, o Cloud Shell solicita a associação de um compartilhamento de arquivos novo ou existente para persistir arquivos entre as sessões.

> [!NOTE]
> O Bash e o PowerShell compartilham o mesmo compartilhamento de arquivos. Somente um compartilhamento de arquivos pode ser associado á montagem automática no Cloud Shell.

## <a name="create-new-storage"></a>Criar novo armazenamento

Ao usar as configurações básicas e seleciona apenas uma assinatura, o Cloud Shell criará três recursos em seu nome na região com suporte mais próxima de você:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Compartilhamento de arquivos:`cs-<user>-<domain>-com-<uniqueGuid>`

![A configuração da assinatura](media/persisting-shell-storage/basic-storage.png)

O compartilhamento de arquivos é montado como `clouddrive` no seu diretório `$Home`. Isso é uma ação única e o compartilhamento de arquivos será montado automaticamente nas sessões subsequentes. 

> [!NOTE]
> Por questões de segurança, cada usuário deve provisionar a própria conta de armazenamento.  No caso do RBAC (controle de acesso baseado em função), os usuários devem ter acesso de colaborador ou superior, no nível da conta de armazenamento.

O compartilhamento de arquivos também contém uma imagem de 5 GB criada para você e que automaticamente mantém os dados em seu diretório `$Home`. Isso se aplica para Bash e PowerShell.

## <a name="use-existing-resources"></a>Usar recursos existentes

Usando a opção avançada, você pode associar recursos existentes. Ao selecionar uma região do Cloud Shell, você deve selecionar uma conta de armazenamento de apoio co-localizada na mesma região. Por exemplo, se sua região atribuída for Oeste dos EUA, você deve associar um compartilhamento de arquivos que reside no Oeste dos EUA também.

Quando aparecer o prompt de instalação de armazenamento, selecione **Mostrar configurações avançadas** para exibir opções adicionais. As opções de armazenamento preenchidas são filtradas para contas de LRS (armazenamento com redundância local), GRS (armazenamento com redundância geográfica) e ZRS (armazenamento com redundância de zona). 

> [!NOTE]
> Contas de armazenamento usando GRS ou ZRS são recomendadas para obter resiliência adicional para o compartilhamento de arquivos de backup. Qual tipo de redundância depende de suas metas e a preferência de preço. [Saiba mais sobre opções de replicação para contas do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![A configuração do grupo de recursos](media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
As contas de armazenamento do Azure associadas devem residir na mesma região que o computador do Cloud Shell em que você estiver montando. Para localizar sua região atual, você poderá executar `env` em Bash e localizar a variável `ACC_LOCATION`. Os compartilhamentos de arquivos recebem uma imagem de 5 GB criada para você manter o diretório `$Home`.

Há computadores do Cloud Shell nas regiões a seguir:

|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Índia Central, Sudeste Asiático|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento criadas no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se você deseja impedir que os usuários criem contas de armazenamento no Cloud Shell, crie uma [Política de recursos do Azure para marcas](../azure-policy/json-samples.md) que seja disparada por essa marca específica.



## <a name="how-cloud-shell-storage-works"></a>Como funciona o armazenamento do Cloud Shell 
O Cloud Shell persiste arquivos usando os seguintes métodos: 
* Criando uma imagem de disco do seu diretório `$Home` para persistir todo o conteúdo do diretório. A imagem de disco é salva no compartilhamento de arquivos especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e sincroniza as alterações automaticamente. 
* Montando o compartilhamento de arquivos especificado como `clouddrive` no diretório `$Home` para que haja interação direta com o compartilhamento de arquivos. `/Home/<User>/clouddrive` é mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os arquivos em seu diretório `$Home` como chaves SSH são persistidos em sua imagem de disco do usuário, que é armazenada no compartilhamento de arquivos montado. Aplique as práticas recomendadas ao persistir informações em seu diretório `$Home` e no compartilhamento de arquivos montado.

## <a name="clouddrive-commands"></a>comandos CloudDrive

### <a name="use-the-clouddrive-command"></a>Use o comando `clouddrive`
No Cloud Shell, você pode executar um comando chamado `clouddrive`, que permite atualizar manualmente o compartilhamento de arquivos que é montado no Cloud Shell.
![Usando o comando “clouddrive”](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista `clouddrive`
Para descobrir qual compartilhamento de arquivos está montado como `clouddrive`, execute o comando `df`. 

O caminho de arquivo para a unidade de nuvem mostra o nome da conta de armazenamento e o compartilhamento de arquivos na URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Montar um novo clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
Você pode atualizar o compartilhamento de arquivos associado ao Cloud Shell usando o comando `clouddrive mount`.

Se estiver montando um compartilhamento de arquivos existente, as contas de armazenamento deverão estar localizadas na sua região selecionada do Cloud Shell. Recupere o local executando `env` e verificando o `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>O comando `clouddrive mount`

> [!NOTE]
> Se você estiver montando um novo compartilhamento de arquivo, uma nova imagem de usuário será criada para o diretório `$Home`. A imagem `$Home` anterior é mantida em seu compartilhamento de arquivo anterior.

Execute o comando `clouddrive mount` com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para exibir mais detalhes, execute `clouddrive mount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmontar clouddrive
Você pode desmontar um compartilhamento de arquivos que esteja montado no Cloud Shell a qualquer momento. Como o Cloud Shell requer o uso de compartilhamento de arquivos montado, será solicitado que você crie e monte outro compartilhamento de arquivos na sessão seguinte.

1. Execute `clouddrive unmount`.
2. Reconheça e confirme os prompts.

Seu compartilhamento de arquivos continuará existindo se você não o excluir manualmente. O Cloud Shell deixará de procurar por esse compartilhamento de arquivos em sessões subsequentes. Para exibir mais detalhes, execute `clouddrive unmount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora a execução desse comando não exclua todos os recursos, a exclusão manual de um grupo de recursos, de uma conta de armazenamento, ou de um compartilhamento de arquivos mapeado para o Cloud Shell apaga a imagem do disco `$Home` e todos os arquivos em seu compartilhamento de arquivos. Essa ação não pode ser desfeita.
## <a name="powershell-specific-commands"></a>Comandos específicos do PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Compartilhamentos de Arquivos do Azure
O cmdlet `Get-CloudDrive` recupera as informações de compartilhamento de arquivos do Azure montadas no momento por `clouddrive` no Cloud Shell. <br>
![Executando Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar o `clouddrive`
Você pode desmontar um compartilhamento de arquivos do Azure que esteja montado no Cloud Shell a qualquer momento. Se o compartilhamento de arquivos do Azure tiver sido removido, será solicitado que você crie e monte um novo compartilhamento de arquivos do Azure na sessão seguinte.

O cmdlet `Dismount-CloudDrive` desmonta um compartilhamento de arquivos do Azure da conta de armazenamento atual. Desmontar `clouddrive` encerra a sessão atual. O usuário precisará criar e montar um novo compartilhamento de arquivos do Azure durante a próxima sessão.
![Executando Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Observação: se você precisar definir uma função em um arquivo e chamá-la dos cmdlets do PowerShell, o operador de ponto deverá ser incluído. Por exemplo: . .\MyFunctions.ps1

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md) <br>
[Saiba mais sobre armazenamento de Arquivos do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
