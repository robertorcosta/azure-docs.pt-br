---
title: Configure o conjunto de escala da máquina virtual com um azure Load Balancer - Portal Azure existente
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349716"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configure um conjunto de escala de máquina virtual com um Azure Load Balancer existente usando o portal Azure

Neste artigo, você aprenderá como configurar um conjunto de escala de máquina virtual com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga sku padrão existente na assinatura onde o conjunto de escala da máquina virtual será implantado.
- Uma Rede Virtual Azure para o conjunto de escala de máquinavirtual.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar conjunto de escala de máquina virtual com balanceador de carga existente

Nesta seção, você criará uma escala de máquina virtual definida no portal Azure com um balanceador de carga Azure existente.

> [!NOTE]
> As etapas a seguir supõem que uma rede virtual chamada **myVNet** e um balanceador de carga Do Azure chamado **myLoadBalancer** foi previamente implantado.

1. No lado superior esquerdo da tela, clique em Criar um conjunto de escala de**máquina virtual** de**computação** > de **recursos** > ou procurar a **escala da máquina virtual definida** na pesquisa de mercado.

2. Selecione **Criar**.

3. Em **Criar um conjunto de escala de máquina virtual,** digite ou selecione essas informações na guia **Básico:**

    | Configuração                        | Valor                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Detalhes do projeto**            |                                                                                                       |
    | Subscription                   | Selecionar sua assinatura do Azure                                                                        |
    | Grupo de recursos                 | Selecione Criar novo, digite **myResourceGroup**e selecione OK ou selecione um grupo de recursos existente. |
    | **Detalhes do conjunto de escalas**          |                                                                                                       |
    | Nome do conjunto de dimensionamento de máquinas virtuais | Digite **myVMSS**                                                                                      |
    | Região                         | Selecione **Leste dos EUA 2**                                                                                    |
    | Zona de disponibilidade              | Selecione **Nenhum**                                                                                       |
    | **Detalhes de instância**           |                                                                                                       |
    | Imagem                          | Selecione **o Servidor Ubuntu 18.04 LTS**                                                                    |
    | Exemplo do Azure Spot            | Selecione **Não**                                                                                         |
    | Tamanho                           | Sair à revelia                                                                                      |
    | **Conta do administrador**      |                                                                                                       |
    | Tipo de autenticação            | Selecione **Senha**                                                                                   |
    | Nome de Usuário                       | Digite seu nome de usuário de admin        |
    | Senha                       | Digite sua senha de admin    |
    | Confirmar senha               | Redigite sua senha de admin |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Criar conjunto de escala de máquina virtual." border="true":::

4. Selecione a guia **Rede.**

5. Digite ou selecione essas informações na guia **Rede:**

     Configuração                           | Valor                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configuração da rede virtual** |                                                          |
    | Rede virtual                   | Selecione **myVNet** ou sua rede virtual existente.      |
    | **Balanceamento de carga**                |                                                          |
    | Use um balanceador de carga               | Selecione **Sim**.                                           |
    | **Configurações do balanceamento de carga**       |                                                          |
    | Opções de balanceamento de carga            | Selecione **o balanceador de carga Azure**                           |
    | Selecione um balanceador de carga            | Selecione **myLoadBalancer** ou seu balanceador de carga existente |
    | Selecione um pool de back-end             | Selecione **myBackendPool** ou seu pool de backend existente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Criar conjunto de escala de máquina virtual." border="true":::

6. Selecione a guia **Gerenciamento.**

7. Na guia **Gerenciamento,** defina **os diagnósticos de inicialização** como **Desligados**.

8. Selecione o botão **'Revisão + criar'** azul.

9. Revise as configurações e selecione o botão **Criar.**

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e balanceador de carga, consulte:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
