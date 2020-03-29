---
title: Link Privado - Portal Azure - Banco de Dados Azure para MariaDB
description: Saiba como configurar link privado para Banco de Dados Azure para MariaDB no portal Azure
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 3f421cad64caf91b898bb1ec13dc909b93b7f72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370331"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Criar e gerenciar o Private Link para O Banco de Dados Azure para MariaDB usando o Portal

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.  Neste artigo, você aprenderá a usar o portal Azure para criar uma VM em uma Rede Virtual Azure e um Banco de Dados Azure para servidor MariaDB com um ponto final privado do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure, onde o Azure Database for MariaDB suporta níveis de preços otimizados para propósito geral e memória.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Faça login no [portal Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure

Nesta seção, você criará a rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso De Link Privado (um servidor MariaDB no Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM usada para acessar o recurso de Link Privado.

1. No lado superior esquerdo da tela, selecione **Criar uma rede** > virtual de rede**de** > **recursos**.
2. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Digite *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **Europa Ocidental**.|
    | Sub-rede – Nome | Insira *mySubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
3. Deixe o restante com os valores padrão e selecione **Criar**.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela no portal Azure, selecione Criar uma**máquina virtual****de computação** >  **de recursos** > .

2. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **Europa Ocidental**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DO ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
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
    |||


1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**.

## <a name="create-an-azure-database-for-mariadb"></a>Criar um Banco de Dados do Azure para MariaDB

Nesta seção, você criará um banco de dados Azure para servidor MariaDB no Azure. 

1. No lado superior esquerdo da tela no portal Azure, selecione **Criar um banco** > de**dados de** > recursos**Azure database para MariaDB**.

1. No **Banco de Dados Do Azure para MariaDB,** forneça essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Insira *myserver*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Nome de usuário do administrador| Insira um nome de administrador de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 8 caracteres e atender a requisitos complexidade definidos. |
    | Location | Selecione uma região do Azure onde deseja que seu servidor MariaDB resida. |
    |Versão  | Selecione a versão do banco de dados do servidor MariaDB que é necessário.|
    | Calcular + Armazenamento| Selecione o nível de preços necessário para o servidor com base na carga de trabalho. |
    |||
 
7. Selecione **OK**. 
8. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem aprovada pela Validação, selecione **Criar**. 
10. Quando vir a mensagem Validação aprovada, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um ponto final privado para o servidor MariaDB para ele. 

1. No lado superior esquerdo da tela no portal Azure, selecione **Criar um recurso** > **Networking** > **Private Link**.
2. Em **Central de Link Privado – Visão Geral**, na opção **Criar uma conexão privada com um serviço**, selecione **Iniciar**.

    ![Visão geral do Link Privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Em **Criar um ponto final privado - Noções básicas,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes da instância** |  |
    | Nome | Insira *myPrivateEndpoint*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    |Região|Selecione **Europa Ocidental**.|
    |||
5. Selecione **A seguir: Recurso**.
6. Em **Criar um ponto de extremidade privado – Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione conectar-se a um recurso do Azure em meu diretório.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.DBforMariaDB/servidores**. |
    | Recurso |Selecione *myServer*|
    |Sub-recurso de destino |Selecione *mariadbServer*|
    |||
7. Selecione **A seguir: Configuração**.
8. Em **Criar um ponto final privado - Configuração,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione  *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privado |Selecione **Sim**. |
    |Zona DNS privado |Selecione *(Novo)privatelink.mariadb.database.azure.com* |
    |||

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**. 

    ![Link privado criado](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > O FQDN na configuração de DNS do cliente não resolve o IP privado configurado. Você terá que configurar uma região DNS para o FQDN configurado como mostrado [aqui](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando a RDP (Área de Trabalho Remota)


Depois de criar **myVm**, conecte-se a ela pela Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Você pode precisar selecionar **Mais opções** > **Use uma conta diferente,** para especificar as credenciais inseridas quando criou a VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Acesse o servidor MariaDB privadamente da VM

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

2. Insira  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a conexão de link privado para o servidor MariaDB usando qualquer cliente disponível. No exemplo abaixo eu usei [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para fazer a operação.


4. Em **Nova conexão,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **MariaDB**.|
    | Nome do servidor| Selecione *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Nome de usuário | Digite o username@servername nome de usuário como é fornecido durante a criação do servidor MariaDB. |
    |Senha |Digite uma senha fornecida durante a criação do servidor MariaDB. |
    |SSL|Selecione **Obrigatório**.|
    ||

5. Selecione **Conexão de teste** ou **OK**.

6. (Opcionalmente) Procurar bancos de dados do menu esquerdo e criar ou consultar informações do banco de dados MariaDB

7. Feche a conexão remota da área de trabalho para myVm.

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar de usar o ponto final privado, o servidor MariaDB e a VM, exclua o grupo de recursos e todos os recursos que ele contém:

1. Digite *myResourceGroup* na caixa **pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa.
2. Selecione **Excluir grupo de recursos**.
3. Digite myResourceGroup para **digitar o nome do grupo de recursos e** **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste como fazer, você criou uma VM em uma rede virtual, um Banco de Dados Azure para MariaDB e um ponto final privado para acesso privado. Você se conectou a uma VM da internet e se comunicou com segurança ao servidor MariaDB usando o Private Link. Para saber mais sobre os pontos finais privados, consulte [O que é o ponto final privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
