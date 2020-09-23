---
title: Central de segurança do Azure e serviço kubernetes do Azure
description: Saiba mais sobre a integração da central de segurança do Azure com os serviços Kubernetess do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894939"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos serviços Kubernetess do Azure com a central de segurança

O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres. 

Habilite o Azure **defender para kubernetes** para obter uma visibilidade mais profunda dos nós do AKs, do tráfego de nuvem e dos controles de segurança.

Juntos, a central de segurança e a AKS formam a melhor oferta de segurança kubernetes de nuvem nativa.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quais são os componentes da proteção kubernetes da central de segurança?

As proteções da central de segurança para kubernetes são fornecidas por uma combinação de dois elementos:

- **Proteção contra ameaças da central de segurança do Azure para máquinas virtuais** – usando o mesmo agente de log Analytics que a central de segurança usa em outras VMS, a central de segurança pode mostrar problemas de segurança que ocorrem em seus nós AKs. O agente também monitora a análise específica do contêiner.

- **Azure defender for kubernetes opcional da central de segurança do Azure** -o plano kubernetes recebe logs e informações do subsistema kubernetes por meio do serviço AKs. Esses logs já estão disponíveis no Azure por meio do serviço AKS. Ao habilitar o Azure defender para kubernetes, você concede acesso à central de segurança aos logs. Portanto, a central de segurança traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. Alguns dos dados verificados pela central de segurança do Azure do ambiente do kubernetes podem conter informações confidenciais.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o Registro de Contêiner do Azure](azure-container-registry-integration.md)

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) -descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Microsoft 365), detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados
