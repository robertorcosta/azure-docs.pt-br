---
title: Solução Azure VMware por CloudSimple - Tabelas de firewall
description: Saiba mais sobre as tabelas de firewall de nuvem privadas do CloudSimple e as regras de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025037"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego de rede de e para recursos do Private Cloud. Você pode aplicar tabelas de firewall a uma VLAN/sub-rede. As regras controlam o tráfego da rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras de firewall

A tabela a seguir descreve os parâmetros em uma regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra do firewall e seu propósito. |
| **Priority** | Um número entre 100 e 4096, sendo 100 a maior prioridade. As regras são processadas em ordem de prioridade. Quando o tráfego encontra uma correspondência de regras, o processamento de regras pára. Como resultado, regras com prioridades mais baixas que têm os mesmos atributos que regras com prioridades mais altas não são processadas.  Tome cuidado para evitar regras conflitantes. |
| **Rastreamento do Estado** | O rastreamento pode ser apátrida (Nuvem Privada, Internet ou VPN) ou estatal (IP público).  |
| **Protocolo** | As opções incluem Qualquer, TCP ou UDP. Se você precisar de ICMP, use Qualquer. |
| **Direção** | Se a regra se aplica ao tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar o tipo de tráfego definido na regra. |
| **Fonte** | Um endereço IP, bloco de roteamento entre domínios sem classe (CIDR) (10.0.0.0/24, por exemplo) ou Qualquer.  Especificar um intervalo, uma tag de serviço ou um grupo de segurança de aplicativos permite criar menos regras de segurança. |
| **Porto de Origem** | Porta de onde o tráfego de rede se origina.  Você pode especificar uma porta individual ou uma gama de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança. |
| **Destino** | Um endereço IP, bloco de roteamento entre domínios sem classe (CIDR) (10.0.0.0/24, por exemplo) ou Qualquer.  Especificar um intervalo, uma tag de serviço ou um grupo de segurança de aplicativos permite criar menos regras de segurança.  |
| **Porto de Destino** | Porto para o qual o tráfego da rede flui.  Você pode especificar uma porta individual ou uma gama de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra apátrida olha apenas para pacotes individuais e os filtra com base na regra.  
Podem ser necessárias regras adicionais para o fluxo de tráfego no sentido inverso.  Use regras apátridas para o tráfego entre os seguintes pontos:

* Sub-redes de nuvens privadas
* Sub-rede no local e uma sub-rede Private Cloud
* Tráfego de Internet das Nuvens Privadas

### <a name="stateful"></a>Monitoração de estado

 Uma regra imponente está ciente das conexões que passam por ela. Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo.  Use este tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras padrão

As seguintes regras padrão são criadas em todas as tabelas de firewall.

|Prioridade|Nome|Rastreamento do Estado|Direção|Tipo de Tráfego|Protocolo|Fonte|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo-para-internet|Monitoração de estado|Saída|Ip público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|
|65001|negar-tudo-da-internet|Monitoração de estado|Entrada|Ip público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-tudo-para-intranet|Sem estado|Saída|Tráfego interno ou VPN da Private Cloud|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|
|65003|permitir-tudo-de-intranet|Sem estado|Entrada|Tráfego interno ou VPN da Private Cloud|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|

## <a name="next-steps"></a>Próximas etapas

* [Configurar regras e tabelas de firewall](firewall.md)
