---
title: 'Início rápido: conectar-se a uma máquina virtual usando um endereço IP privado: bastiões do Azure'
description: Neste artigo, saiba como criar um host de bastiões do Azure de uma máquina virtual e conectar-se com segurança usando um endereço IP privado.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619261"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Início rápido: conectar-se a uma máquina virtual usando um endereço IP privado e a bastiões do Azure

Este artigo de início rápido mostra como se conectar a uma máquina virtual usando um endereço IP privado. Quando você se conecta por meio de bastiões, suas máquinas virtuais não precisam de um endereço IP público. As etapas neste artigo ajudam você a implantar a bastiões em sua rede virtual por meio de sua máquina virtual no Portal. Depois que o serviço é provisionado, a experiência de RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual.

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual do Azure.
* Uma máquina virtual do Azure localizada na rede virtual com a porta 3389 aberta.

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Name |  VNet1Bastion |
| Região | eastus |
| Rede virtual |  VNet1 |
| + Nome da sub-rede | AzureBastionSubnet |
| Endereços AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar Novo |
| Nome do endereço IP público | VNet1BastionPIP  |
| SKU de endereço IP público |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Crie um host bastião

Quando você cria um host de bastiões no portal usando uma máquina virtual existente, várias configurações padrão automaticamente correspondem à sua máquina virtual e/ou rede virtual.

1. Abra o [portal do Azure](https://portal.azure.com). Vá para sua máquina virtual e clique em **conectar**.

   ![configurações de máquina virtual](./media/quickstart-host-portal/vm-settings.png)
1. No menu suspenso, selecione **bastiões**.
1. Na página conectar, selecione **usar bastiões**.

   ![selecionar bastiões](./media/quickstart-host-portal/select-bastion.png)

1. Na página de bastiões, preencha os seguintes campos de configuração:

   * **Nome**: nomear o host de bastiões
   * **Sub-rede**: a sub-rede dentro de sua rede virtual para a qual o recurso de bastiões será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. O nome permite que o Azure saiba em qual sub-rede implantar o recurso de bastiões. Isso é diferente de uma sub-rede de gateway. Use uma sub-rede de pelo menos/27 ou maior (/27,/26,/25 e assim por diante).
   
      * Selecione **gerenciar configuração de sub-rede**e, em seguida, selecione **+ sub-rede**.
      * Na página Adicionar sub-rede, digite **AzureBastionSubnet**.
      * Especifique o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Selecione **OK** para criar a sub-rede. Na parte superior da página, navegue de volta para a bastiões para concluir o restante das configurações.

         ![Navegue até configurações de bastiões](./media/quickstart-host-portal/navigate-bastion.png)
   * **Endereço IP público**: esse é o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um novo IP público ou use um existente. O endereço IP público deve estar na mesma região que o recurso de bastiões que você está criando.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
1. Na tela validação, clique em **criar**. Aguarde cerca de 5 minutos para o recurso de bastiões criar e implantar.

   ![criar host de bastiões](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Conectar

Depois que a bastiões tiver sido implantada na rede virtual, a tela será alterada para a página conectar.

1. Digite o nome de usuário e a senha para sua máquina virtual. Em seguida, selecione **conectar**.

   ![connect](./media/quickstart-host-portal/connect.png)
1. A conexão RDP com essa máquina virtual via bastiões será aberta diretamente no portal do Azure (sobre o HTML5) usando a porta 443 e o serviço de bastiões.

   ![Conexão RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar a rede virtual e as máquinas virtuais, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *TestRG1* na caixa de **pesquisa** na parte superior do portal e selecione **TestRG1** nos resultados da pesquisa.

2. Selecione **Excluir grupo de recursos**.

3. Insira *TestRG1* para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um host de bastiões para sua rede virtual e, em seguida, se conectou a uma máquina virtual com segurança por meio do host de bastiões.

* Para saber mais sobre a bastiões do Azure, leia a [visão geral de bastiões](bastion-overview.md) e as [perguntas frequentes sobre bastiões](bastion-faq.md).
* Para usar grupos de segurança de rede com a sub-rede de bastiões do Azure, confira [trabalhar com NSGs](bastion-nsg.md).
* Para obter instruções que contenham explicações das configurações do host de bastiões do Azure, consulte o [tutorial](bastion-create-host-portal.md).
* Para se conectar a um conjunto de dimensionamento de máquinas virtuais, consulte [conectar-se a um conjunto de dimensionamento de máquinas virtuais usando a bastiões do Azure](bastion-connect-vm-scale-set.md).