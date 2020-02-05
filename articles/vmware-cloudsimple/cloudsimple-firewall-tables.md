---
title: Soluções VMware do Azure (AVS)-tabelas de firewall
description: Saiba mais sobre as tabelas de firewall e as regras de firewall da nuvem privada da AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025037"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego de rede de e para recursos de nuvem privada da AVS. Você pode aplicar tabelas de firewall a uma VLAN/sub-rede. As regras controlam o tráfego de rede entre uma rede de origem ou um endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras de firewall

A tabela a seguir descreve os parâmetros em uma regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra de firewall e sua finalidade. |
| **Prioridade** | Um número entre 100 e 4096, com 100 sendo a prioridade mais alta. As regras são processadas em ordem de prioridade. Quando o tráfego encontra uma correspondência de regra, o processamento da regra é interrompido. Como resultado, as regras com prioridades mais baixas que têm os mesmos atributos que as regras com prioridades mais altas não são processadas. Tome cuidado para evitar regras conflitantes. |
| **Rastreamento de estado** | O rastreamento pode ser sem monitoração de estado (rede virtual de sincronização automática, Internet ou VPN) ou com estado (IP público).  |
| **Protocolo** | As opções incluem any, TCP ou UDP. Se você precisar de ICMP, use any. |
| **Direção** | Se a regra se aplica ao tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar para o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer endereço IP.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança. |
| **Porta de origem** | Porta da qual o tráfego de rede é originado.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança. |
| **Destino** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer endereço IP.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança.  |
| **Porta de destino** | Porta para a qual o tráfego de rede flui.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra sem estado só é semelhante a pacotes individuais e filtra-as com base na regra. Regras adicionais podem ser necessárias para o fluxo de tráfego na direção inversa. Use regras sem estado para o tráfego entre os seguintes pontos:

* Sub-redes de nuvens privadas da AVS
* Sub-rede local e uma sub-rede da nuvem privada da AVS
* Tráfego de Internet das nuvens privadas da AVS

### <a name="stateful"></a>Dinâmico

 Uma regra com estado está ciente das conexões que passam por ela. Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo. Use esse tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras padrão

As regras padrão a seguir são criadas em cada tabela de firewall.

|Prioridade|Nome|Rastreamento de estado|Direção|Tipo de tráfego|Protocolo|Origem|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo para a Internet|Dinâmico|Saída|IP público ou tráfego de Internet|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65001|negar-tudo-da-Internet|Dinâmico|Entrada|IP público ou tráfego de Internet|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-tudo para a intranet|Sem estado|Saída|Tráfego interno ou VPN da nuvem privada da AVS|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65003|allow-all-from-intranet|Sem estado|Entrada|Tráfego interno ou VPN da nuvem privada da AVS|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|

## <a name="next-steps"></a>Próximos passos

* [Configurar regras e tabelas de firewall](firewall.md)
