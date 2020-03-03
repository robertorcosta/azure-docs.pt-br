---
title: Filtrar o tráfego da Internet de entrada com o DNAT do Firewall do Azure usando o portal
description: Neste tutorial, você aprenderá a implantar e configurar o DNAT do Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/26/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1528087ced54ddcab2e3dd44b65fb3411cae3004
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621782"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego da Internet de entrada com o DNAT do Firewall do Azure usando o portal do Azure

Você pode configurar o DNAT (conversão de endereço de rede de destino) do Firewall do Azure para converter e filtrar o tráfego da Internet de entrada para as sub-redes. Quando você configura o modo DNAT, a ação da coleção de regras da NAT é definida como **Dnat**. Assim, cada regra na coleção de regras NAT pode ser usada para converter o IP e a porta públicos em um IP e porta privados do firewall. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](rule-processing.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Testar o firewall

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, você criará duas redes virtuais emparelhadas:

- **VN-Hub** - o firewall está nessa rede virtual.
- **VN-Spoke** - o servidor de carga de trabalho está nessa rede virtual.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página inicial do portal do Azure, clique em **Grupos de recursos** e clique em **Adicionar**.
3. Em **Nome do grupo de recursos**, digite **RG-DNAT-Test**.
4. Em **Assinatura**, selecione sua assinatura.
5. Em **Local do grupo de recursos**, selecione um local. Todos os recursos criados depois disso devem estar no mesmo local.
6. Clique em **Criar**.

## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede

Primeiro, crie as redes virtuais e, em seguida, emparelhe-as.

### <a name="create-the-hub-vnet"></a>Criar a rede virtual do hub

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **VN-Hub**.
5. Em **Espaço de endereço**, digite **10.0.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**.

     O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
     > [!NOTE]
     > O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Em **Intervalo de endereços**, digite **10.0.1.0/26**.
11. Use as outras configurações padrão e clique em **Criar**.

### <a name="create-a-spoke-vnet"></a>Criar uma rede virtual do spoke

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **VN-Spoke**.
5. Em **Espaço de endereço**, digite **192.168.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Em **Sub-rede**, em **Nome**, digite **SN-Workload**.

    O servidor estará nessa sub-rede.
10. Em **Intervalo de endereços**, digite **192.168.1.0/24**.
11. Use as outras configurações padrão e clique em **Criar**.

### <a name="peer-the-vnets"></a>Emparelhar as redes virtuais

Agora, emparelhe as duas redes virtuais.

#### <a name="hub-to-spoke"></a>Do hub para o spoke

1. Clique na rede virtual **VN-Hub**.
2. Em **Configurações**, clique em **Emparelhamentos**.
3. Clique em **Adicionar**.
4. Digite **Peer-HubSpoke** para o nome.
5. Escolha **VN-Spoke** para a rede virtual.
6. Clique em **OK**.

#### <a name="spoke-to-hub"></a>Do spoke para o hub

1. Clique na rede virtual **VN-Spoke**.
2. Em **Configurações**, clique em **Emparelhamentos**.
3. Clique em **Adicionar**.
4. Digite **Peer-SpokeHub** para o nome.
5. Escolha **VN-Hub** para a rede virtual.
6. Clique em **Permitir tráfego encaminhado**.
7. Clique em **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual de carga de trabalho e coloque-a na sub-rede **SN-Workload**.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Computação**, clique em **Máquinas virtuais**.
3. Clique em **Adicionar**, clique em **Windows Server**, clique em **Windows Server 2016 Datacenter**e clique em **Criar**.

**Noções básicas**

1. Em **Nome**, digite **Srv-Workload**.
5. Digite um nome de usuário e uma senha.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, clique em **Usar existente** e escolha **RG-DNAT-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Clique em **OK**.

**Tamanho**

1. Escolha um tamanho apropriado para uma máquina virtual de teste que executa o Windows Server. Por exemplo, **B2ms** (8 GB de RAM, 16 GB de armazenamento).
2. Clique em **Selecionar**.

**Configurações**

1. Em **Rede**, em **Rede Virtual**, escolha **VN-Spoke**.
2. Em **Sub-rede**, escolha **SN-Workload**.
3. Clique em **Endereço IP público** e, em seguida, clique em **None**.
4. Em **Selecionar portas de entrada públicas**, escolha **Nenhuma porta de entrada pública**. 
2. Deixe as outras configurações padrão e clique em **OK**.

**Resumo**

Revise o resumo e clique em **Criar**. Isso levará alguns minutos para ser concluído.

Após a conclusão da implantação, observe o endereço IP privado da máquina virtual. Ele será usado mais tarde ao configurar o firewall. Clique no nome da máquina virtual e, em **Configurações**, clique em **Rede** para localizar o endereço IP privado.

## <a name="deploy-the-firewall"></a>Implantar o firewall

1. Na página inicial do portal, clique em **Criar um recurso**.
2. Clique em **Rede**e, após **Em destaque**, clique em **Ver todos**.
3. Clique em **Firewall**e clique em **Criar**. 
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

   |Configuração  |Valor  |
   |---------|---------|
   |Nome     |FW-DNAT-test|
   |Subscription     |\<sua assinatura\>|
   |Resource group     |**Usar existente**: RG-DNAT-Test |
   |Location     |Selecionar o mesmo local usado anteriormente|
   |Escolher uma rede virtual     |**Usar existente**: VN-Hub|
   |Endereço IP público     |**Criar novo**. O endereço IP público deve ser do tipo SKU Standard.|

5. Clique em **Revisar + Criar**.
6. Reveja o resumo e clique em **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
7. Após a implantação ser concluída, vá para o grupo de recursos **RG-DNAT-Test** e clique no firewall **FW-DNAT-test**.
8. Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **SN-Workload**, configure a rota de saída padrão para atravessar o firewall.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Tabelas de rotas**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **RT-FWroute**.
5. Em **Assinatura**, selecione sua assinatura.
6. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
7. Em **local**, selecione o mesmo local usado anteriormente.
8. Clique em **Criar**.
9. Clique em **Atualizar** e, em seguida, clique na tabela de rotas **RT-FWroute**.
10. Clique em **Sub-redes** e em **Associar**.
11. Clique em **Rede virtual**e escolha **VN-Spoke**.
12. Em **Sub-rede**, clique em **SN-Workload**.
13. Clique em **OK**.
14. Clique em **Rotas** e em **Adicionar**.
15. Em **Nome da rota**, digite **FW-DG**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
18. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
19. Clique em **OK**.

## <a name="configure-a-nat-rule"></a>Configurar uma regra NAT

1. Abra o **RG-DNAT-Test** e clique no firewall **FW-DNAT-test**. 
2. Na página **FW-DNAT-test**, em **Configurações**, clique em **Regras**. 
3. Clique em **Adicionar coleção de regras do NAT**. 
4. Para **Nome**, digite **RC-DNAT-01**. 
5. Digite **200** em **Prioridade**. 
6. Em **Regras**, para **Nome**, digite **RL-01**.
7. Em **Protocolo**, selecione **TCP**.
8. Para **Endereços de Origem**, digite *. 
9. Para **Endereços de Destino** digite o endereço IP público do firewall. 
10. Para **Portas de Destino**, digite **3389**. 
11. Para **Endereço Convertido** digite o endereço IP privado da máquina virtual Srv-Workload. 
12. Para **Porta traduzida**, digite **3389**. 
13. Clique em **Adicionar**. 

## <a name="test-the-firewall"></a>Testar o firewall

1. Conecte uma área de trabalho remota ao endereço IP público do firewall. Você deve estar conectado à máquina virtual **Srv-Workload**.
2. Feche a área de trabalho remota.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **RG-DNAT-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Testar o firewall

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
