---
title: 'Criar um host Azure Bastion: portal'
description: Neste artigo, saiba como criar um host Azure Bastion usando o portal
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366136"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Crie um host Azure Bastion usando o portal

Este artigo mostra como criar um host Azure Bastion usando o portal Azure. Uma vez que você disponibilize o serviço Azure Bastion em sua rede virtual, a experiência RDP/SSH perfeita está disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Você pode criar um novo recurso de host de bastião no portal especificando todas as configurações manualmente ou usando as configurações que correspondem a uma VM existente. Para criar um host de bastião usando as configurações de VM, consulte o artigo [quickstart.](quickstart-host-portal.md) Opcionalmente, você pode usar [o Azure PowerShell](bastion-create-host-powershell.md) para criar um host Azure Bastion.

## <a name="before-you-begin"></a>Antes de começar

Bastião está disponível nas seguintes regiões públicas do Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Crie um host bastião

Esta seção ajuda você a criar um novo recurso Do Azure Bastion a partir do portal Azure.

1. No menu do [portal Azure](https://portal.azure.com) ou na **página inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** no campo *Pesquisar o Mercado,* digite **Bastion,** em seguida, clique **em Enter** para chegar aos resultados da pesquisa.

1. A partir dos resultados, clique em **Bastion**. Verifique se o fornecedor é a *Microsoft* e se a categoria é *Rede*.

1. Na página **Bastião,** clique **em Criar** para abrir a página **Criar um bastião.**

1. Na **página Criar um bastião,** configure um novo recurso Bastion. Especifique as configurações do recurso Bastion.

    ![criar um bastião](./media/bastion-create-host-portal/settings.png)

    * **Assinatura**: A assinatura do Azure que você deseja usar para criar um novo recurso Bastion.
    * **Grupo de recursos**: O grupo de recursos do Azure no qual o novo recurso Bastion será criado. Se você não tiver um grupo de recursos existente, você pode criar um novo.
    * **Nome**: O nome do novo recurso Bastião
    * **Região**: A região pública do Azure em que o recurso será criado.
    * **Rede virtual**: A rede virtual na qual o recurso Bastion será criado. Você pode criar uma nova rede virtual no portal durante esse processo, ou usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, certifique-se de que a rede virtual existente tenha espaço de endereço livre suficiente para acomodar os requisitos da sub-rede Bastion.
    * **Sub-rede**: A sub-rede em sua rede virtual para a qual o novo recurso de host Bastion será implantado. Você deve criar uma sub-rede usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba para qual sub-rede implantar os recursos do Bastion. Isto é diferente de uma sub-rede gateway. Você deve usar uma sub-rede de pelo menos 27 ou maior (/27, /26, e assim por diante).
    
       Crie **a AzureBastionSubnet** sem quaisquer tabelas de rota ou delegações. Se você usar Grupos de Segurança de Rede na **AzureBastionSubnet,** consulte o artigo [Work with NSGs.](bastion-nsg.md)
    * **Endereço IP público**: O IP público do recurso Bastion no qual o RDP/SSH será acessado (sobre a porta 443). Crie um novo IP público ou use um já existente. O endereço IP público deve estar na mesma região que o recurso Bastion que você está criando.
    * **Nome do endereço IP público**: O nome do recurso público de endereço IP.
    * **Endereço IP público SKU**: Esta configuração está prepovoada por padrão para **Padrão**. O Azure Bastion usa/suporta apenas o Standard Public IP SKU.
    * **Atribuição**: Esta configuração é pré-povoada por padrão para **Estática**.

1. Quando terminar de especificar as configurações, clique em **'Revisar + Criar**. Isso valida os valores. Uma vez que a validação passe, você pode iniciar o processo de criação.
1. Na **página Criar um bastião,** clique em **Criar**.
1. Você verá uma mensagem informando que sua implantação está em andamento. O status será exibido nesta página à medida que os recursos forem criados. Leva cerca de 5 minutos para que o recurso Bastion seja criado e implantado.

## <a name="next-steps"></a>Próximas etapas

* Leia o [Bastion FAQ](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).