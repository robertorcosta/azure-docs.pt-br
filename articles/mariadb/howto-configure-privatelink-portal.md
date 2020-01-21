---
title: Link privado para o método de instalação do portal do banco de dados do Azure para MariaDB (versão prévia)
description: Saiba como configurar o link privado para o banco de dados do Azure para MariaDB do portal do Azure
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 76e9526ab39cbccd45a48d2cd24e05867c953774
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280825"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-preview-using-portal"></a>Criar e gerenciar o link privado para o banco de dados do Azure para MariaDB (versão prévia) usando o portal

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.  Neste artigo, você aprenderá a usar o portal do Azure para criar uma VM em uma rede virtual do Azure e um banco de dados do Azure para MariaDB Server com um ponto de extremidade privado do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MariaDB dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um servidor MariaDB no Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM usada para acessar o recurso de Link Privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
2. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Local | Selecione **Europa Ocidental**.|
    | Sub-rede – Nome | Insira *mySubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
3. Deixe o restante com os valores padrão e selecione **Criar**.

### <a name="create-virtual-machine"></a>Crie uma Máquina Virtual

1. No lado superior esquerdo da tela no portal do Azure, selecione **Criar um recurso** > **Computação** > **Máquina Virtual**.

2. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **Europa Ocidental**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
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
    | Rede virtual | Deixe o padrão **MyVirtualNetwork**.  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    |||


1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-an-azure-database-for-mariadb"></a>Criar um Banco de Dados do Azure para MariaDB

Nesta seção, você criará um banco de dados do Azure para o servidor MariaDB no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **bancos** de **dados > banco de dados do Azure para MariaDB**.

1. No **banco de dados do Azure para MariaDB** , forneça estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Insira *myserver*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Nome de usuário do administrador| Insira um nome de administrador de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 8 caracteres e atender a requisitos complexidade definidos. |
    | Local | Selecione uma região do Azure onde você deseja que seu servidor MariaDB resida. |
    |Versão  | Selecione a versão do banco de dados do servidor MariaDB que é necessária.|
    | Computação + armazenamento| Selecione o tipo de preço necessário para o servidor com base na carga de trabalho. |
    |||
 
7. Selecione **OK**. 
8. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando a mensagem Validação aprovada for exibida, selecione **Criar**. 
10. Quando vir a mensagem Validação aprovada, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um ponto de extremidade privado para o servidor MariaDB. 

1. No lado superior esquerdo da tela no portal do Azure, selecione **Criar um recurso** > **Rede** > **Central de Link Privado (Versão Prévia)** .
2. Em **Central de Link Privado – Visão Geral**, na opção **Criar uma conexão privada com um serviço**, selecione **Iniciar**.

    ![Visão geral do link privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Em **Criar um ponto de extremidade privado (versão prévia) – noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes da instância** |  |
    | Nome | Insira *myPrivateEndpoint*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    |Região|Selecione **Europa Ocidental**.|
    |||
5. Selecione **Avançar: recurso**.
6. Em **Criar um ponto de extremidade privado – Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione conectar-se a um recurso do Azure em meu diretório.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft. DBforMariaDB/servidores**. |
    | Grupos |Selecione *myServer*|
    |Sub-recurso de destino |Selecionar *mariadbServer*|
    |||
7. Selecione **Avançar: configuração**.
8. Em **Criar um ponto de extremidade privado (versão prévia) – configuração**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione  *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(novo) privatelink. MariaDB. Database. Azure. com* |
    |||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 

    ![Link privado criado](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando a RDP (Área de Trabalho Remota)


Depois de criar **myVm**, conecte-se a ela pela Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Acessar o servidor MariaDB de forma privada da VM

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

2. Insira  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MariaDB server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **MariaDB**.|
    | Server name| Select *mydemoserver.privatelink.mariadb.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the MariaDB server creation. |
    |Password |Enter a password provided during the MariaDB server creation. |
    |SSL|Select **Required**.|
    ||

5. Select **Test Connection** or **OK**.

6. (Optionally) Browse databases from left menu and Create or query information from the MariaDB database

7. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, MariaDB server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for MariaDB, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the MariaDB server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
