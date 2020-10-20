---
title: 'Início Rápido: Criar um Bastion host de uma VM e se conectar por meio de um endereço IP privado'
titleSuffix: Azure Bastion
description: Neste artigo de início rápido, saiba como criar um host do Azure Bastion de uma máquina virtual e se conectar com segurança usando um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019045"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Início Rápido: Conectar-se a uma máquina virtual usando um endereço IP privado e o Azure Bastion

Este artigo de início rápido mostrará como se conectar a uma máquina virtual por meio do navegador usando o Azure Bastion e o portal do Azure. No portal do Azure, será possível implantar o Bastion em sua rede virtual da VM do Azure. Depois de implantar o Bastion, você poderá se conectar à VM por meio de seu respectivo endereço IP privado usando o portal do Azure. Sua VM não precisará obter um endereço IP público nem um software especial. Uma vantagem de criar um Bastion host para a VNet diretamente da VM é obter várias configurações que serão previamente preenchidas para você.

Depois que o serviço é provisionado, a experiência de RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para obter mais informações sobre o Azure Bastion, confira [O que é o Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual.
* Uma máquina virtual do Windows na rede virtual.
* As seguintes funções obrigatórias:
  * A função de leitor na máquina virtual.
  * A função de leitor na placa de interface de rede com endereço IP privado da máquina virtual.

* Portas: Para se conectar à VM, você deverá abrir as seguintes portas nela:
  * Portas de entrada: RDP (3389)

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Nome |  TestVNet1-bastion |
| Rede virtual |  TestVNet1 (com base na VM) |
| + Nome da Sub-rede | AzureBastionSubnet |
| Endereços da AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar |
| Nome do endereço IP público | VNet1BastionPIP  |
| SKU do endereço IP público |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um bastion host

Ao criar um bastion host no portal do Azure usando uma máquina virtual existente, várias configurações serão padronizadas de maneira automática para corresponder à sua máquina virtual e/ou rede virtual.

1. Abra o [Portal do Azure](https://portal.azure.com). Acesse sua máquina virtual e clique em **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="configurações da máquina virtual" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Na lista suspensa, selecione **Bastion**.
1. Na **página TestVM | Conectar**, selecione **Usar o Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="configurações da máquina virtual" border="false":::

1. Na página **Bastion**, preencha os seguintes campos de configurações:

   * **Nome**: nomeie o bastion host.
   * **Sub-rede**: a sub-rede na rede virtual na qual o recurso do Bastion será implantado. A sub-rede precisa ser criada com o nome **AzureBastionSubnet**. O nome permite que o Azure saiba em qual sub-rede os recursos do Bastion serão implantados. Ele será diferente de uma sub-rede de gateway. Use uma sub-rede de pelo menos /27 ou maior (/27,/26, /25 e assim por diante).
   
      * Clique em **Gerenciar a configuração da sub-rede**.
      * Selecione o **AzureBastionSubnet**.
      * Se necessário, ajuste o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Não ajuste nenhuma outra configuração. Clique em **OK** para aceitar e salvar as alterações de sub-rede ou clique em um **x** na parte superior da página caso não queira fazer nenhuma alteração.
1. Clique no botão voltar no navegador para voltar para a página **Bastion** e continuar especificando os valores.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
   * **Endereço IP público**: esse é o IP público do recurso do Bastion no qual o RDP/SSH será acessado (pela porta 443). Criar um IP público.
1. Clique em **Criar** para criar o bastion host. O Azure validará suas configurações e criará o host. O host e seus respectivos recursos levarão cerca de 5 minutos para serem criados e implantados.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="configurações da máquina virtual":::

## <a name="connect"></a><a name="connect"></a>Conectar

Depois que o Bastion tiver sido implantado na rede virtual, a tela será alterada para a página Conectar.

1. Insira o nome de usuário e a senha da sua máquina virtual. Em seguida, selecione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="configurações da máquina virtual":::
1. A conexão RDP com essa máquina virtual via Bastion será aberta diretamente no portal do Azure (via HTML5) usando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="configurações da máquina virtual":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira o nome do grupo de recursos na caixa **Pesquisar** na parte superior do portal e selecione-o nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira seu grupo de recursos para obter a opção **DIGITAR O NOME DO GRUPO DE RECURSOS** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um host do Bastion para sua rede virtual e, em seguida, se conectou a uma máquina virtual com segurança por meio do host do Bastion. Depois será possível continuar com a etapa a seguir caso queira se conectar a um conjunto de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Como se conectar a um conjunto de dimensionamento de máquinas virtuais usando o Azure Bastion](bastion-connect-vm-scale-set.md)
