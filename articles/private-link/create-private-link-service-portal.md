---
title: Início Rápido – Criar um serviço de Link Privado usando o portal do Azure
titlesuffix: Azure Private Link
description: Saiba como criar um serviço de Link Privado usando o portal do Azure neste Início Rápido
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026199"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Início Rápido: Criar um serviço de Link Privado usando o portal do Azure

O serviço de Link Privado do Azure é a referência para seu serviço que é desenvolvido com o Link Privado do Azure. O serviço ou recurso que está operando por trás do Azure Load Balancer pode ser habilitado para acesso de Link Privado. Os consumidores do seu serviço podem acessar o serviço de maneira privada das próprio VNets deles. Neste Início Rápido, você aprenderá a criar um serviço de Link Privado usando o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Primeiro, crie uma rede virtual e, em seguida, um balanceador de carga interno para usar com o serviço de Link Privado do Azure.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma Rede Virtual do Microsoft Azure e a sub-rede para hospedar o balanceador de carga usado para acessar o recurso de Link Privado.


1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
   
1. No painel **Criar rede virtual**, insira ou selecione estes valores:
   
   - **Name**: digite **MyVNet**.
   - **ResourceGroup**: selecione **Criar novo**, insira **MyResourceGroupLB** e selecione **OK**. 
   - **Sub-rede** > **Nome**: digite **MyBackendSubnet**.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Criar um Standard Load Balancer

Crie um balanceador de carga Standard interno usando o portal. O nome e o endereço IP que você criar serão configurados automaticamente como o front-end do balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione *MyResourceGroupLB* na caixa suspensa.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Leste dos EUA 2**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Selecione **Padrão**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereço IP              | Selecione **Estático**.   |
    | Endereço IP privado|digite um endereço que está no espaço de endereço de sua rede virtual e sub-rede, por exemplo, *10.3.0.7*.  |

3. Na guia **Revisar + criar**, clique em **Criar**. 
   

### <a name="create-standard-load-balancer-resources"></a>Criar recursos do Standard Load Balancer

Nesta seção, você definirá as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, além de especificar as regras do balanceador de carga.

#### <a name="create-a-backend-pool"></a>Crie um pool de back-end

Para distribuir o tráfego aos seus recursos, um pool de endereços de back-end contém os endereços IP dos NICs virtuais conectados ao Load Balancer. Crie o pool de endereços de back-end *myBackendPool* para incluir recursos para balanceamento de carga de tráfego.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
3. Na página **Adicionar um pool de back-end**, insira *myBackendPool* como o nome para o pool de back-end e depois selecione **Adicionar**.



#### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status dos recursos, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente recursos da rotação do Load Balancer com base na resposta às verificações de integridade. 

**Para criar uma investigação de integridade para monitorar a integridade dos recursos:**

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar uma investigação de integridade**, insira ou selecione os seguintes valores:
   
   - **Name**: insira **MyHealthProbe**.
   - **Protocolo**: Abra a lista suspensa e selecione **TCP**. 
   - **Porta**: digite **80**. 
   - **Intervalo**: digite **15**. O intervalo é o número de segundos entre as tentativas de investigação.
   - **Limite não íntegro**: digite **2**. Esse valor é a quantidade de falhas de investigação consecutivas que ocorrem antes que uma VM seja considerada não íntegra.
   
1. Selecione **OK**.

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balanceador de carga define como o tráfego é distribuído para os recursos. A regra define a configuração de IP de front-end para o tráfego de entrada, o pool de IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de balanceador de carga chamada **MyLoadBalancerRule** escuta a porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede ao pool de endereços de back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra de balanceador de carga:**

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar regra de balanceamento de carga**, digite ou selecione os seguintes valores, se ainda não existirem:
   
   - **Name**: digite **MyLoadBalancerRule**.
   - **Endereço IP de front-end:** digite **LoadBalancerFrontEnd** se não houver nenhum.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: digite **80**.
   - **Porta de back-end**: digite **80**.
   - **Pool de back-end**: selecione **MyBackendPool**.
   - **Investigação de integridade**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado

Nesta seção, você criará um serviço de link privado por trás de um Standard Load Balancer.

1. No lado superior esquerdo da tela no portal do Azure, selecione **Criar um recurso** > **Rede** > **Central de Link Privado (Versão Prévia)** . Você também pode pesquisar o Link Privado por meio da pesquisa do portal.

2. Em **Central de Link Privado – Visão geral**, na opção **"Expor seu serviço para que outras pessoas possam se conectar"** , selecione Iniciar.

3. Em **Criar um serviço de Link Privado – Noções básicas**, insira ou selecione estas informações:

    | Configuração           | Valor                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Detalhes do projeto:**  |                                                                              |
    | Subscription      | Selecione sua assinatura                                                     |
    | Grupo de recursos    | Selecione *MyResourceGroupLB*                                                     |
    | **DETALHES DA INSTÂNCIA:** |                                                                              |
    | Nome              | Insira *myPrivateLinkService* |
    | Região            | Selecione *Leste dos EUA 2*                                                         |

4. Selecione **Avançar: Configurações de saída**.

5. Em **Criar um serviço de Link Privado – Configurações de saída**, insira ou selecione estas informações:


    | Configuração                           | Valor                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Selecione *MyLoadBalancer*                                                           |
    | Endereço IP de front-end do Azure Load Balancer | Selecione o endereço IP de front-end de MyLoadBalancer                                |
    | Rede virtual NAT de origem        | Selecione *myVNET*                                                                   |
    | Sub-rede da NAT de origem                 | Selecione *myBackendSubnet*                                                          |
    | Habilitar o proxy TCP v2               | Selecione SIM/NÃO dependendo se o aplicativo está esperando o cabeçalho de proxy de TCP v2 |
    | Configurações de endereço IP privado       | Configurar o método de alocação e o endereço IP para cada IP de NAT                  |

6. Selecione **Avançar: Segurança de acesso**.

7. Em **Criar um serviço de Link Privado – Segurança de acesso**, insira ou selecione estas informações:

    | Configuração                                     | Valor                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Visibilidade                                  | Escolha *Somente controle de acesso baseado em função*         |
  
8. Selecione **Avançar: Marcas** e, em seguida, **Examinar + criar** ou escolha a guia **Examinar + criar** na parte superior da página.

9. Examine suas informações e selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando você terminar de usar o serviço de link privado, exclua o grupo de recursos para limpar os recursos usados neste início rápido:

1. Insira *myResourceGroupLB* na caixa **Pesquisar** na parte superior do portal e selecione *myResourceGroupLB* nos resultados da pesquisa. 
2. Selecione **Excluir grupo de recursos**. 
3. Insira myResourceGroup em **DIGITAR O NOME DO GRUPO DE RECURSOS** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um balanceador de carga interno do Azure e um serviço de link privado. Para saber mais sobre como criar pontos de extremidade privados, confira [Criar pontos de extremidade privados usando o portal do Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
