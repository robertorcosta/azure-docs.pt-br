---
title: Soluções VMware do Azure (AVS)-endereço IP público
description: Saiba mais sobre endereços IP públicos e seus benefícios em soluções VMware do Azure (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024935"
---
# <a name="avs-public-ip-address-overview"></a>Visão geral do endereço IP público da AVS

Um endereço IP público permite que os recursos da Internet se comuniquem de entrada para recursos de nuvem privada AVS em um endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software em seu vCenter de nuvem privada da AVS. O endereço IP público permite que você exponha serviços em execução na sua nuvem privada de AVS para a Internet.

O endereço IP público é dedicado ao endereço IP privado até que você o cancele. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público sempre usa o endereço IP público para acesso à Internet. Por padrão, somente o acesso à Internet de saída é permitido em um endereço IP público.  O tráfego de entrada no endereço IP público foi negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

O uso de um endereço IP público para comunicar a entrada fornece:

* Prevenção de ataque de DDoS (negação de serviço distribuído). Essa proteção é habilitada automaticamente para o endereço IP público.
* Monitoramento de tráfego sempre ativa e mitigação em tempo real de ataques comuns em nível de rede. Essas defesas são as mesmas usadas pelo Microsoft serviços online.
* Toda a escala da rede global do Azure. A rede pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões.  

## <a name="next-steps"></a>Próximos passos

* Saiba como [alocar um endereço IP público](public-ips.md)