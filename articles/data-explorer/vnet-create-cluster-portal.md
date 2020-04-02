---
title: Crie um cluster azure Data Explorer & DB em sua rede virtual
description: Neste artigo, você aprende como criar um cluster Azure Data Explorer em sua rede virtual.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548893"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Crie um cluster Azure Data Explorer em sua rede virtual

O Azure Data Explorer suporta a implantação de um cluster em uma sub-rede em sua rede virtual (VNet). Esse recurso permite que você acesse o cluster privadamente a partir de sua rede virtual do Azure ou no local, acesse recursos como Event Hub e Storage dentro de sua rede virtual e restrinja o tráfego de entrada e saída.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Criar o NsG (Network Security Group, grupo de segurança de rede)

[Os Grupos de Segurança de Rede (NSG)](/azure/virtual-network/security-overview) fornecem a capacidade de controlar o acesso à rede dentro de um VNet. O Azure Data Explorer pode ser acessado usando dois pontos finais HTTPs (443) e TDS (1433). As seguintes regras do NSG devem ser configuradas para permitir o acesso a esses pontos finais para gerenciamento, monitoramento e operação adequada do seu cluster.

Para criar o grupo de segurança da rede:

1. Selecione o **+ Crie um** botão de recurso no canto superior esquerdo do portal.
1. Procure por *Network Security Group*.
1. Em **Network Security Group**, na parte inferior da tela, selecione **Criar**.
1. Na janela **criar grupo de segurança de rede,** preencha as seguintes informações.

   ![Criar formulário NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para seu cluster.|
    | Resource group | Seu grupo de recursos | Use um grupo de recursos existente ou crie um novo. |
    | Nome | AzureDataExplorerNsg | Escolha um nome que identifique o NSG (Network Security Group, grupo de segurança de rede) no grupo de recursos.  |
    | Região | *Oeste dos EUA* | Selecione a região que melhor atende às suas necessidades.
    | | | |

1. Selecione **Revisar + criar** para examinar os detalhes do cluster e **Criar** para provisionar o cluster.

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

    ![Ir para o recurso](media/vnet-create-cluster-portal/notification-nsg.png)

1. Na guia **'Inbound' security** rules,selecione **+Add**.
1. Na janela **de regra de segurança de entrada,** preencha as seguintes informações.

    ![Criar formulário de regra de entrada do NSG](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Configuração** | **Valor sugerido** 
    |---|---|
    | Fonte | Tag de Serviços
    | Marca de serviço de origem | AzureDataExplorerManagement
    | Intervalos de portas de origem | *
    | Destino | VirtualNetwork
    | Intervalos de portas de destino | *
    | Protocolo | TCP
    | Ação | Allow
    | Prioridade | 100
    | Nome | PermitirAZureDataExplorerManagement
    | | |
    
1. Repita as duas etapas anteriores para todas as dependências de entrada e saída de acordo com [as dependências para implantação do VNet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). Alternativamente, as regras de saída podem ser substituídas por uma regra única para *permitir* internet para as portas 443 e 80.
    
    As regras do NSG para dependências de entrada e saída devem ser assim:

    ![Regras NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Criar endereços IP públicos

Para criar os endereços IP públicos da consulta (Engine):

1. Selecione o **+ Crie um** botão de recurso no canto superior esquerdo do portal.
1. Procure por *Network Security Group*.
1. Em **endereço IP público,** na parte inferior da tela, selecione **Criar**.
1. No **painel Criar endereço IP público,** complete as seguintes informações.
   
   ![Criar formulário IP público](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Versão IP | IPv4 | Selecione a versão IP. Nós só apoiamos o IPv4.|
    | Sku | Standard | Exigimos ponto final uri **padrão** para consulta (Engine). |
    | Nome | motor pip | Escolha um nome que identifique seu endereço IP público no grupo de recursos.
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para o seu IP público.|
    | Resource group | Seu grupo de recursos | Use um grupo de recursos existente ou crie um novo. |
    | Location | *Oeste dos EUA* | Selecione a região que melhor atende às suas necessidades.
    | | | |

1. Selecione **Criar** para criar o endereço IP público.

1. Para criar o endereço IP público de ingestão (Data Management) siga as mesmas instruções e selecione 
    * **Sku**: Básico
    * **Atribuição de endereço IP:** Estática

## <a name="create-virtual-network-and-subnet"></a>Criar rede virtual e sub-rede

Para criar a rede virtual e a sub-rede:

1. Selecione o **+ Crie um** botão de recurso no canto superior esquerdo do portal.
1. Procure por *Rede Virtual*.
1. Em **Rede Virtual,** na parte inferior da tela, selecione **Criar**.
1. Na **janela Criar rede virtual,** complete as seguintes informações.

   ![Criar formulário de rede virtual](media/vnet-create-cluster-portal/vnet-blade.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para seu cluster.|
    | Resource group | Seu grupo de recursos | Use um grupo de recursos existente ou crie um novo. |
    | Nome | AzureDataExplorerVnet | Escolha um nome que identifique sua rede virtual no grupo de recursos.
    | Região | *Oeste dos EUA* | Selecione a região que melhor atende às suas necessidades.
    | | | |

    > [!NOTE]
    > Para cargas de trabalho de produção, planeje o tamanho da sub-rede de acordo com o [tamanho da sub-rede do plano em seu VNet](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. Selecione **Revisar + criar** para examinar os detalhes do cluster e **Criar** para provisionar o cluster.

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.
1. Vá para **a lâmina Subnets** e selecione a sub-rede **padrão.**
    
    ![Lâmina de subredes](media/vnet-create-cluster-portal/subnets.png)

1. Na janela da sub-rede **padrão:**
    1. Selecione o Grupo de Segurança de **Rede** no menu suspenso. 
    1. Selecione o nome do seu grupo de segurança de rede, neste **caso, AzureDataExplorerNsg**. 
    1. **Salvar**

    ![Configurar sub-rede](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Criar um cluster

Crie um cluster Azure Data Explorer com um conjunto definido de recursos de computação e armazenamento em um grupo de recursos do Azure, conforme descrito na [criação de um cluster](create-cluster-database-portal.md#create-a-cluster).

1. Antes de finalizar a criação de cluster, na janela **Criar um cluster Do Azure Data Explorer,** selecione a guia **Rede** para fornecer detalhes de rede virtuais usando os recursos criados nas guias anteriores:

   ![Criar forma vnet de cluster](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para recursos de rede.|
    | Rede Virtual | AzureDataExplorerVnet | Escolha a rede virtual criada nas etapas anteriores.
    | Sub-rede | default | Escolha a sub-rede criada nas etapas anteriores.
    | Consulta IP público | motor pip | Escolha o IP público de consulta criado nas etapas anteriores.
    | Ingestão de dados IP público | dm-pip | Escolha o IP público de ingestão criado nas etapas anteriores.
    | | | |

1. Selecione **'Revisar + criar'** para criar seu cluster.
1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

Para implantar o cluster Azure Data Explorer em sua rede virtual, use o [cluster Deploy Azure Data Explorer no](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modelo do VNet Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar o Azure Data Explorer em sua rede virtual](vnet-create-cluster-portal.md)