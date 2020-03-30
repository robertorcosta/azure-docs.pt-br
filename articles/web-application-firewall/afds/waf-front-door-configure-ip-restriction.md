---
title: Configure a regra WAF de restrição de IP para porta frontal do Azure
description: Saiba como configurar uma regra do Firewall de aplicativos da Web para restringir endereços IP para um ponto final existente do Azure Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336094"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configure uma regra de restrição ip com um Firewall de aplicativo web para porta frontal do Azure

Este artigo mostra como configurar regras de restrição de IP em um WAF (Web Application Firewall) para o Azure Front Door usando o portal Azure, Azure CLI, Azure PowerShell ou um modelo do Azure Resource Manager.

Uma regra de controle de acesso baseada em endereço IP é uma regra WAF personalizada que permite controlar o acesso aos seus aplicativos web. Ele faz isso especificando uma lista de endereços IP ou faixas de endereçoIP no formato CIDR (Classless Inter-Domain Routing).

Por padrão, seu aplicativo web está acessível a partir da Internet. Se você quiser limitar o acesso aos clientes de uma lista de endereços IP conhecidos ou intervalos de endereços IP, você pode criar uma regra de correspondência ip que contém a lista de endereços IP como valores correspondentes e define o operador para "Não" (negar é verdade) e a ação para **Bloquear**. Depois que uma regra de restrição de IP é aplicada, as solicitações que se originam de endereços fora dessa lista permitida recebem uma resposta proibida 403.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configure uma política WAF com o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Crie um perfil da Porta da Frente do Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para um aplicativo web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

1. No portal Do Zure, selecione **Criar um recurso,** digitar **firewall de aplicativo web** na caixa de pesquisa e, em seguida, selecionar o **WAF (Web Application Firewall, firewall de aplicativos da Web)**.
2. Selecione **Criar**.
3. Na página Criar uma diretiva **WAF,** use os seguintes valores para concluir a guia **Básico:**
   
   |Configuração  |Valor  |
   |---------|---------|
   |Política para     |WAF global (Porta da Frente)|
   |Subscription     |Selecione sua assinatura|
   |Resource group     |Selecione o grupo de recursos onde está a porta da frente.|
   |Nome de política     |Digite um nome para sua política|
   |Estado da política     |habilitado|

   Selecione **A seguir: Configurações de diretiva**

1. Na **guia Configurações** de Diretiva, selecione **Prevenção**. Para o corpo de resposta do **bloco,** digite *Você foi bloqueado!* para que você possa ver que sua regra personalizada está em vigor.
2. Selecione **A seguir: Regras gerenciadas**.
3. Selecione **A seguir: Regras personalizadas**.
4. Selecione **Adicionar regra personalizada**.
5. Na página **Adicionar regra personalizada,** use os seguintes valores de teste para criar uma regra personalizada:

   |Configuração  |Valor  |
   |---------|---------|
   |Nome de regra personalizado     |Regra fdWafCust|
   |Status     |habilitado|
   |Tipo de regra     |Corresponder a|
   |Prioridade    |100|
   |Tipo de correspondência     |Endereço IP|
   |Variável de correspondência|Remoteaddr|
   |Operação|Não contém|
   |Endereço ou intervalo IP|10.10.10.0/24|
   |Então|Negar tráfego|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regra aduaneira":::

   Selecione **Adicionar**.
6. Selecione **A seguir: Associação**.
7. Selecione **Adicionar host frontend**.
8. Para **o host Frontend,** selecione o host frontend e selecione **Adicionar**.
9. Selecione **Revisão + criar**.
10. Depois que sua validação de diretiva passar, selecione **Criar**.

### <a name="test-your-waf-policy"></a>Teste sua política waf

1. Depois que a implantação da diretiva WAF estiver concluída, navegue até o nome do host front-end da porta da frente.
2. Você deve ver sua mensagem de bloqueio personalizada.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Teste de regra waf":::

   > [!NOTE]
   > Um endereço IP privado foi usado intencionalmente na regra personalizada para garantir que a regra seria acionada. Em uma implantação real, crie *regras de permitir* e *negar* usando endereços IP para sua situação particular.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configure uma política WAF com o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure seu ambiente CLI e crie um perfil do Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Configure o ambiente Azure CLI
1. Instale o [Azure CLI](/cli/azure/install-azure-cli)ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **Experimente nos** comandos CLI que se seguem e, em seguida, faça login na sua conta do Azure na sessão Cloud Shell que é aberta. Após o início `az extension add --name front-door` da sessão, entre para adicionar a extensão Azure Front Door.
 2. Se você estiver usando a CLI localmente em Bash, `az login`faça login no Azure usando .

#### <a name="create-an-azure-front-door-profile"></a>Crie um perfil da porta frontal do Azure
Crie um perfil da Porta da Frente do Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para um aplicativo web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Crie uma política WAF usando o comando [az network front-door waf-policy create.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) No exemplo a seguir, substitua o nome de política *IPAllowPolicyExampleCLI* por um nome de diretiva exclusivo.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicione uma regra de controle de acesso IP personalizada

Use o comando [az network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) para adicionar uma regra de controle de acesso IP personalizada para a política WAF que você acabou de criar.

Nos exemplos a seguir:
-  Substitua *o IPAllowPolicyExampleCLI* por sua política exclusiva criada anteriormente.
-  Substitua *o ip-address-range-1,* *ip-address-range-2* por seu próprio alcance.

Primeiro, crie uma regra de permitir IP para a política criada a partir da etapa anterior. 
> [!NOTE]
> **--adiar** é necessário porque uma regra deve ter uma condição de correspondência para ser adicionada na próxima etapa.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Em seguida, adicione a condição de correspondência à regra:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Encontre o ID de uma política WAF 
Encontre o ID de uma política waf usando o comando [waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) da porta da rede az. Substitua *ipAllowPolicyExampleCLI* no exemplo a seguir com sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Vincule uma diretiva WAF a um host front-end do Azure Front Door

Defina o ID da Porta frontal do *AzureFirewallPolicyLink* ID para o ID de diretiva usando o comando de atualização da porta frontal da [rede az.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Substitua *o IPAllowPolicyExampleCLI* por sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a diretiva WAF é aplicada ao **FrontendEndpoints[0]**. Você pode vincular a política waf a qualquer um dos seus front ends.
> [!Note]
> Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma diretiva WAF a um front-end do Azure Front Door. As atualizações de diretiva subseqüentes são aplicadas automaticamente no front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configure uma política WAF com o Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure seu ambiente PowerShell e crie um perfil do Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usam o modelo [azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure.

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar no PowerShell usando suas credenciais do Azure e, em seguida, instale o módulo Az.

1. Conecte-se ao Azure usando o seguinte comando e use uma caixa de diálogo interativa para fazer login.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo Azure Front Door, certifique-se de que você tenha a versão atual do módulo PowerShellGet instalada. Execute o seguinte comando e, em seguida, reabra o PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo Az.FrontDoor usando o seguinte comando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Crie um perfil da porta frontal do Azure
Crie um perfil da Porta da Frente do Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para um aplicativo web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Defina uma condição de correspondência IP
Use o comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) para definir uma condição de correspondência IP.
No exemplo a seguir, substitua *o ip-address-range-1,* *ip-address-range-2* por seu próprio alcance.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Criar uma regra de permitir IP personalizado

Use o comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) para definir uma ação e definir uma prioridade. No exemplo a seguir, as solicitações não de IPs de cliente que correspondam à lista serão bloqueadas.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configure uma diretiva WAF
Encontre o nome do grupo de recursos que contém `Get-AzResourceGroup`o perfil do Azure Front Door usando . Em seguida, configure uma diretiva WAF com a regra IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Vincule uma diretiva WAF a um host front-end do Azure Front Door

Vincule um objeto de diretiva WAF a um host front-end existente e atualize as propriedades do Azure Front Door. Primeiro, recupere o objeto Azure Front Door usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, defina a propriedade **WebApplicationFirewallPolicyLink** para o ID de recursos do *$IPAllowPolicyExamplePS,* criado na etapa anterior, usando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a diretiva WAF é aplicada ao **FrontendEndpoints[0]**. Você pode vincular uma política WAF a qualquer uma de suas extremidades frontais. Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma diretiva WAF a um front-end do Azure Front Door. As atualizações de diretiva subseqüentes são aplicadas automaticamente no front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configure uma diretiva WAF com um modelo de Gerenciador de recursos
Para visualizar o modelo que cria uma política do Azure Front Door e uma política WAF com regras de restrição ip personalizadas, vá para [o GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Próximas etapas

- Aprenda a [criar um perfil do Azure Front Door](../../frontdoor/quickstart-create-front-door.md).
