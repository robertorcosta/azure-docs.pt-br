---
title: Gerencie os membros do consórcio Azure Blockchain Service - PowerShell
description: Saiba como gerenciar os membros do consórcio Azure Blockchain Service usando o Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505990"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gerencie membros do consórcio no Azure Blockchain Service usando o PowerShell

Você pode usar o PowerShell para gerenciar membros do consórcio blockchain para o seu Azure Blockchain Service. Os membros que possuem privilégios de administrador podem convidar, adicionar, remover e alterar funções para todos os participantes do consórcio blockchain. Os membros que possuem privilégios de usuário podem visualizar todos os participantes do consórcio blockchain e alterar o nome de exibição de seus membros.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um membro blockchain usando o [portal Azure](create-member.md).
* Para obter mais informações sobre consórcios, membros e nós, consulte [o consórcio Azure Blockchain Service](consortium.md).

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Você também pode abrir o Cloud Shell em uma guia de navegador separada indo para [shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote Microsoft.AzureBlockchainService.ConsortiumManagement.PS da Galeria PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Defina a preferência de informações

Você pode obter mais informações ao executar os cmdlets definindo a variável de preferência de informações. Por padrão, *$InformationPreference* é definido como *SilentlyContinue*.

Para obter mais informações verbos as informações dos cmdlets, defina a preferência no PowerShell da seguinte forma:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Estabeleça uma conexão Web3

Para gerenciar os membros do consórcio, estabeleça uma conexão Web3 com o ponto final do seu membro do Blockchain Service. Você pode usar este script para definir variáveis globais para chamar os cmdlets de gerenciamento de consórcio.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua * \<a\> senha* da conta do Membro pela senha da conta de membro que você usou quando criou o membro.

Encontre os outros valores no portal Azure:

1. Faça login no [portal Azure](https://portal.azure.com).
1. Vá para a página **de visão geral** do membro do Serviço Blockchain padrão.

    ![Visão geral do membro](./media/manage-consortium-powershell/member-overview.png)

    Substitua * \<\> a conta do membro* e * \<\> * o endereço RootContract pelos valores do portal.

1. Para o endereço ponto final, selecione **'Nó de transação'** e selecione o **nó de transação padrão**. O nó padrão tem o mesmo nome do membro blockchain.
1. Selecione **strings Connection**.

    ![Cadeias de conexão](./media/manage-consortium-powershell/connection-strings.png)

    Substitua * \<o\> endereço endpoint* pelo valor de **HTTPS (tecla de acesso 1)** ou **HTTPS (tecla De acesso 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Gerencie a rede e os contratos inteligentes

Use a rede e os cmdlets de contrato inteligente para estabelecer uma conexão com os contratos inteligentes do endpoint blockchain responsáveis pela gestão do consórcio.

### <a name="import-consortiummanagementcontracts"></a>Contratos de gestão de empresas de importação-consórcio

Use este cmdlet para se conectar aos contratos inteligentes da gestão do consórcio. Esses contratos são usados para gerenciar e fazer cumprir membros dentro do consórcio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| RootContractAddress | Endereço de contrato raiz dos contratos inteligentes de gestão do consórcio | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importação-Web3Conta

Use este cmdlet para criar um objeto para manter as informações para a conta de gerenciamento de um nó remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Endereço de conta gerenciada | Endereço da conta do membro blockchain | Sim |
| ManagedAccountPassword | Senha do endereço da conta | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Conexão New-Web3

Use este cmdlet para estabelecer uma conexão com o ponto final rpc de um nó de transação.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Endereço de ponto final de membro blockchain | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gerencie os membros do consórcio

Use cmdlets de gestão de membros do consórcio para gerenciar os membros dentro do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Use este cmdlet para obter detalhes do membro ou listar os membros do consórcio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Nome | O nome do membro do Serviço Blockchain sobre o que você deseja recuperar detalhes. Quando um nome é inserido, ele retorna os detalhes do membro. Quando um nome é omitido, ele retorna uma lista de todos os membros do consórcio. | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir a variável $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Saída de exemplo

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Use este cmdlet para remover um membro blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Nome | Nome do membro para remover | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Conta | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Use este cmdlet para definir atributos de membros blockchain, incluindo o nome de exibição e a função de consórcio.

Os administradores do consórcio podem definir **DisplayName** e **Role** para todos os membros. Um membro do consórcio com a função de usuário pode alterar apenas o nome de exibição de seu próprio membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Nome | Nome do membro blockchain | Sim |
| DisplayName | Novo nome de exibição | Não |
| Endereço da conta | Endereço da conta | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Conta | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client |  Objeto Web3Client obtido a partir de New-Web3Connection| Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gerencie os convites dos membros do consórcio

Use os cmdlets de gestão de convites dos membros do consórcio para gerenciar os convites dos membros do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="new-blockchainmemberinvitation"></a>Novo-BlockchainMemberInvitation

Use este cmdlet para convidar novos membros para o consórcio.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure do membro para convidar | Sim |
| Função | O papel do consórcio. Os valores podem ser ADMIN ou USER. A ADMIN é o papel de administrador do consórcio. USER é o papel de membro do consórcio. | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Conta | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Use este cmdlet para recuperar ou listar o status de convite de um membro do consórcio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | O ID de assinatura do Azure do membro para convidar. Se o ID de assinatura for fornecido, ele retorna os detalhes do convite do ID da assinatura. Se o ID de assinatura for omitido, ele retorna uma lista de todos os convites de membros. | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir a variável $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Saída de exemplo

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Use este cmdlet para revogar o convite de um membro do consórcio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure do membro para revogar | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Conta | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Use este cmdlet para definir o **Papel** para um convite existente. Apenas administradores de consórcios podem mudar os convites.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure do membro para convidar | Sim |
| Função | Novo papel de consórcio para convite. Os valores podem ser **USER** ou **ADMIN**. | Sim |
| Membros |  Objeto de membros obtido a partir de Contratos de Gestão de Importação-Consórcio | Sim |
| Web3Conta | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma conexão Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre consórcios, membros e nós, consulte [o consórcio Azure Blockchain Service](consortium.md)
