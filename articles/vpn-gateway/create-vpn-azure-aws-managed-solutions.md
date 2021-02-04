---
title: Criar uma VPN entre o Azure e o AWS usando soluções gerenciadas
description: Como criar uma conexão VPN entre o Azure e o AWS usando soluções gerenciadas, em vez de VMs ou dispositivos.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/03/2021
ms.author: ricmart
ms.openlocfilehash: 82161ac92566f6589ebfb64dddc221e296ec0992
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539121"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Criar uma conexão VPN entre o Azure e o AWS usando soluções gerenciadas

Você pode estabelecer uma conexão entre o Azure e o AWS usando soluções gerenciadas. Anteriormente, era necessário usar um dispositivo ou uma VM atuando como respondente. Agora, você pode conectar o gateway privado virtual do AWS ao gateway de VPN do Azure diretamente sem precisar se preocupar com o gerenciamento de recursos de IaaS, como máquinas virtuais. Este artigo ajuda você a criar uma conexão VPN entre o Azure e o AWS usando apenas soluções gerenciadas.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagrama de arquitetura":::

## <a name="configure-azure"></a>Configurar o Azure

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Configurar uma rede virtual. Para obter instruções, consulte o [início rápido da rede virtual](../virtual-network/quick-create-portal.md).

Os seguintes valores de exemplo são usados neste artigo:

* **Grupo de recursos:** RG-Azure-AWS
* **Região:** Leste dos EUA
* **Nome da rede virtual:** vnet-Azure
* **Espaço de endereço IPv4:** 172.10.0.0/16
* **Nome da sub-rede:** subnet-01
* **Intervalo de endereços da sub-rede:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN

Crie um gateway de VPN para sua rede virtual. Para obter instruções, consulte [tutorial: criar e gerenciar um gateway de VPN](tutorial-create-gateway-portal.md).

Os seguintes valores de exemplo e configurações são usados neste artigo:

* **Nome do gateway:** VPN-Azure-AWS
* **Região:** Leste dos EUA
* **Tipo de gateway:** VPN
* **Tipo de VPN:** Baseado em rotas
* **SKU:** VpnGw1
* **Geração:** Geração 1
* **Rede virtual:** Deve ser a VNet para a qual você deseja criar o gateway.
* **Intervalo de endereços da sub-rede do gateway:** 172.10.0.0/27
* **Endereço IP público:** Criar Novo
* **Nome do endereço IP público:** Pip-VPN-Azure-AWS
* **Habilitar o modo ativo-ativo:** Desativar
* **Configurar BGP:** Desativar

Exemplo:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Resumo do gateway de rede virtual":::

## <a name="configure-aws"></a>Configurar AWS

1. Crie a nuvem privada virtual (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Criar informações do VPC":::

1. Crie uma sub-rede dentro do VPC (rede virtual).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Criar a sub-rede":::

1. Crie um gateway de cliente que aponte para o endereço IP público do gateway de VPN do Azure. O **gateway do cliente** é um recurso AWS que contém informações para AWS sobre o dispositivo de gateway do cliente, que nesse caso é o gateway de VPN do Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Criar gateway de cliente":::

1. Crie o gateway privado virtual.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Criar gateway privado virtual":::

1. Anexe o gateway privado virtual ao VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Anexar o VPG ao VPC":::

1. Selecione o VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Attach":::

1. Crie uma conexão VPN site a site.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Criar conexão VPN":::

1. Defina a opção de roteamento como **estática** e aponte para o prefixo de sub-rede do Azure-01 **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Definindo uma rota estática":::

1. Depois de preencher as opções, **crie** a conexão.

1. Baixe o arquivo de configuração. Para baixar a configuração correta, altere o fornecedor, a plataforma e o software para **genérico**, pois o Azure não é uma opção válida.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Baixar configuração":::

1. O arquivo de configuração contém a chave pré-compartilhada e o endereço IP público para cada um dos dois túneis IPsec criados pelo AWS.

   **Túnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Túnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Configuração do túnel 1":::

   **Túnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Túnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Configuração do túnel 2":::

1. Depois que os túneis forem criados, você verá algo semelhante a este exemplo.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Detalhes da conexão VPN AWS":::

## <a name="create-local-network-gateway"></a>Criar gateway de rede local

No Azure, o gateway de rede local é um recurso do Azure que normalmente representa uma localização no local. Ele é preenchido com informações usadas para se conectar ao dispositivo VPN local. No entanto, nessa configuração, o gateway de rede local é criado e populado com as informações de conexão do gateway privado virtual AWS. Para obter mais informações sobre gateways de rede local do Azure, consulte [configurações de gateway de VPN do Azure](vpn-gateway-about-vpn-gateway-settings.md#lng).

Crie um gateway de rede local no Azure. Para obter as etapas, consulte [criar um gateway de rede local](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Especifique os seguintes valores:

* **Nome:** No exemplo, usamos LNG-Azure-AWS.
* **Ponto de extremidade:** Endereço IP
* **Endereço IP:** O endereço IP público do gateway virtual privado do AWS e o prefixo CIDR do VPC. Você pode encontrar o endereço IP público no arquivo de configuração que você baixou anteriormente.

O AWS cria dois túneis IPsec para fins de alta disponibilidade. O exemplo a seguir mostra o endereço IP público do túnel IPsec #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Gateway de rede local":::

## <a name="create-vpn-connection"></a>Criar conexão VPN

Nesta seção, você cria a conexão VPN entre o gateway de rede virtual do Azure e o gateway AWS.

1. Crie a conexão do Azure. Para obter as etapas para criar uma conexão, consulte [criar uma conexão VPN](tutorial-site-to-site-portal.md#CreateConnection).

   No exemplo a seguir, a chave compartilhada foi obtida do arquivo de configuração que você baixou anteriormente. Neste exemplo, usamos os valores para o túnel IPsec #1 criado por AWS e descritos no arquivo de configuração.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Objeto de conexão do Azure":::

1. Exibir a conexão. Depois de alguns minutos, a conexão é estabelecida.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Conexão de trabalho":::

1. Verifique se o #1 de túnel IPsec AWS está **ativo**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Verifique se o túnel AWS está ativo":::

1. Edite a tabela de rotas associada ao VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Editar a rota":::

1. Adicione a rota à sub-rede do Azure. Essa rota percorrerá o gateway do VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Salvar a configuração de rota":::

## <a name="configure-second-connection"></a>Configurar a segunda conexão

Nesta seção, você cria uma segunda conexão para garantir a alta disponibilidade.

1. Crie outro gateway de rede local que aponte para o endereço IP público do túnel IPsec #2 em AWS. Este é o gateway em espera.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Criar o gateway de rede local":::

1. Crie a segunda conexão VPN do Azure para o AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Criar a conexão de gateway de rede local em espera":::

1. Exiba as conexões de gateway de VPN do Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Status da conexão do Azure":::

1. Exiba as conexões AWS. Neste exemplo, você pode ver que as conexões agora são estabelecidas.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Status da conexão do AWS":::

## <a name="to-test-connections"></a>Para testar conexões

1. Adicione um **Gateway de Internet** ao VPC no AWS. O gateway de Internet é uma conexão lógica entre uma VPN da Amazon e a Internet. Esse recurso permite que você se conecte por meio da VM de teste do IP público do AWS por meio da Internet. Esse recurso não é necessário para a conexão VPN. Estamos apenas usando-o para testar.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Criar o gateway de Internet":::

1. Selecione **anexar ao VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Anexando o gateway de Internet ao VPC":::

1. Selecione um VPC e **anexe o gateway de Internet**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Anexar o gateway":::

1. Crie uma rota para permitir conexões a **0.0.0.0/0** (Internet) por meio do gateway de Internet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Configurar a rota por meio do gateway":::

1. No Azure, a rota é criada automaticamente. Você pode verificar a rota da VM do Azure selecionando **vm > rede > interface de rede > rotas efetivas**. Você vê 2 rotas, 1 rota por conexão.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Verificar as rotas em vigor":::

1. Você pode testá-lo de uma VM do Linux no Azure. O resultado aparecerá semelhante ao exemplo a seguir.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Visão geral do Azure da VM Linux":::

1. Você também pode testar isso de uma VM do Linux em AWS. O resultado aparecerá semelhante ao exemplo a seguir.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Visão geral de AWS da VM Linux":::

1. Teste a conectividade da VM do Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Teste de ping do Azure":::

1. Teste a conectividade da VM AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Teste de ping de AWS":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o suporte do AWS para IKEv2, consulte o [artigo AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).

* Para obter mais informações sobre como criar uma VPN de nuvem em escala, consulte o vídeo [criar a melhor VPN de multinuvem em escala](https://www.youtube.com/watch?v=p7h-frLDFE0).
