---
title: Início Rápido – Criar um serviço de Link Privado usando o portal do Azure
titlesuffix: Azure Private Link
description: Learn how to create a Private Link service by using the Azure portal in this quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252541"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Início Rápido: Criar um serviço de Link Privado usando o portal do Azure

Um serviço de Link Privado do Azure se refere ao seu próprio serviço gerenciado pelo Link Privado. Você pode conceder acesso ao Link Privado ao serviço ou recurso que opera por trás do Azure Standard Load Balancer. Os consumidores do seu serviço podem acessá-lo em particular a partir de suas próprias redes virtuais. Neste Início Rápido, você aprenderá a criar um serviço de Link Privado usando o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Primeiro, crie uma rede virtual. Em seguida, crie um balanceador de carga interno para usar com o serviço de Link Privado.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta seção, você criará uma rede virtual. Você também cria a sub-rede para hospedar o balanceador de carga que acessa seu serviço de Link Privado.

Nesta seção, você precisará substituir os seguintes parâmetros nas etapas pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Leste dos EUA 2      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Use o portal para criar um balanceador de carga Standard interno. O nome e o endereço IP que você especificar serão configurados automaticamente como o front-end do balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.

1. Na guia **Básico** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | **Assinatura**               | Selecione sua assinatura.    |
    | **Grupo de recursos**         | Selecione **myResourceGroupLB** na caixa.|
    | **Nome**                   | Insira **myLoadBalancer**.                                   |
    | **Região**         | Selecione **Leste dos EUA 2**.                                        |
    | **Tipo**          | selecione **Interno**.                                        |
    | **SKU**           | Selecione **Padrão**.                          |
    | **Rede virtual**           | Selecione **myVNet**.                          |
    | **Atribuição de endereço IP**              | Selecione **Estático**.   |
    | **Endereço IP privado**|Insira um endereço que está no espaço de endereço de sua rede virtual e sub-rede. Um exemplo é 10.3.0.7.  |

1. Aceite os padrões para as demais configurações e selecione **Revisar + criar**

1. Na guia **Revisar + criar**, selecione **Criar**.

### <a name="create-standard-load-balancer-resources"></a>Criar recursos do Standard Load Balancer

Nesta seção, você define as configurações de balanceador de carga para um pool de endereços de back-end e uma investigação de integridade. Especifique também o balanceador de carga.

#### <a name="create-a-back-end-pool"></a>Criar um pool de back-end

Um pool de endereços de back-end contém os endereços IP dos NICs virtuais conectados ao balanceador de carga. Esse pool permite distribuir o tráfego para seus recursos. Crie o pool de endereços de back-end denominado **myBackendPool** para incluir recursos para balanceamento de carga de tráfego.

1. Selecione **Todos os serviços** no menu mais à esquerda.
1. Selecione **Todos os recursos**, e **myLoadBalancer** na lista de recursos.
1. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
1. Na página **Adicionar um pool de back-end**, insira **myBackendPool** como o nome para o pool de back-end e depois selecione **Adicionar**.

#### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Use uma investigação de integridade para permitir que o balanceador de carga monitore o status dos recursos. Com base na resposta às verificações de integridade, a investigação de integridade adiciona ou remove dinamicamente recursos da rotação do balanceador de carga.

Para criar uma investigação de integridade para monitorar a integridade dos recursos:

1. Selecione **Todos os recursos** no menu mais à esquerda e **myLoadBalancer** na lista de recursos.

1. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.

1. Na página **Adicionar uma investigação de integridade**, insira ou selecione os seguintes valores:

   - **Name**: Insira **myHealthProbe**.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: Insira **80**.
   - **Intervalo**: Insira **15**. Esse valor é o número de segundos entre as tentativas de investigação.
   - **Limite não íntegro**: Insira **2**. Esse valor é a quantidade de falhas de investigação consecutivas que ocorrem antes que uma máquina virtual seja considerada não íntegra.

1. Selecione **OK**.

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balanceador de carga define como o tráfego é distribuído para os recursos. A regra define:

- A configuração de IP de front-end para tráfego de entrada.
- O pool de IP de back-end para receber o tráfego.
- As portas de origem e de destino necessárias.

A regra de balanceador de carga chamada **MyLoadBalancerRule** escuta a porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede para o pool de endereços de back-end **myBackendPool** na mesma porta 80.

Para criar uma regra do balanceador de carga:

1. Selecione **Todos os recursos** no menu mais à esquerda e **myLoadBalancer** na lista de recursos.

1. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.

1. Na página **Adicionar regra de balanceamento de carga**, insira ou selecione os seguintes valores, se ainda não existirem:

   - **Name**: Insira **myLoadBalancerRule**.
   - **Endereço IP de front-end:** Insira **LoadBalancerFrontend**.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: Insira **80**.
   - **Porta de back-end**: Insira **80**.
   - **Pool de back-end**: Selecione **myBackendPool**.
   - **Investigação de integridade**: Selecione **myHealthProbe**. 

1. Selecione **OK**.

## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado

Nesta seção, você cria um serviço de link privado por trás de um balanceador de carga padrão.

1. No lado superior esquerdo dapágina no portal do Azure, selecione **Criar um recurso** > **Rede** > **Central de Link Privado (Versão Prévia)** . Você também pode usar a caixa de pesquisa do portal para procurar o Link Privado.

1. Em **Central de Link Privado – Visão geral** > **Expor seu serviço para que outras pessoas possam se conectar**, selecione **Iniciar**.

1. Em **Criar um serviço de Link Privado – Noções básicas**, insira ou selecione estas informações:

    | Configuração           | Valor                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Detalhes do projeto:  |                                                                              |
    | **Assinatura**      | Selecione sua assinatura.                                                     |
    | **Grupo de recursos**    | Selecione **myResourceGroupLB**.                                                    |
    | Detalhes da instância: |                                                                              |
    | **Nome**              | Insira **myPrivateLinkService**. |
    | **Região**            | Selecione **Leste dos EUA 2**.                                                        |

1. Selecione **Avançar: Configurações de saída**.

1. Em **Criar um serviço de Link Privado – Configurações de saída**, insira ou selecione estas informações:

    | Configuração                           | Valor                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Balanceador de Carga**                     | Selecione **myLoadBalancer**.                                                           |
    | **Endereço IP de front-end do Azure Load Balancer** | Selecione o endereço IP de front-end de **myLoadBalancer**.                                |
    | **Rede virtual NAT de origem**        | Selecione **myVNet**.                                                                   |
    | **Sub-rede da NAT de origem**                 | Selecione **myBackendSubnet**.                                                          |
    | **Habilitar o proxy TCP v2**               | Selecione **SIM** ou **NÃO** dependendo se o aplicativo está esperando o cabeçalho de proxy de TCP v2. |
    | **Configurações de endereço IP privado**       | Configurar o método de alocação e o endereço IP para cada IP de NAT.                  |

1. Selecione **Avançar: Segurança de acesso**.

1. Em **Criar um serviço de Link Privado – Segurança de acesso**, selecione **Visibilidade** e, em seguida, escolha **Somente controle de acesso baseado em função**.
  
1. Selecione **Avançar: Marcas** > **Revisar + criar** ou escolha a guia **Revisar + criar** na parte superior da página.

1. Examine suas informações e selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar o serviço de link privado, exclua o grupo de recursos para limpar os recursos usados neste início rápido.

1. Insira **myResourceGroupLB** na caixa Pesquisar na parte superior do portal e selecione **myResourceGroupLB** nos resultados da pesquisa.
1. Selecione **Excluir grupo de recursos**.
1. Em **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup**.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um balanceador de carga interno do Azure e um serviço de link privado. Você também pode saber como [criar um ponto de extremidade privado usando o portal do Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
