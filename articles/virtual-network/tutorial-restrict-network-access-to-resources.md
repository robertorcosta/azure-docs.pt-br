---
title: Restringir o acesso de recursos de PaaS – tutorial – portal do Azure
description: Neste tutorial, você aprende a limitar e restringir o acesso à rede de recursos do Azure, como Armazenamento do Azure e Banco de Dados SQL do Azure, com pontos de extremidade de serviço de rede virtual usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 85fc5687b82947ed16bde0c30ca2b947514ba958
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74186362"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutorial: restringir o acesso à rede de recursos de PaaS com pontos de extremidade de serviço de rede virtual usando o Portal do Azure

Os pontos de extremidade de serviço de rede virtual permitem limitar o acesso à rede a alguns recursos de serviço do Azure para uma sub-rede da rede virtual. Você também pode remover o acesso à Internet para os recursos. Os pontos de extremidade de serviço fornecerão conexão direta de sua rede virtual a um serviço do Azure, permitindo que você use o espaço de endereço privado da sua rede virtual para acessar os serviços do Azure compatíveis. O tráfego destinado aos recursos do Azure por meio de pontos de extremidade de serviço sempre fica na rede de backbone do Microsoft Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual com uma sub-rede
> * Adicionar uma sub-rede e habilitar um ponto de extremidade de serviço
> * Criar um recurso do Azure e permitir o acesso à rede para ele apenas de uma sub-rede
> * Implantar uma VM (máquina virtual) para cada sub-rede
> * Confirmar o acesso a um recurso por meio de uma sub-rede
> * Confirmar se o acesso é negado para um recurso por meio de uma sub-rede e da Internet

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-restrict-network-access-to-resources-cli.md) ou o [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira ou selecione as seguintes informações e selecione **Criar**:

   |Configuração|Valor|
   |----|----|
   |Nome| myVirtualNetwork |
   |Espaço de endereço| 10.0.0.0/16|
   |Subscription| Selecione sua assinatura|
   |Resource group | Selecione **Criar novo** e insira *myResourceGroup*.|
   |Location| Selecione **Leste dos EUA** |
   |Nome da sub-rede| Público|
   |Intervalo de endereços da sub-rede| 10.0.0.0/24|
   |Proteção contra DDOS| Basic|
   |Pontos de extremidade de serviço| Desabilitado|
   |Firewall| Desabilitado|

   ![Insira s informações básicas sobre sua rede virtual](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço

Pontos de extremidade de serviço são habilitados por serviço, por sub-rede. Criar uma sub-rede e habilitar um ponto de extremidade de serviço para a sub-rede.

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, digite *myVirtualNetwork.* Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Adicione uma sub-rede à rede virtual. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **+ Sub-rede**, conforme mostrado na seguinte imagem:

    ![Adicionar sub-rede](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Em **Adicionar sub-rede**, selecione ou insira as informações a seguir e selecione **OK**:

    |Configuração|Valor|
    |----|----|
    |Nome| Privado |
    |Intervalo de endereços| 10.0.1.0/24|
    |Pontos de extremidade de serviço| Selecione **Microsoft.Storage** em **Serviços**|

> [!CAUTION]
> Antes de habilitar um ponto de extremidade de serviço para uma sub-rede existente que tenha recursos nela, consulte [Alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso à rede de uma sub-rede

Por padrão, todas as VMs em uma sub-rede podem se comunicar com todos os recursos. Você pode limitar a comunicação com todos os recursos em uma sub-rede, criando um grupo de segurança de rede e o associando à sub-rede.

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e **Grupo de segurança de rede**.
3. Em **Criar um grupo de segurança de rede**, insira ou selecione as seguintes informações e selecione **Criar**:

    |Configuração|Valor|
    |----|----|
    |Nome| myNsgPrivate |
    |Subscription| Selecione sua assinatura|
    |Resource group | Selecione **Usar existente** e, em seguida, *myResourceGroup*.|
    |Location| Selecione **Leste dos EUA** |

4. Depois que o grupo de segurança de rede for criado, digite *myNsgPrivate*, na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal. Quando **myNsgPrivate** for exibido nos resultados da pesquisa, selecione-o.
5. Em **CONFIGURAÇÕES**, selecione **Regras de segurança de saída**.
6. Selecione **+ Adicionar**.
7. Crie uma regra que permita a comunicação de saída para o serviço de Armazenamento do Azure. Insira ou selecione as seguintes informações e selecione **Adicionar**:

    |Configuração|Valor|
    |----|----|
    |Fonte| Selecione **VirtualNetwork** |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **Marca de Serviço**|
    |Marca de serviço de destino | Selecione **Armazenamento**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|Allow|
    |Prioridade|100|
    |Nome|Allow-Storage-All|

8. Crie outra regra de segurança de saída que nega a comunicação com a Internet. Essa regra substitui uma regra padrão em todos os grupos de segurança de rede que permite a comunicação de saída à Internet. Conclua as etapas 5 a 7 novamente, usando os seguintes valores:

    |Configuração|Valor|
    |----|----|
    |Fonte| Selecione **VirtualNetwork** |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **Marca de Serviço**|
    |Marca de serviço de destino| Selecione **Internet**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|Negar|
    |Prioridade|110|
    |Nome|Deny-Internet-All|

9. Em **CONFIGURAÇÕES**, selecione **Regras de segurança de entrada**.
10. Selecione **+ Adicionar**.
11. Crie uma regra de segurança de entrada que permita o tráfego do protocolo RDP à sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. Conexões de área de trabalho remota são permitidas para a sub-rede para que a conectividade possa ser testada em uma etapa posterior. Em **CONFIGURAÇÕES**, selecione **Regras de segurança de entrada**, depois **+Adicionar**, insira os valores a seguir e depois selecione **Adicionar**:

    |Configuração|Valor|
    |----|----|
    |Fonte| Qualquer |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **VirtualNetwork**|
    |Intervalos de portas de destino| 3389 |
    |Protocolo|Qualquer|
    |Ação|Allow|
    |Prioridade|120|
    |Nome|Allow-RDP-All|

12. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
13. Selecione **+ Associar**
14. Em **Associar sub-rede**, selecione **Rede virtual** e **myVirtualNetwork** em **Escolher rede virtual**.
15. Em **Escolher sub-rede**, selecione **Privada**e selecione **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso à rede para um recurso

As etapas necessárias para restringir o acesso de rede a recursos criados por meio de serviços do Azure habilitados para pontos de extremidade do serviço variam de acordo com os serviços. Confira a documentação de serviços individuais para obter as etapas específicas para cada serviço. O restante deste tutorial inclui etapas para restringir o acesso de rede para uma conta de Armazenamento do Microsoft Azure como exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Armazenamento** e, em seguida, selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.
3. Insira ou selecione as informações a seguir, aceite os padrões restantes e selecione **Criar**:

    |Configuração|Valor|
    |----|----|
    |Nome| Insira um nome que seja exclusivo em todos os locais do Azure, com 3 a 24 caracteres de comprimento, usando apenas números e letras minúsculas.|
    |Tipo de conta|StorageV2 (v2 de uso geral)|
    |Location| Selecione **Leste dos EUA** |
    |Replicação| Armazenamento com redundância local (LRS)|
    |Subscription| Selecione sua assinatura|
    |Resource group | Selecione **Usar existente** e, em seguida, *myResourceGroup*.|

### <a name="create-a-file-share-in-the-storage-account"></a>Criar um compartilhamento de arquivos na conta de armazenamento

1. Depois que a conta de armazenamento for criada, insira o nome dela na caixa **Pesquisar recursos, serviços e documentos**, na parte superior do portal. Quando o nome da conta de armazenamento for exibido nos resultados da pesquisa, selecione-o.
2. Selecione **Arquivos**, conforme mostrado na seguinte figura:

   ![Conta de armazenamento](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Selecione **+Compartilhamento de arquivos**.
4. Digite *my-file-share* em **Nome** e selecione **OK**.
5. Feche a caixa **Serviço de arquivo**.

### <a name="restrict-network-access-to-a-subnet"></a>Restringir o acesso à rede para uma sub-rede

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede, incluindo a Internet. Negar acesso à rede da Internet e a todas as outras sub-redes em todas as redes virtuais, exceto para a sub-rede *Privada* na rede virtual *myVirtualNetwork*.

1. Em **CONFIGURAÇÕES** para a conta de armazenamento, selecione **Firewalls e redes virtuais**.
2. Selecione **Redes selecionadas**.
3. Selecione **+Adicionar rede virtual existente**.
4. Em **Adicionar redes**, selecione os seguintes valores e **Adicionar**:

    |Configuração|Valor|
    |----|----|
    |Subscription| Selecione sua assinatura.|
    |Redes virtuais|Selecione **myVirtualNetwork**, em **Redes virtuais**|
    |Sub-redes| Selecione **Privada**, em **Sub-redes**|

    ![Firewalls e redes virtuais](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Clique em **Salvar**.
6. Feche a caixa **Firewalls e redes virtuais**.
7. Em **CONFIGURAÇÕES** para a conta de armazenamento, selecione **Chaves de acesso**, conforme mostrado na seguinte figura:

      ![Firewalls e redes virtuais](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Anote o valor de **Chave**, pois você precisará inseri-lo manualmente em uma etapa posterior ao mapear o compartilhamento de arquivos para uma letra de unidade em uma VM.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso à rede para uma conta de armazenamento, implante uma VM para cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

1. Selecione **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.
3. Insira ou selecione as seguintes informações e selecione **OK**:

   |Configuração|Valor|
   |----|----|
   |Nome| myVmPublic|
   |Nome de usuário|Insira um nome de usuário de sua escolha.|
   |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Subscription| Selecione sua assinatura.|
   |Resource group| Selecione **Usar existente** e, em seguida, **myResourceGroup**.|
   |Location| Selecione **Leste dos EUA**.|

   ![Digite informações básicas sobre uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Selecione um tamanho para a máquina virtual e selecione **Selecionar**.
5. Em **Configurações**, selecione **Rede** e **myVirtualNetwork**. Em seguida, selecione **Sub-rede** e **Pública**, conforme mostrado na seguinte figura:

   ![Selecione uma rede virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. Em **Grupo de segurança de rede**, selecione **Avançado**. O portal cria automaticamente um grupo de segurança de rede que permite a porta 3389, a qual você vai precisará abrir para se conectar à máquina virtual em uma etapa posterior. Na página **Configurações**, selecione **OK**.
7. Na página **Resumo**, selecione **Criar** para iniciar a implantação da máquina virtual. A VM leva alguns minutos para ser implantada, mas você pode continuar na próxima etapa, enquanto a VM está sendo criada.

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

Conclua as etapas 1 a 7 novamente, mas, na etapa 3, nomeie a máquina virtual como *myVmPrivate* e, na etapa 5, selecione a sub-rede **Privada**.

A VM demora alguns minutos para implantar. Não prossiga para a próxima etapa até que ela conclua a criação e as configurações sejam abertas no portal.

## <a name="confirm-access-to-storage-account"></a>Confirmar acesso à conta de armazenamento

1. Quando a VM *myVmPrivate* terminar de ser criada, o Azure abrirá as configurações dela. Conecte-se à VM, selecionando o botão **Conectar**, conforme mostrado na seguinte figura:

   ![Conectar-se a uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Após selecionar o botão **Conectar**, um arquivo Remote Desktop Protocol (.rdp) será criado e baixado no computador.  
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. 
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.
6. Na VM *myVmPrivate*, mapeie o compartilhamento de arquivos do Azure para a unidade Z usando o PowerShell. Antes de executar os comandos a seguir, substitua `<storage-account-key>` e `<storage-account-name>` pelos valores fornecidos e recuperados em [Criar uma conta de armazenamento](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   O PowerShell retorna uma saída semelhante à seguinte saída de exemplo:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   O compartilhamento de arquivos do Azure foi mapeado com êxito para a unidade Z.

7. Confirme que a VM não tem conectividade de saída com a Internet em um prompt de comando:

   ```
   ping bing.com
   ```

   Você não recebe nenhuma resposta, pois o grupo de segurança de rede associado à sub-rede *Privada* não permite acesso de saída para a Internet.

8. Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso é negado para a conta de armazenamento

1. Digite *myVmPublic* na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal.
2. Quando **myVmPublic** for exibido nos resultados da pesquisa, selecione-o.
3. Conclua as etapas 1 a 6 em [Confirmar acesso à conta de armazenamento](#confirm-access-to-storage-account) para a VM *myVmPublic*.

   Após uma breve espera, você verá um erro `New-PSDrive : Access is denied`. O acesso é negado porque a VM *myVmPublic* é implantada na sub-rede *Pública*. A sub-rede *Pública* não tem um ponto de extremidade de serviço habilitado para Armazenamento do Azure. A conta de armazenamento só permite o acesso à rede a partir da sub-rede *Privada*, não da sub-rede *Pública*.

4. Feche a sessão da área de trabalho remota para a VM *myVmPublic*.

5. No computador, vá para o [portal](https://portal.azure.com) do Azure.
6. Insira o nome da conta de armazenamento que você criou na caixa **Pesquisar recursos, serviços e documentos**. Quando o nome da conta de armazenamento for exibido nos resultados da pesquisa, selecione-o.
7. Selecionar **Arquivos**.
8. Você receberá o erro mostrado na seguinte figura:

   ![Erro de acesso negado](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   Acesso negado porque o computador não está na sub-rede *Privada* da rede virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou um ponto de extremidade de serviço para uma sub-rede de rede virtual. Você aprendeu que pode habilitar pontos de extremidade de serviço para recursos implantados a partir de vários serviços do Azure. Você criou uma conta de Armazenamento do Azure e acesso restrito à rede para a conta de armazenamento apenas para os recursos em uma sub-rede de rede virtual. Para saber mais sobre pontos de extremidade de serviços, consulte [Visão geral de pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md) e [Gerenciar sub-redes](virtual-network-manage-subnet.md).

Se você tem várias redes virtuais na conta, convém conectar duas redes virtuais em conjunto para que os recursos de cada rede virtual possam se comunicar uns com os outros. Para saber mais sobre como conectar redes virtuais, siga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Conectar redes virtuais](./tutorial-connect-virtual-networks-portal.md)
