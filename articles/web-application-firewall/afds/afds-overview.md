---
title: O que é o firewall do aplicativo Web do Azure no Azure Front Door?
description: Saiba como o firewall do aplicativo Web do Azure no Azure Front Door Service protege seus aplicativos Web contra ataques mal-intencionados.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 09/28/2019
ms.author: victorh
ms.openlocfilehash: ce70260c6033d22b20675d6f3872c2ffa6368252
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495627"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall do Aplicativo Web do Azure no Azure Front Door

O WAF (Firewall do Aplicativo Web) do Azure no Azure Front Door fornece proteção centralizada de seus aplicativos Web que são disponibilizados globalmente usando o Azure Front Door. Ele foi projetado e operado para proteger seus serviços Web contra vulnerabilidades e explorações comuns, além de manter o serviço altamente disponível para seus usuários e ajudar você a atender aos requisitos de conformidade.

O WAF no Front Door é uma solução global e centralizada. Ele é implantado em locais de borda de rede do Azure no mundo inteiro e cada solicitação de entrada para um aplicativo Web habilitado para WAF entregue pelo Front Door é inspecionada na borda da rede. Isso permite que o WAF impeça ataques mal-intencionados próximos às fontes de ataque antes que eles entrem em sua rede virtual e oferece proteção global em escala sem prejudicar o desempenho. Uma política de WAF pode ser facilmente vinculada a qualquer perfil do Front Door em sua assinatura e novas regras podem ser implantadas em minutos, permitindo que você responda rapidamente aos padrões de ameaça em constante mudança.

![Firewall do aplicativo Web do Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Política e regras do WAF

Você pode configurar uma política de WAF e associá-la a um ou mais front-ends do Front Door para proteção. Uma política do WAF consiste em dois tipos de regras de segurança:

- regras personalizadas criadas pelo cliente.

- conjuntos de regras gerenciadas que são uma coleção do conjunto de regras pré-configurado gerenciado pelo Azure.

Quando ambas estiverem presentes, as regras personalizadas serão processadas antes das regras em um conjunto de regras gerenciado. Uma regra é composta por uma condição de correspondência, uma prioridade e uma ação. Os tipos de ação com suporte são: PERMITIR, BLOQUEAR, REGISTRAR e REDIRECIONAR. Você pode criar uma política totalmente personalizada que atenda aos seus requisitos de proteção de aplicativo específicos combinando regras gerenciadas e personalizadas.

As regras em uma política são processadas em uma ordem priorizada em que a prioridade é um inteiro único que define a ordem das regras que estão sendo processadas. Um valor inteiro menor denota uma prioridade maior, e essas regras são avaliadas antes daquelas com um valor inteiro mais alto. Quando há correspondência de uma regra, a ação relevante definida na regra é aplicada à solicitação. Depois de essa correspondência ser processada, as regras com prioridades menores não são mais processadas.

Um aplicativo Web entregue pelo Front Door pode ter apenas uma política WAF associada por vez. No entanto, você pode ter uma configuração de Front Door sem nenhuma política de WAF associada. Se uma política de WAF estiver presente, ela será replicada para todos os locais de borda para garantir a consistência em políticas de segurança em todo o mundo.

## <a name="waf-modes"></a>Modos de WAF

A política de WAF pode ser configurada para ser executada nos dois modos a seguir:

- **Modo de detecção:** Quando executado no modo de detecção, o WAF não executa outras ações além de monitores e registra a solicitação e sua regra WAF correspondente nos logs do WAF. Você pode ativar o diagnóstico de log para o Front Door (ao usar o portal, isso pode ser feito acessando a seção **Diagnóstico** no portal do Azure).

- **Modo de prevenção:** Quando configurado para ser executado no modo de prevenção, o WAF executará a ação especificada se uma solicitação corresponder a uma regra e, se uma correspondência for encontrada, nenhuma regra adicional com prioridade mais baixa será avaliada. Qualquer solicitação correspondente também é registrada nos logs do WAF.

## <a name="waf-actions"></a>Ações de WAF

Os clientes do WAF podem optar por executar uma das ações quando uma solicitação corresponde às condições de uma regra:

- **Permitir:**  a solicitação passa pelo WAF e é encaminhada para o back-end. Nenhuma outra regra de prioridade mais baixa pode bloquear essa solicitação.
- **Bloquear:** a solicitação é bloqueada e o WAF envia uma resposta ao cliente sem encaminhar a solicitação ao back-end.
- **Registrar em log:**  a solicitação é registrada nos logs do WAF e o WAF continua avaliando as regras de prioridade mais baixa.
- **Redirecionar:** o WAF redireciona a solicitação para o URI especificado. O URI especificado é uma configuração em nível de política. Depois de configuradas, todas as solicitações que correspondem à ação **Redirecionar** são enviadas para esse URI.

## <a name="waf-rules"></a>Regras de WAF

Uma política do WAF pode consistir em dois tipos de regras de segurança: regras personalizadas, criadas pelo cliente, e conjuntos de regras gerenciados, um conjunto de regras pré-configurado gerenciado pelo Azure.

### <a name="custom-authored-rules"></a>Regras criadas personalizadas

Você pode configurar regras personalizadas WAF da seguinte maneira:

- **Lista de contatos bloqueados e lista de permissões de IP:** é possível configurar regras personalizadas para controlar o acesso a aplicativos Web com base em uma lista de endereços IP ou intervalos de endereço IP do cliente. Há suporte para os tipos de endereço IPv4 e IPv6. Essa lista pode ser configurada para bloquear ou permitir as solicitações em que o IP de origem corresponde a um IP na lista.

- **Controle de acesso baseado em região geográfica:** você pode configurar regras personalizadas para controlar o acesso a seus aplicativos Web com base no código do país associado ao endereço IP de um cliente.

- **Controle de acesso baseado em parâmetros HTTP:** você pode configurar regras personalizadas com base em uma cadeia de caracteres que corresponde a parâmetros de solicitação HTTP/HTTPS, como cadeias de consulta, argumentos POST, URI de solicitação, cabeçalho de solicitação e corpo da solicitação.

- **Solicitar controle de acesso baseado em método:** você pode configurar regras personalizadas com base no método de solicitação HTTP da solicitação, como GET, PUT ou HEAD.

- **Restrição de tamanho:** Você pode configurar regras personalizadas com base nos comprimentos de partes específicas de uma solicitação, como cadeia de consulta, URI ou corpo da solicitação.

- **Regras de limitação de taxa:** Uma regra de controle de taxa tem a finalidade de limitar tráfego anormalmente alto proveniente de qualquer IP de cliente. Você pode configurar um número limite de solicitações Web permitidas de um IP de cliente no intervalo de um minuto. Isso é diferente de uma regra personalizada de permitir/bloquear baseada em lista de IPs que permite ou bloqueia todas as solicitações de um IP do cliente. A limitação de taxa pode ser combinada a condições de correspondência adicionais, como parâmetros HTTP(S) correspondentes, para controle de taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras gerenciados pelo Azure

Os conjuntos de regras gerenciados pelo Azure oferecem uma maneira fácil de implantar a proteção contra um conjunto comum de ameaças de segurança. Como esses conjuntos de regras são gerenciados pelo Azure, as regras são atualizadas conforme necessário para proteção contra novas assinaturas de ataque. Na versão prévia pública, o Conjunto de Regras Padrão gerenciado pelo Azure inclui regras contra as seguintes categorias de ameaça:

- Script entre sites
- Ataques de Java
- Inclusão de arquivo local
- Ataque de injeção de PHP
- Execução de comando remoto
- Inclusão de arquivo remoto
- Fixação da sessão
- Proteção contra injeção de SQL
- Invasores de protocolo

O número de versão do Conjunto de Regras Padrão será incrementado quando novas assinaturas de ataque forem adicionadas ao conjunto de regras.
O Conjunto de Regras Padrão é habilitado por padrão no modo de detecção em suas políticas de WAF. Você pode desabilitar ou habilitar regras individuais dentro do Conjunto de Regras Padrão para atender aos requisitos do aplicativo. Você também pode definir ações específicas (PERMITIR/BLOQUEAR/REDIRECIONAR/REGISTRAR EM LOG) por regra. A ação padrão é BLOQUEAR. Além disso, as regras personalizadas poderão ser configuradas na mesma política de WAF se você desejar ignorar qualquer uma das regras pré-configuradas no conjunto de regras padrão.
As regras personalizadas sempre são aplicadas antes de as regras no conjunto de regras padrão serem avaliadas. Se uma solicitação corresponder a uma regra personalizada, a ação de regra correspondente será aplicada e a solicitação será bloqueada ou passada para o back-end sem a invocação de mais nenhuma regra personalizada ou das regras no Conjunto de Regras Padrão. Além disso, você tem a opção de remover o Conjunto de Regras Padrão de suas políticas de WAF.


### <a name="bot-protection-rule-preview"></a>Regra de proteção contra bots (versão prévia)

Um conjunto de regras de proteção contra bots gerenciado pode ser habilitado para que seu WAF execute ações personalizadas em solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

![Conjunto de regras de proteção contra bots](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Se a Proteção contra bots estiver habilitada, as solicitações de entrada que corresponderem a IPs de Bots Mal-Intencionados serão registradas no log FrontdoorWebApplicationFirewallLog. Você pode acessar os logs do WAF da conta de armazenamento, Hub de eventos ou análise de logs. 

## <a name="configuration"></a>Configuração

A configuração e a implantação de todos os tipos de regras WAF têm suporte total usando o portal do Azure, as APIs REST, os modelos do Azure Resource Manager e o Azure PowerShell.

## <a name="monitoring"></a>Monitoramento

O monitoramento para WAF no Front Door é integrado ao Azure Monitor para acompanhar alertas e monitorar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre o [Firewall de Aplicativo Web no Gateway de Aplicativo do Azure](../ag/ag-overview.md)