---
title: Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
description: Saiba como conectar-se de forma privada a uma conta de armazenamento no Azure usando um ponto de extremidade privado.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: bb1913d77616869c889c464a41e8166b3a88b03c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028878"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, uma conta de armazenamento com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta de armazenamento da VM.

> [!NOTE]
> Os pontos de extremidade privados não são permitidos em conjunto com pontos de extremidade de serviço na mesma sub-rede!

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta de armazenamento neste exemplo).

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Local | Selecione **WestCentralUS**.|
    | Sub-rede – Nome | Insira *mySubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
1. Deixe o restante com os valores padrão e selecione **Criar**.


### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
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
    ||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-your-private-endpoint"></a>Criar seu ponto de extremidade privado
Nesta seção, você criará uma conta de armazenamento particular usando um ponto de extremidade privado para ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento**.

1. Em **criar conta de armazenamento-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Insira *mystorageaccount*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    | Região | Selecione **WestCentralUS**. |
    | Performance| Deixe o padrão **Standard**. |
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v2)** . |
    | Replicação | Selecione **armazenamento com redundância geográfica com acesso de leitura (ra-grs)** . |
    |||
  
3. Selecione **Avançar: rede**.
4. Em **criar uma conta de armazenamento –** método de conectividade, rede, selecione **ponto de extremidade privado**.
5. Em **criar uma conta de armazenamento – rede**, selecione **Adicionar ponto de extremidade privado**. 
6. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    |Local|Selecione **WestCentralUS**.|
    |Nome|Insira *myPrivateEndpoint*.  |
    |Subrecurso de armazenamento|Deixe o **blob**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Sub-rede | Selecione *mysubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO**|  |
    | Integrar com a zona DNS privado  | Deixe o padrão **Sim**. |
    | Zona DNS privada  | Deixe o padrão **(novo) privatelink.blob.Core.Windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 
10. Navegue até o recurso de conta de armazenamento que você acabou de criar.
11. Selecione **chaves de acesso** no menu conteúdo à esquerda.
12. Selecione **copiar** na cadeia de conexão para key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Acessar a conta de armazenamento de forma privada da VM

Nesta seção, você se conectará de forma privada à conta de armazenamento usando o ponto de extremidade privado.

1. Na Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Insira `nslookup mystorageaccount.blob.core.windows.net` você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Instale o [Gerenciador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecione **contas de armazenamento** com o botão direito do mouse.
5. Selecione **conectar a um armazenamento do Azure**.
6. Selecione **usar uma cadeia de conexão**.
7. Selecione **Avançar**.
8. Insira a cadeia de conexão colando as informações copiadas anteriormente.
9. Selecione **Avançar**.
10. Selecione **Conectar**.
11. Procurar os contêineres de blob de mystorageaccount 
12. Opcionalmente Crie pastas e/ou carregue arquivos no *mystorageaccount*. 
13. Feche a conexão de área de trabalho remota para *myVM*. 

Opções adicionais para acessar a conta de armazenamento:
- Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo gratuito da Microsoft que permite que você trabalhe visualmente com os dados do armazenamento do Azure no Windows, no macOS e no Linux. Você pode instalar o aplicativo para navegar de forma privada o conteúdo da conta de armazenamento. 
 
- O utilitário AzCopy é outra opção para transferência de dados programável por scripts de alto desempenho para o armazenamento do Azure. Use AzCopy para transferir dados para e do armazenamento de Blobs, Arquivo e Tabela. 


## <a name="clean-up-resources"></a>Limpar os recursos 
Quando você terminar de usar o ponto de extremidade privado, a conta de armazenamento e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 
1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa. 
2. Selecione **Excluir grupo de recursos**. 
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**. 

## <a name="next-steps"></a>Próximos passos
Neste guia de início rápido, você criou uma VM em uma rede virtual e uma conta de armazenamento e um ponto de extremidade privado. Você se conectou a uma VM da Internet e se comunica com segurança à conta de armazenamento usando o link privado. Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](private-endpoint-overview.md).
