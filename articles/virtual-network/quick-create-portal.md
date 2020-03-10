---
title: Criar uma rede virtual – início rápido – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, você aprende como criar uma rede virtual usando o Portal do Azure. Uma rede virtual permite que recursos do Azure, como máquinas virtuais, comuniquem-se de forma segura uns com os outros e usando a Internet
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393111"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual usando o Portal do Azure

Uma rede virtual é o bloco de construção fundamental de sua rede privada no Azure. Ela permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se de forma segura uns com os outros e usando a Internet. Neste Início Rápido, você aprenderá a criar uma rede virtual usando o portal do Azure. Em seguida, você poderá implantar duas VMs na rede virtual, comunicar-se com segurança entre as duas VMs e conectar-se às VMs da Internet.


Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**.

2. No Azure Marketplace, selecione **rede** > **rede virtual**.

3. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    | {1&gt;Nome&lt;1} | Insira *myVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Assinatura | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Local | Selecione **Leste dos EUA**.|
    | Sub-rede – Nome | Insira *myVirtualSubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |

4. Deixe o restante com os valores padrão e selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No menu do portal do Azure, selecione **Criar um recurso**.

2. No Azure Marketplace, selecione **computação** > **Windows Server 2019 datacenter**.

3. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm1*. |
    | Região | Selecione **Leste dos EUA**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Deixe o padrão **Datacenter do Windows Server 2019**. |
    | Size | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirme a senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |

4. Selecione **Avançar: discos**.

5. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

6. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    | Rede virtual | Deixe o padrão **myVirtualNetwork**. |
    | Sub-rede | Deixe o padrão **myVirtualSubnet (10.1.0.0/24)** . |
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.

7. Selecione **Avançar: gerenciamento**.

8. Em **Criar uma máquina virtual – Gerenciamento**, para **Conta de armazenamento de diagnóstico**, selecione **Criar novo**.

9. Em **Criar conta de armazenamento**, insira ou selecione estas informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    | {1&gt;Nome&lt;1} | Insira *myvmstorageaccount*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Tipo de conta | Deixe o padrão **Armazenamento (uso geral v1)** . |
    | Desempenho | Deixe o padrão **Standard**. |
    | Replicação | Deixe o padrão **LRS (Armazenamento com redundância local)** . |

10. Selecione **OK**

11. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

12. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. Conclua as etapas 1 e 9 acima.

    > [!NOTE]
    > Na etapa 2, para o **Nome da máquina virtual**, insira *myVm2*.
    >
    > Na etapa 7, para **Conta de armazenamento de diagnóstico**, certifique-se de selecionar **myvmstorageaccount**.

2. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar** e o Azure valida sua configuração.

3. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Depois de criar *myVm1*, conecte-se à Internet.

1. Na barra de pesquisa do portal, insira *myVm1*.

2. Selecione o botão **Conectar**.

    ![Conectar-se a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

3. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

4. Abra o arquivo *.rdp* baixado.

    1. Se solicitado, selecione **Conectar**.

    2. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

5. Selecione **OK**.

6. Você pode receber um aviso de certificado durante o processo de entrada. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

7. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na Área de Trabalho Remota do *myVm1*, abra o PowerShell.

2. Digite `ping myVm2`.

    Você receberá uma mensagem semelhante a esta:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O `ping` falha, pois `ping` usa o ICMP (Internet Control Message Protocol). Por padrão, o ICMP não é permitido pelo Firewall do Windows.

3. Para permitir que *myVm2* execute o ping *myVm1* em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite a entrada ICMP pelo firewall do Windows:

4. Feche a conexão da área de trabalho remota para *myVm1*.

5. Complete as etapas em [Conecte uma VM a partir da Internet](#connect-to-a-vm-from-the-internet) novamente, mas conecte para *myVm2*.

6. Em um prompt de comando, insira `ping myvm1`.

    Você obterá algo parecido com esta mensagem:

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Você recebe respostas de *myVm1*, porque permitiu ICMP através do firewall do Windows na VM *myVm1* na etapa 3.

7. Feche a conexão da área de trabalho remota para *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as VMs, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal e selecione **myResourceGroup** nos resultados da pesquisa.

2. Selecione **Excluir grupo de recursos**.

3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste Início Rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs de maneira segura. Para saber mais sobre configurações de rede virtual, consulte [Gerenciar uma rede virtual](manage-virtual-network.md).

Por padrão, o Azure permite comunicação segura irrestrita entre VMs. Por outro lado, ele só permite conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Para saber mais sobre como configurar diferentes tipos de comunicações de rede de VMs, acesse o tutorial [Filtrar o tráfego de rede](tutorial-filter-network-traffic.md).
