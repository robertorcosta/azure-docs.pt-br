---
title: Dimensionar e proteger rapidamente um aplicativo Web usando o Azure Front Door e o Firewall do Aplicativo Web do Azure (WAF) | Microsoft Docs
description: Este artigo ajuda você a entender como usar o Firewall do Aplicativo Web com o Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 6f91a98372aa85a52a6013a121235ca354004a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743534"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Dimensionar e proteger rapidamente um aplicativo Web usando o Azure Front Door e o Firewall do Aplicativo Web do Azure (WAF)

Nas últimas semanas, muitos aplicativos Web experimentaram um aumento rápido de tráfego relacionado à COVID-19. Além disso, esses aplicativos Web também estão observando um aumento no tráfego mal-intencionado, incluindo ataques de negação de serviço. Uma maneira eficaz de lidar com essas necessidades, escalar horizontalmente para aumentos de tráfego e proteger contra ataques, é configurar o Azure Front Door com o WAF do Azure como uma camada de aceleração, cache e segurança na frente do seu aplicativo Web. Este artigo fornece orientação sobre como obter rapidamente essa Azure Front Door com a configuração de WAF do Azure para qualquer aplicativo Web em execução dentro ou fora do Azure. 

Usaremos a CLI do Azure para configurar o WAF neste tutorial, mas todas essas etapas também têm suporte completo no portal do Azure, Azure PowerShell, ARM do Azure e nas APIs REST do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

As instruções neste blog usam a CLI (interface de linha de comando) do Azure. Exiba este guia de [introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Dica: uma maneira fácil e rápida de começar a usar a CLI do Azure é com o [Bash no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Verifique se a extensão de front door foi adicionada à sua CLI do Azure

```azurecli-interactive 
az extension add --name front-door
```

Observação: Para obter mais detalhes sobre os comandos listados abaixo, consulte a [referência de CLI do Azure para Front Door](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Etapa 1: Criar um recurso do Azure Front Door (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: O endereço de back-end é o nome de domínio totalmente qualificado (FQDN) do aplicativo que você deseja proteger. Por exemplo, myapplication.contoso.com

**--accepted-protocols**: Os protocolos aceitos especificam quais protocolos você deseja que o AFD dê suporte para seu aplicativo Web. Um exemplo seria --accepted-protocols Http Https.

**--name**: Especifique um nome para o recurso do AFD

**--resource-group**: O grupo de recursos no qual você deseja posicionar este recurso do AFD.  Para saber mais sobre grupos de recursos, visite Gerenciar grupos de recursos no Azure

Na resposta que você obtiver ao executar com êxito esse comando, procure a chave “hostName” e anote o valor a ser usado em uma etapa posterior. O hostName é o nome DNS do recurso do AFD que você criou

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Etapa 2: Criar um perfil de WAF do Azure para usar com os recursos do Azure Front Door

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Especifique um nome para a política do WAF do Azure

--resource-group O grupo de recursos no qual você deseja posicionar este recurso de WAF. 

O código da CLI acima criará uma política de WAF que está habilitada e está no modo de prevenção. 

Observação: você também pode querer criar o WAF no modo de Detecção e observar como ele está detectando e registrando em log as solicitações mal-intencionadas (e não o bloqueio) antes de decidir alterar para o modo de Proteção.

Na resposta que você obtiver ao executar com êxito esse comando, procure a chave “ID” e anote o valor a ser usado em uma etapa posterior. O campo ID deve estar no formato

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Etapa 3: Adicionar conjuntos de regras gerenciados a esta política de WAF

Em uma política de WAF, você pode adicionar conjuntos de regras gerenciados que são um conjunto de regras criadas e gerenciadas pela Microsoft e fornece proteção pronta para uso em classes inteiras de ameaças. Neste exemplo, estamos adicionando dois conjuntos de regras desse tipo: (1) um conjunto de regras padrão, que protege contra ameaças comuns da Web e (2) um conjunto de regras de proteção contra bots, que protege contra bots mal-intencionados

(1) Adicionar o conjunto de regras padrão

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Adicionar o conjunto de regras do gerenciador de bots

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name O nome que você deu para o recurso de WAF do Azure

--resource-group O grupo de recursos no qual você posicionou este recurso de WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Etapa 4: Associar a política de WAF ao recurso do AFD

Nesta etapa, vamos associar a política de WAF que criamos com o recurso do AFD que está na frente do seu aplicativo Web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name O nome que você especificou para o recurso do AFD

--resource-group O grupo de recursos no qual você colocou o recurso do Azure Front Door.

--set É aqui que você atualiza o atributo WebApplicationFirewallPolicyLink para o frontendEndpoint associado ao recurso do AFD com a política de WAF criada recentemente. A ID da política de WAF pode ser encontrada a partir da resposta obtida na etapa nº 2 acima

Observação: o exemplo acima é para o caso de você não estar usando um domínio personalizado, se você estiver

Se você não estiver usando domínios personalizados para acessar seus aplicativos Web, poderá ignorar a etapa nº 5. Nesse caso, você estará fornecendo aos seus usuários finais o nome de host obtido na etapa nº 1 para navegar até seu aplicativo Web

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Etapa 5: Configurar domínios personalizados para seu aplicativo Web

Inicialmente, o nome de domínio personalizado do seu aplicativo Web (aquele que os clientes usam para fazer referência ao seu aplicativo, por exemplo, www.contoso.com) estava apontando para o local onde você o estava executando antes de o AFD ser introduzido. Após essa alteração de arquitetura adicionando AFD + WAF para proteger o aplicativo, a entrada DNS correspondente a esse domínio personalizado agora deve apontar para este recurso do AFD. Isso pode ser feito remapeando essa entrada no servidor DNS para o nome de host do AFD que você anotou na etapa nº 1.

As etapas específicas para atualizar seus registros DNS dependerão do seu provedor de serviços DNS, mas se você estiver usando o DNS do Azure para hospedar seu nome DNS, consulte a documentação com as [etapas para atualizar um registro DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e apontar para o nome do host do AFD. 

Uma coisa importante a ser observada aqui é que, se você precisar que seus usuários naveguem até seu site usando o Apex da zona, por exemplo, contoso.com, será necessário usar o DNS do Azure e o [tipo de registro de ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) para hospedar seu nome DNS. 

Além disso, você também precisa atualizar sua configuração do AFD para [adicionar esse domínio personalizado](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) a ele para que o AFD entenda esse mapeamento.

Por fim, se você estiver usando um domínio personalizado para acessar seu aplicativo Web e quiser habilitar o protocolo HTTPS, você precisará ter os [certificados para a configuração de domínio personalizado no AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Etapa 6: Bloquear seu aplicativo Web

Uma melhor prática opcional a ser seguida é garantir que apenas as bordas do AFD possam se comunicar com seu aplicativo Web. Essa ação garantirá que ninguém possa ignorar as proteções do AFD e acessar seus aplicativos diretamente. Você pode realizar esse bloqueio visitando a [seção de perguntas frequentes do AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) e referindo-se à pergunta sobre o bloqueio de back-ends para acesso somente pelo AFD.
