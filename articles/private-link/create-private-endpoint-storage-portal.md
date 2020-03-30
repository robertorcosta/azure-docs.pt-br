---
title: Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
description: Aprenda a se conectar privadamente a uma conta de armazenamento no Azure usando um Ponto Final Privado.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8c76333d5a2be8a2c589dbe54389b023fef34854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252524"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
O Azure Private Endpoint é o bloco fundamental de construção do Private Link no Azure. Ele permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem privadamente com os recursos do Private Link.

Neste Quickstart, você aprenderá como criar uma VM em uma rede virtual Do Zure, uma conta de armazenamento com um Ponto Final Privado usando o portal Azure. Em seguida, você pode acessar com segurança a conta de armazenamento da VM.

> [!NOTE]
> Os pontos de extremidade privados não são permitidos em conjunto com pontos de extremidade de serviço na mesma sub-rede!

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma máquina virtual
Nesta seção, você criará a rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu Recurso de Link Privado (uma conta de armazenamento neste exemplo).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta seção, você criará a rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso do Private Link.

Nesta seção, você precisará substituir os seguintes parâmetros nas etapas com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroup |
| **\<>de nome de rede virtual** | myVirtualNetwork          |
| **\<>de nome da região**          | Centro-Oeste dos EUA      |
| **\<IPv4-endereço-espaço>**   | 10.1.0.0\16          |
| **\<>de nomes de sub-rede**          | mySubnet        |
| **\<>de alcance de endereço de sub-rede** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Criar máquina virtual

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
    ||

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**.

## <a name="create-your-private-endpoint"></a>Crie seu ponto final privado
Nesta seção, você criará uma conta de armazenamento privada usando um Ponto Final Privado para ela. 

1. No lado superior esquerdo da tela no portal Azure, selecione **Criar uma** > conta de**armazenamento** > **de**recursos .

1. Em **Criar conta de armazenamento - Noções básicas,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Digite *minha conta de armazenamento*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    | Região | Selecione **WestCentralUS**. |
    | Desempenho| Deixe o padrão **Standard**. |
    | Tipo de conta | Deixe o armazenamento padrão **(propósito geral v2)**. |
    | Replicação | Selecione **armazenamento geo-redundante de acesso de leitura (RA-GRS)**. |
    |||
  
3. Selecione **A seguir: Rede**.
4. Em **Criar uma conta de armazenamento - Rede,** método de conectividade, selecione Ponto final **privado**.
5. Em **Criar uma conta de armazenamento - Rede,** selecione Adicionar ponto final **privado**. 
6. Em **Criar ponto final privado,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    |Location|Selecione **WestCentralUS**.|
    |Nome|Insira *myPrivateEndpoint*.  |
    |Sub-recurso de armazenamento|Deixe o **Blob**padrão. |
    | **Rede** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *myResourceGroup*. |
    | Sub-rede | Selecione *mySubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO**|  |
    | Integrar com a zona DNS privado  | Deixe o padrão **Sim**. |
    | Zona DNS privada  | Deixe o padrão **(Novo) privatelink.blob.core.windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**. 
10. Navegue até o recurso da conta de armazenamento que você acabou de criar.
11. Selecione **Teclas** de acesso no menu de conteúdo à esquerda.
12. Selecione **Copiar** na seqüência de conexões para tecla1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Acesse a conta de armazenamento privada mente da VM

Nesta seção, você se conectará privadamente à conta de armazenamento usando o Ponto Final Privado.

1. Na Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Digite `nslookup mystorageaccount.blob.core.windows.net` Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Instale o [Gerenciador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecione **Contas de armazenamento** com o clique com o botão direito do mouse.
5. Selecione **Conectar a um armazenamento azure**.
6. Selecione **Usar uma seqüência de conexões**.
7. Selecione **Avançar**.
8. Digite a seqüência de conexões colando as informações copiadas anteriormente.
9. Selecione **Avançar**.
10. Selecione **Conectar**.
11. Navegue pelos contêineres Blob da minha conta de armazenamento 
12. (Opcionalmente) Crie pastas e/ou carregue arquivos na *minha conta de armazenamento*. 
13. Feche a conexão remota da área de trabalho para *myVM*. 

Opções adicionais para acessar a conta de armazenamento:
- O Microsoft Azure Storage Explorer é um aplicativo gratuito autônomo da Microsoft que permite que você trabalhe visualmente com dados de armazenamento do Azure no Windows, macOS e Linux. Você pode instalar o aplicativo para navegar privadamente pelo conteúdo da conta de armazenamento. 
 
- O utilitário AzCopy é outra opção para transferência de dados com script de alto desempenho para armazenamento Azure. Use AzCopy para transferir dados para e do armazenamento de Blobs, Arquivo e Tabela. 


## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o Private Endpoint, a conta de armazenamento e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 
1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa. 
2. Selecione **Excluir grupo de recursos**. 
3. Digite *myResourceGroup* para **digitar o nome do grupo de recursos e** **selecione Excluir**. 

## <a name="next-steps"></a>Próximas etapas
Neste Quickstart, você criou uma VM em uma rede virtual e conta de armazenamento e um Ponto Final Privado. Você se conectou a uma VM da internet e comunicou com segurança à conta de armazenamento usando o Private Link. Para saber mais sobre o Private Endpoint, veja [O que é o Ponto Final Privado do Azure?](private-endpoint-overview.md).
