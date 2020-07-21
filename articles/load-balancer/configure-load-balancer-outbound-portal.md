---
title: Configurar o balanceamento de carga e as regras de saída usando o portal do Azure
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar o balanceamento de carga e as regras de saída no Standard Load Balancer usando o portal do Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 0484bc393ac97dc88fed5858f736f01fc41b507a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521047"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configurar o balanceamento de carga e as regras de saída no Standard Load Balancer usando o portal do Azure

Este artigo mostra como configurar regras de saída no Standard Load Balancer usando o portal do Azure.  

O recurso de balanceador de carga contém dois front-ends e suas regras associadas. Você tem um front-end para tráfego de entrada e outro front-end para tráfego de saída.  

Cada front-end faz referência a um endereço IP público. Nesse cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para o tráfego de saída.   A regra de balanceamento de carga fornece apenas o balanceamento de carga de entrada. A regra de saída controla a NAT (conversão de endereços de rede) de saída para a VM.  

O cenário usa dois pools de back-end: um para o tráfego de entrada e outro para o tráfego de saída. Esses pools ilustram a funcionalidade e fornecem flexibilidade para o cenário.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Nesta seção, você criará um balanceador de carga que balanceará a carga de máquinas virtuais. Você pode criar um balanceador de carga público ou um balanceador de carga interno. Ao criar um balanceador de carga público, você cria um novo endereço IP público que é configurado como o front-end para o balanceador de carga. O front-end será nomeado **LoadBalancerFrontEnd** por padrão.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as seguintes informações:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite **myResourceGroupSLB** na caixa de texto.|
    | Nome                   | **myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Padrão**. |
    | Endereço IP público | Selecione **Criar novo**. Se você tiver um IP público existente que deseja usar, selecione **usar existente**.  O IP público existente deve ser o SKU **padrão** .  Os IPs públicos básicos não são compatíveis com o balanceador de carga SKU **padrão** .  |
    | Nome do endereço IP público              | Digite **myPublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione com **redundância de zona** para criar um Load Balancer resiliente. Para criar um Load Balancer zonal, selecione uma zona específica de 1, 2 ou 3 |

3. Aceite os padrões para o restante da configuração.
4. Selecione **revisão + criar**

    > [!IMPORTANT]
    > O restante deste início rápido pressupõe que o SKU **Standard** é escolhido durante o processo de seleção de SKU acima.

5. Na guia **Examinar + criar**, selecione **Criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end, uma investigação de integridade, e especifica uma regra de balanceamento.

### <a name="create-a-backend-pool"></a>Crie um pool de back-end

Um pool de endereços de back-end contém os endereços IP das NICs virtuais no pool de back-end. Crie o pool de endereços de back-end **myBackendPool** para incluir máquinas virtuais para balanceamento de carga de tráfego da Internet.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
3. Na página **Adicionar um pool de back-end**, insira **myBackendPool** como o nome para o pool de back-end e depois selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Uma investigação de integridade é usada para monitorar o status do seu aplicativo. A investigação de integridade adiciona ou remove VMs do balanceador de carga com base em sua resposta às verificações de integridade. Crie uma investigação de integridade **myHealthProbe** para monitorar a integridade das VMs.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Intervalo | Insira **15** para o número de **Intervalo** em segundos entre tentativas de investigação. |
    | Limite não íntegro | Insira **2** para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. 

Você define o:
 - Configuração de IP de front-end para o tráfego de entrada.
 - O pool de IPS de back-end para receber o tráfego.
 - A porta necessária de origem e de destino. 

Na seção a seguir, você cria um:
 - Regra do balanceador de carga **myHTTPRule** para escutar a porta 80.
 - **LoadBalancerFrontEnd**de front-end.
 - O pool de endereços de back-end também **myBackEndPool** usando a porta 80. 

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
3. Use estes valores para configurar a regra de balanceamento de carga:
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Protocolo | selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Pool de back-end | Selecione **myBackendPool**.|
    | Investigação de integridade | Selecione **myHealthProbe**. |
    | Criar regras de saída implícitas | Selecione **Não**. Criaremos regras de saída em uma seção posterior usando um IP público dedicado. |
4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regra de saída
As regras de saída do balanceador de carga configuram SNAT de saída para VMs no pool de back-end. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Criar um endereço IP público de saída e front-end

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **configurações**, selecione **configuração de IP de front-end**e selecione **Adicionar**.

3. Use estes valores para definir a configuração de IP de front-end para saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **LoadBalancerFrontEndOutbound**. |
    | Versão IP | Selecione **IPv4**. |
    | Tipo de IP | Selecione o **endereço IP** ou o **prefixo IP**.|
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Adicionar um endereço IP público**, insira **myPublicIPOutbound**. </br> Se estiver usando o prefixo de IP, em **Adicionar um prefixo de IP público**, insira **myPublicIPPrefixOutbound**. Escolha um **tamanho de prefixo** para o prefixo IP público </br> Selecione **OK**.  |

4. Selecione **Adicionar**.

### <a name="create-an-outbound-backend-pool"></a>Criar um pool de back-end de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página **Adicionar um pool de back-end** , em nome, digite **myBackendPoolOutbound**, como o nome do pool de back-end e, em seguida, selecione **Adicionar**.

### <a name="create-outbound-rule"></a>Criar uma regra de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **configurações**, selecione **regras de saída**e, em seguida, selecione **Adicionar**.

3. Use estes valores para configurar as regras de saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myOutboundRule**. |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEndOutbound**. |
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15 minutos**.|
    | Redefinição de TCP | Selecione **Habilitado**.|
    | Pool de back-end | Selecionar **myBackendPoolOutbound** |
    | Alocação de porta – alocação de porta > | Selecione **escolher manualmente o número de portas de saída** |
    | Portas de saída-> escolher por | Selecionar **portas por instância** |
    | Portas de saída-portas > por instância | Insira **10.000**. |

4. Selecione **Adicionar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupSLB** que contém o balanceador de carga e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo:
 - Você criou um balanceador de carga padrão.
 - Configuradas regras de tráfego de balanceador de carga de entrada e saída.
 - Configurada uma investigação de integridade para as VMs no pool de back-ends. 

Para saber mais, continue nos [tutoriais para Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
