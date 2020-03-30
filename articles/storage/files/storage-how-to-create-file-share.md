---
title: Criar um compartilhamento de arquivos do Azure
titleSuffix: Azure Files
description: Como criar um compartilhamento de arquivos do Azure usando o portal Azure, powershell ou o Cli do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596854"
---
# <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Para criar um compartilhamento de arquivos do Azure, você precisa responder a três perguntas sobre como você irá usá-lo:

- **Quais são os requisitos de desempenho para o compartilhamento de arquivos Do Azure?**  
    O Azure Files oferece compartilhamentos de arquivos padrão, que estão hospedados em hardware baseado em disco rígido (baseado em HDD) e compartilhamentos de arquivos premium, que estão hospedados em hardware baseado em disco de estado sólido (baseado em SSD).

- **De que tamanho você precisa?**  
    Os compartilhamentos de arquivos padrão podem abranger até 100 TiB, no entanto, esse recurso não está ativado por padrão; se você precisar de um compartilhamento de arquivos maior que 5 TiB, você precisará habilitar o recurso de compartilhamento de arquivos grande para sua conta de armazenamento. As ações de arquivos premium podem se estender até 100 TiB sem qualquer configuração especial, no entanto, as ações de arquivos premium são provisionadas, em vez de pagar como ações de arquivo padrão. Isso significa que o provisionamento de um compartilhamento de arquivos muito maior do que o necessário aumentará o custo total de armazenamento.

- **Quais são seus requisitos de redundância para o compartilhamento de arquivos do Azure?**  
    As ações de arquivo padrão oferecem o armazenamento localmente redundante (LRS), redundante de zona (ZRS), geo-redundante (GRS) ou armazenamento geo-zona-redundante (GZRS), no entanto, o recurso de compartilhamento de arquivos grande só é suportado em compartilhamentos de arquivos redundantes localmente redundantes e redundantes de zona. As ações de arquivos premium não suportam qualquer forma de geo-redundância.

    Os compartilhamentos de arquivos premium estão disponíveis com redundância local na maioria das regiões que oferecem contas de armazenamento e com redundância de região em um subconjunto menor de regiões. Para saber se as ações de arquivos premium estão disponíveis atualmente em sua região, consulte os [produtos disponíveis pela](https://azure.microsoft.com/global-infrastructure/services/?products=storage) página da região para o Azure. Para obter informações sobre regiões que suportam ZRS, consulte [a redundância de armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para obter mais informações sobre essas três opções, consulte [Planejamento para uma implantação de Arquivos Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo pressupõe que você já criou uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Se você pretende usar o Azure PowerShell, [instale a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Se você pretende usar o Azure CLI, [instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Os compartilhamentos de arquivos do Azure são implantados em contas de *armazenamento,* que são objetos de alto nível que representam um pool compartilhado de armazenamento. Este pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivos. 

O Azure suporta vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas existem dois tipos principais de contas de armazenamento para arquivos Do Azure. Qual tipo de conta de armazenamento que você precisa criar depende se você deseja criar um compartilhamento padrão de arquivos ou um compartilhamento de arquivo premium: 

- **Contas de armazenamento versão 2 (GPv2): As contas**de armazenamento GPv2 permitem que você implante compartilhamentos de arquivos Azure em hardware baseado em disco padrão/rígido (baseado em HDD). Além de armazenar compartilhamentos de arquivos Do Zure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como recipientes blob, filas ou tabelas. 

- **Contas de armazenamento de armazenamento de arquivos**: As contas de armazenamento de armazenamento de arquivos permitem que você implante compartilhamentos de arquivos do Azure em hardware baseado em disco premium/sólido (baseado em SSD). As contas de armazenamento de arquivos só podem ser usadas para armazenar compartilhamentos de arquivos do Azure; nenhum outro recurso de armazenamento (recipientes blob, filas, tabelas, etc.) pode ser implantado em uma conta de armazenamento de arquivos.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento através do portal Azure, selecione **+ Crie um recurso** no painel de controle. Na janela de pesquisa resultante do Azure Marketplace, procure a **conta de armazenamento** e selecione o resultado da pesquisa resultante. Isso levará a uma página de visão geral das contas de armazenamento; selecione **Criar** para prosseguir com o assistente de criação de conta de armazenamento.

![Uma captura de tela da opção de criação rápida da conta de armazenamento em um navegador](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>A seção Básico
A primeira seção a ser concluída para criar uma conta de armazenamento é rotulada **Basics**. Isso contém todos os campos necessários para criar uma conta de armazenamento. Para criar uma conta de armazenamento GPv2, certifique-se de que o botão **de** rádio Performance está configurado como *Padrão* e que a lista de parada do **tipo Conta** está selecionada para *StorageV2 (objetivo geral v2).*

![Uma captura de tela do botão de rádio Performance com padrão selecionado e tipo conta com StorageV2 selecionado](media/storage-how-to-create-file-share/create-storage-account-1.png)

Para criar uma conta de armazenamento de arquivos, certifique-se de que o botão de rádio **Performance** está definido como *Premium* e que a lista de parada do **tipo Conta** está selecionada para *FileStorage*.

![Uma captura de tela do botão de rádio Performance com premium selecionado e tipo conta com filestorage selecionado](media/storage-how-to-create-file-share/create-storage-account-2.png)

Os outros campos básicos são independentes da escolha da conta de armazenamento:
- **Assinatura**: A assinatura da conta de armazenamento a ser implantada. 
- **Grupo de recursos**: O grupo de recursos para que a conta de armazenamento seja implantada. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente.
- **Nome da conta**de armazenamento : O nome do recurso da conta de armazenamento a ser criado. Este nome deve ser globalmente único, mas de outra forma pode qualquer nome que você desejar. O nome da conta de armazenamento será usado como nome do servidor quando você montar um compartilhamento de arquivo Do Zure via SMB.
- **Localização**: A região para a conta de armazenamento a ser implantada. Esta pode ser a região associada ao grupo de recursos, ou qualquer outra região disponível.
- **Replicação**: Embora esta seja a replicação rotulada, este campo realmente significa **redundância;** este é o nível de redundância desejado: redundância local (LRS), redundância de zona (ZRS), geo-redundância (GRS) e geo-zona-redundância. Esta lista de baixa também contém a geo-redundância de acesso de leitura (RA-GRS) e a redundância de zona geográfica de acesso de leitura (RA-GZRS), que não se aplicam a compartilhamentos de arquivos Do Zure; qualquer compartilhamento de arquivo criado em uma conta de armazenamento com esses selecionados será, na verdade, geo-redundante ou geo-zona-redundante, respectivamente. Dependendo da região ou do tipo de conta de armazenamento selecionada, algumas opções de redundância podem não ser permitidas.
- **Nível de acesso**: Este campo não se aplica aos Arquivos Azure, portanto, você pode escolher qualquer um dos botões de rádio.

#### <a name="the-networking-blade"></a>A lâmina de rede
A seção de rede permite configurar opções de rede. Essas configurações são opcionais para a criação da conta de armazenamento e podem ser configuradas posteriormente, se desejarem. Para obter mais informações sobre essas opções, consulte [considerações de rede do Azure Files](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>A lâmina avançada
A seção avançada contém várias configurações importantes para compartilhamentos de arquivos Do Azure:

- **Transferência segura necessária**: Este campo indica se a conta de armazenamento requer criptografia em trânsito para comunicação à conta de armazenamento. Recomendamos que isso seja deixado ativado, no entanto, se você precisar de suporte smb 2.1, você deve desativar isso. Recomendamos que, se você desativar a criptografia, você restrinja o acesso da sua conta de armazenamento a uma rede virtual com pontos finais de serviço e/ou pontos finais privados.
- **Grandes compartilhamentos de arquivos**: Este campo permite a conta de armazenamento de compartilhamentos de arquivos que abrangem até 100 TiB. A ativação desse recurso limitará sua conta de armazenamento apenas a opções de armazenamento redundantes e redundantes localmente. Uma vez que uma conta de armazenamento GPv2 tenha sido habilitada para grandes compartilhamentos de arquivos, você não poderá desativar o grande recurso de compartilhamento de arquivos. As contas de armazenamento de armazenamento de arquivos de arquivo (contas de armazenamento para compartilhamentos de arquivos premium) não têm essa opção, pois todas as partes de arquivos premium podem escalar até 100 TiB. 

![Uma captura de tela das configurações avançadas importantes que se aplicam aos Arquivos Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

As outras configurações disponíveis na guia avançada (blob soft-delete, espaço hierárquico para armazenamento Azure Data Lake gen 2 e NFSv3 para armazenamento blob) não se aplicam aos Arquivos Azure.

#### <a name="tags"></a>Marcas
As tags são pares de nomes/valores que permitem categorizar recursos e exibir faturamento consolidado aplicando a mesma tag a vários recursos e grupos de recursos. Estes são opcionais e podem ser aplicados após a criação da conta de armazenamento.

#### <a name="review--create"></a>Examinar + criar
O passo final para criar a conta de armazenamento é selecionar o botão **Criar** na guia **''Criar+'.** Este botão não estará disponível se todos os campos necessários para uma conta de armazenamento não forem preenchidos.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Para criar uma conta de armazenamento usando `New-AzStorageAccount` o PowerShell, usaremos o cmdlet. Este cmdlet tem muitas opções; apenas as opções necessárias são mostradas. Para saber mais sobre opções avançadas, consulte a [ `New-AzStorageAccount` documentação do cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Para simplificar a criação da conta de armazenamento e o compartilhamento subsequente de arquivos, armazenaremos vários parâmetros em variáveis. Você pode substituir o conteúdo da variável por quaisquer valores que desejar, no entanto, observe que o nome da conta de armazenamento deve ser globalmente único.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos Padrão Azure, usaremos o seguinte comando. O `-SkuName` parâmetro diz respeito ao tipo de redundância desejada; se você desejar uma conta de armazenamento geo-redundante ou `-EnableLargeFileShare` geo-zona-redundante, você também deve remover o parâmetro.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos Premium Azure, usaremos o seguinte comando. Observe que `-SkuName` o parâmetro mudou `Premium` para incluir ambos e o nível`LRS`de redundância desejado de localmente redundante ( ). O `-Kind` parâmetro `FileStorage` é, `StorageV2` em vez disso, porque os compartilhamentos de arquivos premium devem ser criados em uma conta de armazenamento de arquivos em vez de uma conta de armazenamento GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Para criar uma conta de armazenamento usando o Azure CLI, usaremos o comando az storage create. Este comando tem muitas opções; apenas as opções necessárias são mostradas. Para saber mais sobre as opções avançadas, consulte a [ `az storage account create` documentação](/cli/azure/storage/account)do comando .

Para simplificar a criação da conta de armazenamento e o compartilhamento subsequente de arquivos, armazenaremos vários parâmetros em variáveis. Você pode substituir o conteúdo da variável por quaisquer valores que desejar, no entanto, observe que o nome da conta de armazenamento deve ser globalmente único.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos Padrão Azure, usaremos o seguinte comando. O `--sku` parâmetro diz respeito ao tipo de redundância desejada; se você desejar uma conta de armazenamento geo-redundante ou `--enable-large-file-share` geo-zona-redundante, você também deve remover o parâmetro.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos Premium Azure, usaremos o seguinte comando. Observe que `--sku` o parâmetro mudou `Premium` para incluir ambos e o nível`LRS`de redundância desejado de localmente redundante ( ). O `--kind` parâmetro `FileStorage` é, `StorageV2` em vez disso, porque os compartilhamentos de arquivos premium devem ser criados em uma conta de armazenamento de arquivos em vez de uma conta de armazenamento GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Criar o compartilhamento de arquivos
Depois de criar sua conta de armazenamento, tudo o que resta é criar seu compartilhamento de arquivos. Este processo é principalmente o mesmo, independentemente de você estar usando um compartilhamento de arquivo premium ou um compartilhamento de arquivo padrão. A principal diferença é a **cota** e o que ela representa.

Para compartilhamentos de arquivos padrão, é um limite superior do compartilhamento de arquivos do Azure, além do qual os usuários finais não podem ir. O objetivo principal da cota para uma partilha de arquivos padrão é orçamentário: "Eu não quero que essa parte de arquivo cresça além deste ponto". Se uma cota não for especificada, o compartilhamento padrão de arquivos pode abranger até 100 TiB (ou 5 TiB se a propriedade de compartilhamentos de arquivos grandes não for definida para uma conta de armazenamento).

Para ações de arquivo premium, a cota é sobrecarregada para significar **tamanho provisionado**. O tamanho provisionado é o valor pelo que você será cobrado, independentemente do uso real. Quando você provisão um compartilhamento de arquivo premium, você deseja considerar dois fatores: 1) o crescimento futuro da ação a partir de uma perspectiva de utilização de espaço e 2) o IOPS necessário para sua carga de trabalho. Cada GiB provisionado dá direito a um IOPS reservado e estourado adicional. Para obter mais informações sobre como planejar um compartilhamento de arquivos premium, consulte [provisionamento de ações de arquivos premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Se você acabou de criar sua conta de armazenamento, você pode navegar até ela a partir da tela de implantação selecionando **Go para recurso**. Se você já criou a conta de armazenamento anteriormente, você pode navegar até ela através do grupo de recursos que a contém. Uma vez na conta de armazenamento, selecione os **compartilhamentos de arquivo rotulados** de ladrilho (você também pode navegar para **arquivos compartilha** através da tabela de conteúdo da conta de armazenamento).

![Uma captura de tela do arquivo compartilha o azulejo](media/storage-how-to-create-file-share/create-file-share-1.png)

Na listagem de compartilhamento de arquivos, você deve ver quaisquer compartilhamentos de arquivos que você tenha criado anteriormente nesta conta de armazenamento; uma tabela vazia se nenhuma parte de arquivo foi criada ainda. Selecione **+ Compartilhamento de arquivos** para criar um novo compartilhamento de arquivos.

A nova lâmina de compartilhamento de arquivos deve aparecer na tela. Complete os campos na nova lâmina de compartilhamento de arquivos para criar um compartilhamento de arquivos:

- **Nome**: o nome do compartilhamento de arquivos a ser criado.
- **Cota**: A cota da ação de arquivo para ações de arquivo padrão; o tamanho provisionado da parte de arquivo para ações de arquivo premium.

Selecione **Criar** para finalizar a criação do novo compartilhamento. Observe que se sua conta de armazenamento estiver em uma rede virtual, você não será capaz de criar com sucesso um compartilhamento de arquivos Do Zure, a menos que seu cliente também esteja na rede virtual. Você também pode contornar essa limitação pontual usando o cmdlet Do Azure PowerShell. `New-AzRmStorageShare`

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Você pode criar o compartilhamento de [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) arquivos Azure com o cmdlet. Os seguintes comandos do PowerShell `$resourceGroupName` assumem que você definiu as variáveis e, `$storageAccountName` conforme definido acima, na criação de uma conta de armazenamento com a seção Azure PowerShell. 

> [!Important]  
> Para ações de `-QuotaGiB` arquivo premium, o parâmetro refere-se ao tamanho provisionado da parte do arquivo. O tamanho provisionado da parte do arquivo é o valor que você será cobrado, independentemente do uso. As ações de arquivo padrão são cobradas com base no uso e não no tamanho provisionado.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre a nomeação de compartilhamentos e arquivos de arquivos, consulte [Nomeação e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Antes de criarmos um compartilhamento de arquivos do Azure com o Azure CLI, você deve obter uma chave de conta de armazenamento para autorizar a operação de criação de compartilhamento de arquivos. Isso pode ser [`az storage account keys list`](/cli/azure/storage/account/keys) feito com o comando:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Depois de ter a chave da conta de armazenamento, [`az storage share create`](/cli/azure/storage/share) você pode criar o compartilhamento de arquivos do Azure com o comando. 

> [!Important]  
> Para ações de `--quota` arquivo premium, o parâmetro refere-se ao tamanho provisionado da parte do arquivo. O tamanho provisionado da parte do arquivo é o valor que você será cobrado, independentemente do uso. As ações de arquivo padrão são cobradas com base no uso e não no tamanho provisionado.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Este comando falhará se a conta de armazenamento estiver contida em uma rede virtual e o computador de onde você está invocando este comando não faz parte da rede virtual. Você pode contornar essa limitação pontual usando o cmdlet Azure PowerShell `New-AzRmStorageShare` como descrito acima, ou executando o Azure CLI a partir de um computador que faz parte da rede virtual, inclusive através de uma conexão VPN.

---

> [!Note]  
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre a nomeação de compartilhamentos e arquivos de arquivos, consulte [Nomeação e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Próximas etapas
- [Planeje uma implantação de arquivos azure](storage-files-planning.md) ou [plano para uma implantação do Azure File Sync](storage-sync-files-planning.md). 
- [Visão geral do networking](storage-files-networking-overview.md).
- Conecte e monte um compartilhamento de arquivos no [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)e [Linux.](storage-how-to-use-files-linux.md)