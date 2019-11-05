---
title: Conectar-se a uma conta do Azure cosmos com o link privado do Azure
description: Saiba como acessar com segurança a conta do Azure Cosmos de uma VM criando um ponto de extremidade privado.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sngun
ms.openlocfilehash: 80a0a3440a7fb5d056d1d76fb9d82931721b6e16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514206"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Conectar-se de forma privada a uma conta do Azure Cosmos usando o link privado do Azure

O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste artigo, você aprenderá a criar uma VM em uma rede virtual do Azure e uma conta do Azure cosmos com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta do Azure Cosmos da VM.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Criar uma máquina virtual

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta do Azure Cosmos neste exemplo).

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Assinatura | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Localização | Selecione **WestCentralUS**.|
    | Sub-rede – Nome | Insira *mysubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||

1. Deixe o restante com os valores padrão e selecione **Criar**.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Insira a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **Avançar: discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão.  |
    | Espaço de endereço | Deixe o **10.1.0.0/24**padrão.|
    | Sub-rede | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Crie uma [conta da API do SQL do Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Para simplificar, você pode criar a conta do Azure Cosmos na mesma região que os outros recursos (que é "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Criar um ponto de extremidade privado para sua conta do Azure Cosmos

Crie um link privado para sua conta do Azure Cosmos, conforme descrito na seção [criar um link privado usando a Portal do Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-link-using-the-azure-portal) do artigo vinculado.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *.rdp* baixado.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Acessar a conta do Azure Cosmos de forma privada da VM

Nesta seção, você se conectará de forma privada à conta do Azure Cosmos usando o ponto de extremidade privado. 

> [!IMPORTANT]
> A configuração de DNS para a conta do Azure Cosmos precisa de uma modificação manual no arquivo de hosts para incluir o FQDN da conta específica. Em cenários de produção, você configurará o servidor DNS para usar os endereços IP privados. No entanto, para fins de demonstração, você pode usar permissões de administrador na VM e modificar o arquivo de `c:\Windows\System32\Drivers\etc\hosts` (no Windows) ou `/etc/hosts` arquivo (no Linux) para incluir o endereço IP e o mapeamento DNS.

1. Para incluir o endereço IP e o mapeamento DNS, entre na sua máquina virtual *myVM*, abra o arquivo `c:\Windows\System32\Drivers\etc\hosts` e inclua as informações de DNS da etapa anterior no seguinte formato:

   [Endereço IP privado] [Ponto de extremidade da conta]. Documents. Azure. com

   **Exemplo:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Na Área de Trabalho Remota de *myVM*, instale [Gerenciador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selecione **contas de Cosmos dB (versão prévia)** com o clique com o botão direito do mouse.

1. Selecione **conectar-se a Cosmos DB**.

1. Selecione **API**.

1. Insira a cadeia de conexão colando as informações copiadas anteriormente.

1. Selecione **Avançar**.

1. Selecione **Conectar**.

1. Procure os bancos de dados do Azure Cosmos e contêineres de *mycosmosaccount*.

1. (Opcionalmente) adicione novos itens a *mycosmosaccount*.

1. Feche a conexão de área de trabalho remota para *myVM*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar o ponto de extremidade privado, a conta do Azure Cosmos e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira o *MyResource* na caixa de **pesquisa** na parte superior do portal e selecione o *MyResource* nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira o grupo de *recursos* de para **digite o nome** e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma VM em uma rede virtual, uma conta do Azure Cosmos e um ponto de extremidade privado. Você se conectou à VM da Internet e se comunica com segurança à conta do Azure Cosmos usando o link privado.

* Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](private-endpoint-overview.md).

* Para saber mais sobre a limitação do ponto de extremidade privado ao usar com Azure Cosmos DB, consulte o [link privado do Azure com Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artigo.