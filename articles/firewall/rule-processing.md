---
title: Lógica de processamento de regra do Firewall do Azure
description: O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113445"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra do Firewall do Azure
Você pode configurar regras de NAT, regras de rede e regras de aplicativos no Firewall do Azure. As coletas de regras são processadas de acordo com o tipo de regra em ordem de prioridade, números mais baixos para números mais altos de 100 a 65.000. Um nome de coleção de regras pode ter apenas letras, números, sublinhados, períodos ou hífens. Ele deve começar com uma letra ou número, e terminar com uma letra, número ou sublinhado. O comprimento máximo do nome é de 80 caracteres.

É melhor inicialmente espaçar seus números de prioridade de coleta de regras em 100 incrementos (100, 200, 300, e assim por diante) para que você tenha espaço para adicionar mais coleções de regras, se necessário.

> [!NOTE]
> Se você habilitar a filtragem baseada em inteligência de ameaças, essas regras são de maior prioridade e são sempre processadas primeiro. A filtragem de inteligência de ameaças pode negar o tráfego antes que quaisquer regras configuradas sejam processadas. Para obter mais informações, consulte [a filtragem baseada em ameaças do Azure Firewall](threat-intel.md).

## <a name="outbound-connectivity"></a>Conectividade de saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos

Se você configurar regras de rede e regras de aplicativo, então as regras da rede serão aplicadas em ordem de prioridade antes das regras do aplicativo. As regras estão sendo encerradas. Então, se uma correspondência é encontrada em uma regra de rede, nenhuma outra regra é processada.  Se não houver correspondência de regra de rede e se o protocolo for HTTP, HTTPS ou MSSQL, o pacote será então avaliado pelas regras do aplicativo em ordem de prioridade. Se ainda não for encontrado nenhum jogo, o pacote será avaliado em relação à cobrança das [regras de infra-estrutura](infrastructure-fqdns.md). Se ainda não houver nenhuma correspondência, o pacote será negado por padrão.

## <a name="inbound-connectivity"></a>Conectividade de entrada

### <a name="nat-rules"></a>Regras de NAT

A conectividade de entrada da Internet pode ser ativada configurando o DNAT (Destination Network Address Translation, tradução de endereço de rede de destino) conforme descrito no [Tutorial: Filtrar tráfego de entrada com o DNAT do Firewall Azure usando o portal Azure](tutorial-firewall-dnat.md). As regras nat são aplicadas priormente antes das regras da rede. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido.

As regras do aplicativo não são aplicadas para conexões de entrada. Portanto, se você quiser filtrar o tráfego HTTP/S de entrada, você deve usar o WAF (Web Application Firewall). Para obter mais informações, consulte O que é o Firewall de Aplicativos Web do [Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram os resultados de algumas dessas combinações de regras.

### <a name="example-1"></a>Exemplo 1

A conexão com google.com é permitida por causa de uma regra de rede correspondente.

**Regra da rede**

- Ação: permitir


|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir-web     |TCP|Endereço IP|*|Endereço IP|*|80.443

**Regra de aplicação**

- Ação: Negar

|name  |Tipo de origem  |Fonte  |Protocolo:Porta|Alvo FQDNs|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Endereço IP|*|http:80,https:443|google.com

**Resultado**

A conexão com google.com é permitida porque o pacote corresponde à regra de rede *Allow-web.* O processamento de regras pára neste momento.

### <a name="example-2"></a>Exemplo 2

O tráfego SSH é negado porque uma coleta de regras de rede *Deny* de maior prioridade bloqueia-a.

**Coleção de regras de rede 1**

- Nome: Coleção de permitir
- Prioridade: 200
- Ação: permitir

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Coleção de regras de rede 2**

- Nome: Deny-collection
- Prioridade: 100
- Ação: Negar

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As conexões SSH são negadas porque uma coleta de regras de rede de maior prioridade bloqueia-a. O processamento de regras pára neste momento.

## <a name="rule-changes"></a>Mudanças de regras

Se você alterar uma regra para negar tráfego permitido anteriormente, quaisquer sessões existentes relevantes serão retiradas.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).