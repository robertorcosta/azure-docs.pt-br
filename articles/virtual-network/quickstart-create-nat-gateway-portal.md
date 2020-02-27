---
title: 'Início Rápido: Criar um gateway da NAT – portal do Azure'
titlesuffix: Azure Virtual Network NAT
description: Este início rápido mostra como criar um gateway da NAT usando o portal do Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 429c221609005136663d5e64a1b8650027cba411
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588732"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Início Rápido: Criar um gateway da NAT usando o portal do Azure

Este início rápido mostra como usar o serviço NAT de Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

>[!NOTE] 
>A NAT de Rede Virtual do Azure está disponível como versão prévia pública neste momento e em um conjunto limitado de [regiões](./nat-overview.md#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter detalhes.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de implantar uma VM e usar o gateway da NAT, precisamos criar o grupo de recursos e a rede virtual.  

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual** ou procure **Rede virtual** na pesquisa do Marketplace.

2. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myVNet**. |
    | Espaço de endereço | Insira **192.168.0.0/16**. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione Criar – **myResourceGroupNAT**. |
    | Location | Selecione **Leste dos EUA 2**.|
    | Sub-rede – Nome | Insira **mySubnet**. |
    | Sub-rede – Intervalo de endereços | Insira **192.168.0.0/24**. |

3. Deixe o restante dos padrões e selecione **Criar**.

### <a name="create-a-vm-to-use-the-nat-gateway"></a>Criar uma VM para usar o gateway da NAT

Agora criaremos uma VM para usar o serviço NAT. Essa VM tem um IP público para ser usado como um IP público em nível de instância para que você possa acessar a VM. O serviço NAT reconhece a direção do fluxo e substituirá o destino da Internet padrão em sua sub-rede. O endereço IP público da VM não será usado para conexões de saída.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Ubuntu Server 18.04 LTS** ou procure **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: Digite **myVM**.
   - **Detalhes da Instância** > **Região** > selecione **Leste dos EUA 2**.
   - **Conta de administrador** > **Tipo de autenticação**: Selecione **Senha**.
   - **Conta de administrador** > Insira as informações de **Nome de usuário**, **Senha** e **Confirmar senha**.
   - **Regras de porta de entrada** > **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Regras de porta de entrada** > **Selecionar portas de entrada**: Selecione **SSH (22)**
   - Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.

3. Na guia **Rede** verifique se os itens abaixo estão selecionados:
   - **Rede virtual**: **myVnet**
   - **Sub-rede**: **mySubnet**
   - **IP Público** > selecione **Criar**.  Na janela **Criar endereço IP público**, digite **myPublicIPVM** no campo **Nome** e escolha **Standard** para o **SKU**.  Clique em **OK**.
   - **Grupo de segurança de rede da NIC**: Selecione **Basic**.
   - **Porta de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecionar as portas de entrada**: Confirme se **SSH** está selecionado.

4. Na guia **Gerenciamento**, em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.

5. Selecione **Examinar + criar**. 

6. Examine as configurações e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

Você pode usar um ou mais recursos de endereço IP público, prefixos IP público ou ambos. Adicionaremos um recurso de IP público, um prefixo IP público e um recurso de gateway da NAT.

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Endereço IP público** ou procure **Endereço IP público** na pesquisa do Marketplace.

2. Em **Criar endereço IP público**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Padrão**.
    | Nome | Insira **myPublicIP**. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **myResourceGroupNAT**. |
    | Location | Selecione **Leste dos EUA 2**.|

3. Deixe o restante dos padrões e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Prefixo IP público** ou procure **Prefixo IP público** na pesquisa do Marketplace. 

2. Em **Criar um prefixo IP público**, insira ou selecione os seguintes valores na guia **Configurações básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**>
   - **Detalhes da Instância** > **Nome**: Digite **myPublicIPprefix**.
   - **Detalhes da instância** > **Região**: Selecione **Leste dos EUA 2**.
   - **Detalhes da instância** > **Tamanho do prefixo**: Selecione **/31 (2 endereços)**

3. Mantenha o restante dos padrões e selecione **Examinar + criar**.

4. Examine as configurações e selecione **Criar**.
   

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **gateway da NAT** ou procure **gateway da NAT** na pesquisa do Marketplace.

2. Em **Criar gateway da NAT (conversão de endereços de rede)** , digite ou selecione os seguintes valores na guia **Noções básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da instância** > **Nome do gateway da NAT**: Digite **myNATgateway**.
   - **Detalhes da instância** > **Região**: Selecione **Leste dos EUA 2**.
   - **Detalhes da instância** > **Tempo limite de ociosidade (minutos)** : Digite **10**.
   - Selecione a guia **IP Público** ou selecione **Avançar: IP Público**.

3. Na guia **IP Público**, digite ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIP**.
   - **Prefixos IP públicos**: Selecione **myPublicIPprefix**.
   - Selecione a guia **Sub-rede** ou selecione **Avançar: Sub-rede**.

4. Na guia **Sub-rede**, digite ou selecione os seguintes valores:
   - **Rede virtual**: Selecione **myResourceGroupNAT** > **myVnet**.
   - **Nome da sub-rede**: Selecione a caixa ao lado de **mySubnet**.

5. Selecione **Examinar + criar**.

6. Examine as configurações e selecione **Criar**.

## <a name="discover-the-ip-address-of-the-vm"></a>Descobrir o endereço IP da VM

1. No lado esquerdo do portal, selecione **Grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVM**.
4. Em **Visão geral**, copie o valor do **Endereço IP público** e cole-o no bloco de notas para usá-lo e acessar a VM.

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para poder usá-lo a fim de acessar a VM.

## <a name="sign-in-to-vm"></a>Entrar na VM

Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Agora você está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o gateway da NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway da NAT e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um gateway da NAT e uma VM para usá-lo. 

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  A exaustão de recursos de portas SNAT é resolvida adicionando mais recursos de endereço IP público, de prefixo IP público ou ambos.


- Saiba mais sobre a [NAT de Rede Virtual do Azure](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).
- [Forneça comentários sobre a versão prévia pública](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

