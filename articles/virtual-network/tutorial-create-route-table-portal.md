---
title: Rotear o tráfego de rede – tutorial – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, aprenda a rotear tráfego de rede com uma tabela de rotas usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: be4a47d26bcfc407734956a3d9bf8778c5afcfb4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350287"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Rotear tráfego com uma tabela de rotas utilizando o Portal do Azure

O Azure roteia tráfego entre todas as sub-redes contidas em uma rede virtual por padrão. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de uma NVA (solução de virtualização de rede). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Criar uma rede virtual com várias sub-redes
> * Associar uma tabela de rotas a uma sub-rede
> * Criar uma NVA que roteia o tráfego
> * Implantar VMs (máquinas virtuais) em diferentes sub-redes
> * Rotear o tráfego de uma sub-rede para outra por meio de uma NVA

Se preferir, você poderá finalizar este tutorial usando a [CLI do Azure](tutorial-create-route-table-cli.md) ou o [Azure PowerShell](tutorial-create-route-table-powershell.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Tela de rota**.

1. Em **Criar tabela de rota**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *myRouteTablePublic*. |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, então selecione *OK*. |
    | Location | Deixe o padrão **Leste dos EUA**.
    | Propagação de rota do gateway de rede virtual | Deixe o padrão **Habilitado**. |

1. Selecione **Criar**.

## <a name="create-a-route"></a>Criar uma rota

1. Na barra de pesquisa do portal, insira *myRouteTablePublic*.

1. Quando **myRouteTablePublic** aparecer nos resultados da pesquisa, selecione-o.

1. Em **myRouteTablePublic** em **Configurações**, selecione **Rotas** >  **+ Adicionar**.

    ![Adicionar rota](./media/tutorial-create-route-table-portal/add-route.png)

1. Em **Adicionar rota**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da rota | Insira *ToPrivateSubnet*. |
    | Prefixo de endereço | Insira *10.0.1.0/24*. |
    | Tipo do próximo salto | Selecione **Solução de virtualização**. |
    | Endereço do próximo salto | Insira *10.0.2.4*. |

1. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *myVirtualNetwork*. |
    | Espaço de endereço | Insira *10.0.0.0/16*. |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione ***Selecionar existente*** > **myResourceGroup**. |
    | Location | Deixe o padrão **Leste dos EUA**. |
    | Sub-rede – Nome | Insira *Público*. |
    | Sub-rede – Intervalo de endereços | Insira *10.0.0.0/24*. |

1. Deixe o restante dos padrões e selecione **Criar**.

### <a name="add-subnets-to-the-virtual-network"></a>Adicionar sub-redes à rede virtual

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*.

1. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.

1. Na **myVirtualNetwork**, em **Configurações**, selecione **Sub-redes** >  **+ Sub-rede**.

    ![Adicionar sub-rede](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Em **Adicionar sub-rede**, insira estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *Privada*. |
    | Espaço de endereço | Insira *10.0.1.0/24*. |

1. Deixe o restante dos padrões e selecione **OK**.

1. Selecione **+ Sub-rede** novamente. Desta vez, insira estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *DMZ*. |
    | Espaço de endereço | Insira *10.0.2.0/24*. |

1. Como a última vez, deixe o restante dos padrões e selecione **OK**.

    O Azure mostra três sub-redes: **Público**, **Privado** e **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associar myRouteTablePublic à sua sub-rede Pública

1. Selecione **Público**.

1. Em **Público**, selecione **Tabela de rotas** > **MyRouteTablePublic** > **Salvar**.

    ![Associar tabela de rotas](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Criar uma NVA

NVAs são VMs que ajudam com as funções de rede como a otimização de roteamento e firewall. Você poderá selecionar um sistema operacional diferente se desejar. Este tutorial presume que você esteja usando o **Windows Server 2016 Datacenter**.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVmNva*. |
    | Região | Selecione **Leste dos EUA**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Deixe o padrão **Datacenter do Windows Server 2016**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**.
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |

1. Selecione **Avançar: Discos**.

1. Em **Criar uma máquina virtual – discos**, selecione as configurações adequadas para suas necessidades.

1. Selecione **Avançar: Rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o padrão **myVirtualNetwork**. |
    | Sub-rede | Selecione **DMZ (10.0.2.0/24)** . |
    | IP público | Selecione **Nenhum**. Você não precisa de um endereço IP público. A VM não se conecta à Internet.|

1. Deixe o restante dos padrões e selecione **Avançar: Gerenciamento**.

1. Em **Criar uma máquina virtual – Gerenciamento**, para **Conta de armazenamento de diagnóstico**, selecione **Criar novo**.

1. Em **Criar conta de armazenamento**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *mynvastorageaccount*. |
    | Tipo de conta | Deixe o padrão **Armazenamento (uso geral v1)** . |
    | Desempenho | Deixe o padrão **Standard**. |
    | Replicação | Deixe o padrão **LRS (Armazenamento com redundância local)** .

1. Selecione **OK**

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar** e o Azure valida sua configuração.

1. Quando você vir **Validação aprovada**, selecione **Criar**.

    A VM demora alguns minutos para criar. Não continue até que o Azure termine de criar a VM. A página **Sua implantação está em andamento** mostrará os detalhes de implantação.

1. Quando sua VM estiver pronta, selecione **Ir para o recurso**.

## <a name="turn-on-ip-forwarding"></a>Ativar o encaminhamento de IP

Ativar o encaminhamento de IP para *myVmNva*. Quando o Azure envia o tráfego de rede para *myVmNva*, se o tráfego for destinado a um endereço IP diferente, o encaminhamento de IP enviará o tráfego para a localização correta.

1. Em **myVmNva**, em **Configurações**, selecione **Rede**.

1. Selecione **myvmnva123**. Esse é o adaptador de rede que o Azure criou para sua VM. Ele terá uma cadeia de caracteres de números para torná-la exclusiva para você.

    ![Rede de VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Em **Configurações**, selecione **Configurações de IP**.

1. Em **myvmnva123 – configurações de IP**, para **Encaminhamento de IP**, selecione **Habilitado** e, em seguida, selecione **Salvar**.

    ![Habilitar o encaminhamento de IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Crie máquinas virtuais públicas e privadas

Crie uma VM pública e uma VM privada na rede virtual. Posteriormente, você vai usá-los para ver que o Azure roteia o tráfego da sub-rede *Pública* rede para a sub-rede *Privada* por meio da NVA.

Conclua as etapas 1 a 12 de [Criar uma NVA](#create-an-nva). Use a maioria das mesmas configurações. Estes valores são os que precisam ser diferentes:

| Configuração | Valor |
| ------- | ----- |
| **VM PÚBLICA** | |
| NOÇÕES BÁSICAS |  |
| Nome da máquina virtual | Insira *myVmPublic*. |
| REDE | |
| Sub-rede | Selecione **Público (10.0.0.0/24)** . |
| Endereço IP público | Aceite o padrão. |
| Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
| Selecione as portas de entrada | Selecione **HTTP** e **RDP**. |
| GERENCIAMENTO | |
| Conta de armazenamento de diagnóstico | Deixe o padrão **mynvastorageaccount**. |
| **VM PRIVADA** | |
| NOÇÕES BÁSICAS |  |
| Nome da máquina virtual | Insira *myVmPrivate*. |
| REDE | |
| Sub-rede | Selecione **Privado (10.0.1.0/24)** . |
| Endereço IP público | Aceite o padrão. |
| Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
| Selecione as portas de entrada | Selecione **HTTP** e **RDP**. |
| GERENCIAMENTO | |
| Conta de armazenamento de diagnóstico | Deixe o padrão **mynvastorageaccount**. |

Você pode criar a VM *myVmPrivate* enquanto o Azure cria a VM *myVmPublic*. Não continue com o restante das etapas até que o Azure termine de criar ambas as VMs.

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Entrar em myVmPrivate pela área de trabalho remota

1. Na barra de pesquisa do portal, insira *myVmPrivate*.

1. Quando **myVmPrivate** aparecer nos resultados da pesquisa, selecione-o.

1. Selecione **Conectar** para criar uma conexão de área de trabalho remota para a VM *myVmPrivate*.

1. Em **Conectar-se à máquina virtual**, selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *.rdp* baixado.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e senha que você especificou ao criar a VM Privada.

    1. Talvez você precise selecionar **Mais escolhas** > **Usar uma conta diferente**, para usar as credenciais de VM Privada.

1. Selecione **OK**.

    Você pode receber um aviso de certificado durante o processo de entrada.

1. Selecione **Sim** para se conectar à VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>Habilite o ICMP usando o firewall do Windows

Em uma etapa posterior, você usará a ferramenta de rastreamento de rota para testar o roteamento. A rota de rastreamento usa o protocolo ICMP, que o Firewall do Windows nega por padrão. Habilite o ICMP por meio do Firewall do Windows.

1. Na Área de Trabalho Remota do *myVmPrivate*, abra o PowerShell.

1. Insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Você está usando o rastreamento de rotas para testar o roteamento neste tutorial. Para ambientes de produção, não é recomendável permitir ICMP por meio do Firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ativar o encaminhamento de IP no myVmNva

Você [ativou o encaminhamento de IP](#turn-on-ip-forwarding) para o adaptador de rede da VM usando o Azure. O sistema operacional da VM também deve encaminhar o tráfego de rede. Ativar o encaminhamento de IP para o sistema operacional da VM *myVmNva* com estes comandos.

1. Em um prompt de comando na VM *myVmPrivate*, abra uma área de trabalho remota para a VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Do PowerShell no *myVmNva*, insira este comando para ativar o encaminhamento de IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Reinicie a VM *myVmNva*. Na barra de tarefas, selecione **Botão iniciar** > **Botão liga/desliga**, **Outro (Planejado)**  > **Continuar**.

    Que também desconecta a sessão de área de trabalho remota.

1. Após a VM *myVmNva* reiniciar, crie uma sessão de área de trabalho remota para a VM *myVmPublic*. Enquanto ainda conectado à VM *myVmPrivate*, abra um prompt de comando e execute este comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na Área de Trabalho Remota do *myVmPublic*, abra o PowerShell.

1. Habilite o ICMP por meio do Firewall do Windows digitando este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o roteamento de tráfego de rede

Primeiro, vamos testar o roteamento de tráfego de rede da VM *myVmPublic* para a VM *myVmPrivate*.

1. Do PowerShell na VM *myVmPublic*, digite este comando:

    ```powershell
    tracert myVmPrivate
    ```

    A resposta é semelhante a este exemplo:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Você pode ver que o primeiro salto é para 10.0.2.4. É o endereço IP privado da NVA. O segundo salto é para o endereço IP privado da VM *myVmPrivate*: 10.0.1.4. Anteriormente, você adicionou a rota para a tabela de rotas *myRouteTablePublic* e a associo à sub-rede *Pública*. Como resultado, o Azure enviou o tráfego por meio da NVA, e não diretamente para a sub-rede *Privada*.

1. Feche a sessão da área de trabalho remota para a VM *myVmPublic*, que ainda mantém você ainda conectado à VM *myVmPrivate*.

1. Em um prompt de comando na VM *myVmPrivate*, digite este comando:

    ```cmd
    tracert myVmPublic
    ```

    Ele testa o roteamento de tráfego de rede da VM *myVmPrivate* para a VM *myVmPublic*. A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Você pode ver que o Azure roteia o tráfego diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.

1. Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele tem:

1. Na barra de pesquisa do portal, insira *myResourceGroup*.

1. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.

1. Selecione **Excluir grupo de recursos**.

1. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma NVA simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Agora que você sabe como fazer isso, pode implantar diferentes NVAs pré-configurados do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Eles realizam muitas funções de rede que você vai considerar úteis. Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md) e [Gerenciar uma tabela de rotas](manage-route-table.md).

Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, o Azure não pode implantar recursos para alguns serviços PaaS em uma rede virtual. É possível restringir o acesso aos recursos de alguns serviços PaaS do Azure. Porém, a restrição deve ser apenas tráfego de uma sub-rede de rede virtual. Para aprender como restringir o acesso de rede aos recursos PaaS do Azure, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](tutorial-restrict-network-access-to-resources.md)
