---
title: Configure as regras de balanceamento e saída de carga usando o portal Azure
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar as regras de balanceamento de carga e saída no Standard Load Balancer usando o portal Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590007"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configure as regras de balanceamento e saída de carga no Balanceador de Carga Padrão usando o portal Azure

Este artigo mostra como configurar regras de saída no Standard Load Balancer usando o portal Azure.  

O recurso balanceador de carga contém duas extremidades frontais e suas regras associadas. Você tem uma frente para tráfego de entrada e outra frente para o tráfego de saída.  

Cada front-end faz referência a um endereço IP público. Neste cenário, o endereço IP público para tráfego de entrada é diferente do endereço para tráfego de saída.   A regra de balanceamento de carga fornece apenas balanceamento de carga de entrada. A regra de saída controla a tradução de endereço de rede de saída (NAT) para o VM.  

O cenário usa duas piscinas de back-end: uma para tráfego de entrada e outra para tráfego de saída. Esses pools ilustram a capacidade e fornecem flexibilidade para o cenário.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Nesta seção, você cria um balanceador de carga que carregará máquinas virtuais de equilíbrio. Você pode criar um balanceador de carga pública ou um balanceador de carga interna. Quando você cria um balanceador de carga pública, você cria um novo endereço IP público configurado como frontend para o balanceador de carga. O frontend será chamado **LoadBalancerFrontEnd** por padrão.

1. No lado superior esquerdo da tela, selecione Criar um**balanceador**de carga**de** > rede **de recursos** > .
2. Na guia Básico da página **Criar balanceador** de carga, digite ou selecione as **seguintes** informações:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite **myResourceGroupSLB** na caixa de texto.|
    | Nome                   | **myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard** ou **Básico**. A Microsoft recomenda o Standard para cargas de trabalho de produção. |
    | Endereço IP público | Selecione **Criar novo**. Se você tiver um IP público existente que você gostaria de usar, selecione **Usar existente**.  O IP público existente deve ser **o Padrão** SKU.  Os IPs públicos básicos não são compatíveis com **o balanceador de** carga Standard SKU.  |
    | Nome do endereço IP público              | Digite **myPublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zone-redundante** para criar um Balanceador de carga resiliente. Para criar um Load Balancer zonal, selecione uma zona específica de 1, 2 ou 3 |

3. Aceite os padrões para o resto da configuração.
4. Selecione **Revisão + criar**

    > [!IMPORTANT]
    > O resto deste quickstart assume que **o Standard** SKU é escolhido durante o processo de seleção SKU acima.

5. Na guia **Examinar + criar**, selecione **Criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end, uma investigação de integridade, e especifica uma regra de balanceamento.

### <a name="create-a-backend-pool"></a>Crie um pool de back-end

Um pool de endereços backend contém os endereços IP dos NICs virtuais no pool de backend. Crie o pool de endereços de back-end **myBackendPool** para incluir máquinas virtuais para balanceamento de carga de tráfego da Internet.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
3. Na página **Adicionar um pool de back-end**, insira **myBackendPool** como o nome para o pool de back-end e depois selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Uma sonda de saúde é usada para monitorar o status do seu aplicativo. A sonda de saúde adiciona ou remove VMs do balanceador de carga com base em sua resposta a verificações de saúde. Crie uma investigação de integridade **myHealthProbe** para monitorar a integridade das VMs.

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
 - Configuração de IP frontend para o tráfego de entrada.
 - O pool de IP backend para receber o tráfego.
 - O porto de origem e destino necessário. 

Na seção a seguir, você cria um:
 - Regra do balanceador de **carga myHTTPRule** para ouvir a porta 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Pool de endereços backend **myBackEndPool** também usando a porta 80. 

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
3. Use estes valores para configurar a regra de balanceamento de carga:
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Pool de back-end | Selecione **myBackendPool**.|
    | Investigação de integridade | Selecione **myHealthProbe**. |
    | Criar regras implícitas de saída | Selecione **Não**. Criaremos regras de saída em uma seção posterior usando um IP público dedicado. |
4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regra de saída
As regras de saída do balanceador de carga configuram SNAT de saída para VMs no pool de backend. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Crie um endereço IP público de saída e frontend

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações,** selecione **configuração IP do Frontend**e **selecione Adicionar**.

3. Use esses valores para configurar a configuração ip do frontend para saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Digite **LoadBalancerFrontEndOutbound**. |
    | Versão IP | Selecione **IPv4**. |
    | Tipo IP | Selecione **endereço IP**.|
    | Endereço IP público | Selecione **Criar novo**. No **Adicionar um endereço IP público,** digite **myPublicIPOutbound**.  Selecione **OK**. |

4. Selecione **Adicionar**.

### <a name="create-an-outbound-backend-pool"></a>Criar um pool de backend de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página Adicionar uma página **de pool backend,** para nome, digite **myBackendPoolOutbound**, como o nome do seu pool de backend e, em seguida, selecione **Adicionar**.

### <a name="create-outbound-rule"></a>Criar uma regra de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações,** **selecione 'Saída' de regras**e selecione **Adicionar**.

3. Use esses valores para configurar as regras de saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Digite **myOutboundRule**. |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEndOutbound**. |
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15 minutos.|
    | TCP Reset | Selecione **Habilitado**.|
    | Pool de back-end | Selecione **myBackendPoolOutbound** |
    | Alocação de portas -alocação de > port | Selecione **Escolher manualmente o número das portas de saída** |
    | Portas de saída -> Escolha por | Selecionar **Portas por instância** |
    | Portas de saída -> portas por instância | Digite **10.000**. |

4. Selecione **Adicionar**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupSLB** que contém o balanceador de carga e, em seguida, **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo:
 - Você criou um balanceador de carga padrão.
 - Configurado regras de tráfego de balanceador de carga de entrada e saída.
 - Configurei uma sonda de saúde para as VMs na piscina back-end. 

Para saber mais, continue com os [tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
