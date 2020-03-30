---
title: Conecte-se a uma conta do Azure Cosmos com o Azure Private Link
description: Saiba como acessar com segurança a conta do Azure Cosmos a partir de uma VM criando um Ponto Final Privado.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252594"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Conecte-se privadamente a uma conta do Azure Cosmos usando o Azure Private Link

O Azure Private Endpoint é o bloco fundamental de construção do Private Link no Azure. Ele permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem privadamente com os recursos do Private Link.

Neste artigo, você aprenderá como criar uma VM em uma rede virtual Azure e uma conta do Azure Cosmos com um Ponto Final Privado usando o portal Azure. Em seguida, você pode acessar com segurança a conta do Azure Cosmos a partir da VM.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no [portal Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Criar uma máquina virtual

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso Private Link (uma conta do Azure Cosmos neste exemplo).

Nesta seção, você precisará substituir os seguintes parâmetros nas etapas com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroup|
| **\<>de nome de rede virtual** | myVirtualNetwork         |
| **\<>de nome da região**          | Centro-Oeste dos EUA     |
| **\<IPv4-endereço-espaço>**   | 10.1.0.0\16          |
| **\<>de nomes de sub-rede**          | mySubnet        |
| **\<>de alcance de endereço de sub-rede** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo da tela no portal Azure, selecione Criar uma**máquina Virtual****de computação** >  **de recursos** > .

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DO ADMINISTRADOR** |  |
    | Nome de Usuário | Digite um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Redigite a senha. |
    | **REGRAS DA PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **A seguir: Discos**.

1. Em **Criar uma máquina virtual - Discos**, deixe os padrões e selecione **Next: Networking**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão .  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)**.|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**.

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Crie [uma conta API AQL Do Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Para simplificar, você pode criar a conta do Azure Cosmos na mesma região que os outros recursos (que é "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Crie um ponto final privado para sua conta do Azure Cosmos

Crie um Link Privado para sua conta do Azure Cosmos conforme descrito no Criar um Link Privado usando a seção [portal Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) do artigo vinculado.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *.rdp* baixado.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Você pode precisar selecionar **Mais opções** > **Use uma conta diferente,** para especificar as credenciais inseridas quando criou a VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Acesse a conta do Azure Cosmos em particular da VM

Nesta seção, você se conectará privadamente à conta Do Azure Cosmos usando o Private Endpoint. 

1. Para incluir o endereço IP e o mapeamento de DNS, `c:\Windows\System32\Drivers\etc\hosts` faça login no *myVM*da máquina virtual, abra o arquivo e inclua as informações de DNS da etapa anterior no seguinte formato:

   [Endereço IP privado] [Ponto final da conta].documents.azure.com

   **Exemplo:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Na área de trabalho remota do *myVM,* instale o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selecione **Contas Do Cosmos DB (Visualização)** com o clique com o botão direito do mouse.

1. Selecione **Conectar ao Cosmos DB**.

1. Selecione **API**.

1. Digite a seqüência de conexões colando as informações copiadas anteriormente.

1. Selecione **Avançar**.

1. Selecione **Conectar**.

1. Navegue pelos bancos de dados e contêineres do Azure Cosmos da *mycosmosaccount*.

1. (Opcionalmente) adicionar novos itens ao *mycosmosaccount*.

1. Feche a conexão remota da área de trabalho para *myVM*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar o Private Endpoint, a conta Do Azure Cosmos e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Digite *myResourceGroup* para **digitar o nome do grupo de recursos e** **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma VM em uma rede virtual, uma conta do Azure Cosmos e um Private Endpoint. Você se conectou à VM a partir da internet e comunicou com segurança à conta do Azure Cosmos usando o Private Link.

* Para saber mais sobre o Private Endpoint, veja [O que é o Ponto Final Privado do Azure?](private-endpoint-overview.md).

* Para saber mais sobre a limitação do Private Endpoint ao usar com o Azure Cosmos DB, consulte o artigo [Azure Private Link com o Azure Cosmos DB.](../cosmos-db/how-to-configure-private-endpoints.md)
