---
title: Solução Azure VMware por CloudSimple - Endereço IP público
description: Conheça endereços IP públicos e seus benefícios na Solução Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024935"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Visão geral do endereço IP público do CloudSimple

Um endereço IP público permite que os recursos da Internet se comuniquem a entrar em recursos privados da Nuvem em um endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software no seu vCenter private cloud. O endereço IP público permite que você exponha os serviços em execução em sua Nuvem Privada à internet.

O endereço IP público é dedicado ao endereço IP privado até que você o desassine. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público sempre usa o endereço IP público para acesso à Internet. Por padrão, somente o acesso à internet de saída é permitido em um endereço IP público.  O tráfego de entrada no endereço IP público é negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

O uso de um endereço IP público para se comunicar de entrada fornece:

* Prevenção de ataques de negação distribuída de serviço (DDoS). Essa proteção é habilitada automaticamente para o endereço IP público.
* Monitoramento de tráfego sempre ligado e mitigação em tempo real de ataques comuns em nível de rede. Essas defesas são as mesmas defesas usadas pelos serviços online da Microsoft.
* Toda a escala da rede global azure. A rede pode ser usada para distribuir e mitigar o tráfego de ataques em todas as regiões.  

## <a name="next-steps"></a>Próximas etapas

* Saiba como [alocar um endereço IP público](public-ips.md)