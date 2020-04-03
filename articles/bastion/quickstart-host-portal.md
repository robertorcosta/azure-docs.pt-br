---
title: 'Quickstart: Conecte-se a uma máquina virtual usando um endereço IP privado: Azure Bastion'
description: Neste artigo, aprenda a criar um host Azure Bastion a partir de uma máquina virtual e conecte-se com segurança usando um endereço IP privado.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619261"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Conecte-se a uma máquina virtual usando um endereço IP privado e o Bastião Do Azure

Este artigo quickstart mostra como se conectar a uma máquina virtual usando um endereço IP privado. Quando você se conecta via Bastion, suas máquinas virtuais não precisam de um endereço IP público. Os passos deste artigo ajudam você a implantar bastião em sua rede virtual através de sua máquina virtual no portal. Uma vez que o serviço é provisionado, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual.

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual do Azure.
* Uma máquina virtual Azure localizada na rede virtual com a porta 3389 aberta.

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Nome |  VNet1Bastion |
| Região | eastus |
| Rede virtual |  VNet1 |
| + Nome da sub-rede | AzureBastionSubnet |
| Endereços do AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar Novo |
| Nome do endereço IP público | VNet1BastionPIP  |
| Endereço IP público SKU |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Crie um host bastião

Quando você cria um host bastião no portal usando uma máquina virtual existente, várias configurações serão automaticamente padrão para corresponder à sua máquina virtual e/ou rede virtual.

1. Abra o [portal Azure.](https://portal.azure.com) Vá para sua máquina virtual e clique **em Conectar**.

   ![configurações da máquina virtual](./media/quickstart-host-portal/vm-settings.png)
1. Na queda, selecione **Bastião**.
1. Na página Conectar, selecione **Use Bastion**.

   ![selecionar Bastião](./media/quickstart-host-portal/select-bastion.png)

1. Na página Bastião, preencha os seguintes campos de configurações:

   * **Nome**: Nome do host bastião
   * **Sub-rede**: A sub-rede dentro da sua rede virtual para a qual o recurso Bastion será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. O nome permite que o Azure saiba para qual sub-rede implantar o recurso Bastion. Isto é diferente de uma sub-rede gateway. Use uma sub-rede de pelo menos 27 ou mais (/27, /26, /25, e assim por diante).
   
      * Selecione **Gerenciar a configuração da sub-rede**e selecione **+ Sub-rede**.
      * Na página Adicionar sub-rede, **digite AzureBastionSubnet**.
      * Especifique o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Selecione **OK** para criar a sub-rede. No topo da página, navegue de volta para Bastion para completar o resto das configurações.

         ![navegar para configurações bastião](./media/quickstart-host-portal/navigate-bastion.png)
   * **Endereço IP público**: Este é o IP público do recurso Bastion no qual o RDP/SSH será acessado (sobre a porta 443). Crie um novo IP público ou use um já existente. O endereço IP público deve estar na mesma região que o recurso Bastion que você está criando.
   * **Nome do endereço IP público**: O nome do recurso público de endereço IP.
1. Na tela de validação, clique em **Criar**. Aguarde cerca de 5 minutos para que o recurso Bastion crie e implante.

   ![criar host bastião](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Conectar

Depois que bastião foi implantado na rede virtual, a tela muda para a página de conexão.

1. Digite o nome de usuário e a senha da sua máquina virtual. Em seguida, selecione **Conectar**.

   ![conectar](./media/quickstart-host-portal/connect.png)
1. A conexão RDP a esta máquina virtual via Bastion será aberta diretamente no portal Azure (sobre HTML5) usando a porta 443 e o serviço Bastion.

   ![Conexão RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, exclua o grupo de recursos e todos os recursos que ele contém:

1. Digite *TestRG1* na caixa **de pesquisa** na parte superior do portal e selecione **TestRG1** nos resultados da pesquisa.

2. Selecione **Excluir grupo de recursos**.

3. Digite *TestRG1* para **digitar o nome do grupo de recursos** e **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste quickstart, você criou um host Bastion para sua rede virtual e, em seguida, conectado a uma máquina virtual com segurança através do host Bastion.

* Para saber mais sobre o Bastião do Azure, leia a [Visão Geral do Bastião](bastion-overview.md) e o [FAQ bastião.](bastion-faq.md)
* Para usar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
* Para obter instruções que contenham explicações sobre as configurações do host do Azure Bastion, consulte o [Tutorial](bastion-create-host-portal.md).
* Para conectar-se a um conjunto de escala de máquina virtual, consulte [Conecte-se a um conjunto de escala de máquina virtual usando o Azure Bastion](bastion-connect-vm-scale-set.md).