---
title: Configure endereços IP privados para VMs - Portal Azure
description: Saiba como configurar endereços IP para máquinas virtuais usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461541"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configure um endereço IP privado para uma VM usando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de amostra a seguir esperam que um ambiente simples já seja criado. Se você quiser executar as etapas como elas são exibidas neste documento, primeiro [crie uma rede virtual](quick-create-portal.md#create-a-virtual-network). No entanto, na etapa 3, use esses valores em vez disso:

| Configuração | Valor |
| ------- | ----- |
| Nome | *TestVNet* |
| Espaço de endereço | *192.168.0.0/16* |
| Resource group | **TestRG** (se necessário, selecione **Criar novo** para criá-lo) |
| Sub-rede – Nome | *Frontend* |
| Sub-rede – Intervalo de endereços | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Crie uma VM para testar endereços IP privados estáticos
Quando você cria um modo de implantação do VM in Resource Manager, você não pode definir um endereço IP privado estático usando o portal Azure. Em vez disso, você cria o VM primeiro. Em seguida, você pode definir seu IP privado para ser estático.

Para criar uma VM chamada *DNS01* na sub-rede *FrontEnd* de uma rede virtual chamada *TestVNet,* siga estas etapas:

1. No menu [portal Do Azure,](https://portal.azure.com) selecione **Criar um recurso**.

    ![Crie um recurso, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selecione a máquina Virtual **computacional** > **Virtual machine**.

    ![Crie o VM, portal Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. No **Básico,** especifique valores para itens descritos na tabela a seguir. Em **seguida, selecione Next&nbsp;&nbsp;: Discos** e, em **seguida, Next&nbsp;:&nbsp;Networking**.

    | Item | Valor |
    | --- | --- |
    | **Assinatura** | Sua assinatura atual |
    | **Grupo de recursos** | **TestRG** (selecione na lista de isato) |
    | **Nome da máquina virtual** | *DNS01* |
    | **Região** | **EUA (Leste dos EUA)** |
    | **Imagem** | **Windows Server 2019 Datacenter** |
    | **Tamanho** | **Tamanho vm** de **B1ls**, **Oferta** de **Padrão** |
    | **Nome de usuário** | O nome de usuário da sua conta administradora |
    | **Senha** | A senha para o nome de usuário da sua conta administradora |
    | **Confirmar senha** | A senha novamente |

    ![Guia básica, Crie uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Em **Rede,** especifique valores para itens conforme descrito na tabela a seguir e selecione **Next**.

    | Item | Valor |
    | --- | --- |
    | **Rede virtual** | **TestVNet** |
    | **Sub-rede** | **Frontend** |

    ![Guia de rede, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Em **Gerenciamento,** em **Conta de armazenamento Diagnósticos,** escolha **vnetstorage**. Se essa conta de armazenamento não aparecer na lista, selecione **Criar novo,** especifique um **nome** de *vnetstorage*e selecione **OK**. Por fim, selecione **'Criar'&nbsp;+&nbsp;**

    ![Guia de gerenciamento, Crie uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Em **'Revisar + criar',** revise as informações de visão geral e selecione **Criar**.

    ![Revisar + criar guia, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A mensagem a seguir aparece assim que a VM é criada.

![Mensagem de conclusão de implantação, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recupere informações privadas de endereço IP para uma VM
Para visualizar as informações privadas de endereço IP para sua nova VM:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar sua VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

    ![Máquinas virtuais, caixa de pesquisa, portal Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecione o nome do seu novo VM **(DNS01).**

    ![Lista de máquinas virtuais, portal Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Escolha **Rede**e selecione a única interface de rede listada.

    ![Interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Escolha **configurações IP**e selecione a configuração IP listada na tabela.

    ![Configuração IP, interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Em **configurações privadas de endereço IP,** na rede/sub-rede virtual **TestVNet/FrontEnd,** observe o valor **de Atribuição** **(Dinâmico** ou **Estático)** e o **endereço IP**.

    ![Atribuição dinâmica ou estática, configurações de endereço IP privadas antigas, configuração IP, interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Adicionar um endereço IP privado estático a uma VM existente
Para adicionar um endereço IP privado estático à sua nova VM:

1. Na página de configuração IP, defina a atribuição do seu endereço IP privado como **Estático**.
2. Altere seu **endereço IP** privado para *192.168.1.101*e selecione **Salvar**.
   
    ![Atribuição dinâmica ou estática, novas configurações privadas de endereço IP, configuração IP, interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se você notar depois de selecionar **Salvar** que a atribuição ainda está definida como **Dynamic,** o endereço IP digitado já está em uso. Tente outro endereço IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático da VM:

Na página de configuração IP, defina a atribuição do seu endereço IP privado como **Dinâmico**e selecione **Salvar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP no sistema operacional

De dentro do sistema operacional de uma VM, você não deve atribuir estáticamente o IP *privado* atribuído à VM do Azure. Só faça atribuição estática de um IP privado quando for necessário, como ao [atribuir muitos endereços IP a VMs](virtual-network-multiple-ip-addresses-portal.md). Se você definir manualmente o endereço IP privado dentro do sistema operacional, certifique-se de que ele corresponda ao endereço IP privado atribuído à interface de [rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Do Zure . Caso contrário, você pode perder a conectividade com a VM. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private).

Além disso, você nunca deve atribuir manualmente o endereço IP *público* atribuído a uma máquina virtual Do Zure dentro do sistema operacional da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md).
