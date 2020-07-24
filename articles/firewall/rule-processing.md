---
title: Lógica de processamento de regra do Firewall do Azure
description: O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087536"
---
# <a name="configure-azure-firewall-rules"></a>Configurar regras de firewall do Azure
Você pode configurar regras de NAT, regras de rede e regras de aplicativos no firewall do Azure. As coleções de regras são processadas de acordo com o tipo de regra em ordem de prioridade, números menores para números mais altos de 100 a 65.000. Um nome de coleção de regras pode ter apenas letras, números, sublinhados, pontos ou hifens. Ele deve começar com uma letra ou número e terminar com uma letra, número ou sublinhado. O comprimento máximo do nome é de 80 caracteres.

É melhor espaçar inicialmente os números de prioridade da coleção de regras em incrementos de 100 (100, 200, 300 e assim por diante) para que você tenha espaço para adicionar mais coleções de regras, se necessário.

> [!NOTE]
> Se você habilitar a filtragem baseada em inteligência contra ameaças, essas regras serão de prioridade mais alta e sempre serão processadas primeiro. A filtragem de inteligência de ameaças pode negar o tráfego antes que qualquer regra configurada seja processada. Para obter mais informações, consulte [filtragem baseada em inteligência contra ameaças do firewall do Azure](threat-intel.md).

## <a name="outbound-connectivity"></a>Conectividade de saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos

Se você configurar regras de rede e regras de aplicativo, as regras de rede serão aplicadas em ordem de prioridade antes das regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada em uma regra de rede, não serão processadas outras regras.  Se não houver nenhuma correspondência de regra de rede, e se o protocolo for HTTP, HTTPS ou MSSQL, o pacote será então avaliado pelas regras de aplicativo em ordem de prioridade. Se ainda não for encontrada nenhuma correspondência, o pacote será avaliado em relação à [coleção de regras de infraestrutura](infrastructure-fqdns.md). Se ainda não houver nenhuma correspondência, o pacote será negado por padrão.

## <a name="inbound-connectivity"></a>Conectividade de entrada

### <a name="nat-rules"></a>Regras de NAT

A conectividade de Internet de entrada pode ser habilitada Configurando DNAT (conversão de endereços de rede de destino) conforme descrito em [tutorial: filtrar o tráfego de entrada com o Firewall do Azure DNAT usando o portal do Azure](tutorial-firewall-dnat.md). As regras de NAT são aplicadas em prioridade antes das regras de rede. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido.

As regras de aplicativo não são aplicadas a conexões de entrada. Portanto, se você quiser filtrar o tráfego HTTP/S de entrada, deverá usar o WAF (firewall do aplicativo Web). Para obter mais informações, consulte [o que é o Firewall do aplicativo Web do Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram os resultados de algumas dessas combinações de regras.

### <a name="example-1"></a>Exemplo 1

A conexão com o google.com é permitida devido a uma regra de rede correspondente.

**Regra de rede**

- Ação: Permitir


|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir-Web     |TCP|Endereço IP|*|Endereço IP|*|80.443

**Regra de aplicativo**

- Ação: Negar

|name  |Tipo de origem  |Fonte  |Protocolo: porta|FQDNs de destino|
|---------|---------|---------|---------|----------|----------|
|Negar-Google     |Endereço IP|*|http: 80, https: 443|google.com

**Resultado**

A conexão com google.com é permitida porque o pacote corresponde à regra de rede *Allow-Web* . O processamento da regra é interrompido neste ponto.

### <a name="example-2"></a>Exemplo 2

O tráfego SSH é negado porque uma coleção de regras de rede de *negação* de prioridade mais alta o bloqueia.

**Coleção de regras de rede 1**

- Nome: permitir-coleção
- Prioridade: 200
- Ação: Permitir

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Coleção de regras de rede 2**

- Nome: Deny-Collection
- Prioridade: 100
- Ação: Negar

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Negar-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As conexões SSH são negadas porque uma coleção de regras de rede de prioridade mais alta a bloqueia. O processamento da regra é interrompido neste ponto.

## <a name="rule-changes"></a>Alterações de regra

Se você alterar uma regra para negar tráfego anteriormente permitido, todas as sessões existentes relevantes serão descartadas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).