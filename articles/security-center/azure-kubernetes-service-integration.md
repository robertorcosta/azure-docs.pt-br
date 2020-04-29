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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436170"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos serviços Kubernetess do Azure com a central de segurança

O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres. 

Use o AKS junto com a camada Standard da central de segurança do Azure (consulte [preços](security-center-pricing.md)) para obter uma visibilidade mais profunda dos nós AKs, do tráfego de nuvem e dos controles de segurança.

A central de segurança traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. 

![Visão geral de alto nível da central de segurança do Azure e do AKS (serviço de kubernetes do Azure)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntas, essas duas ferramentas formam a melhor oferta de segurança de kubernetes de nuvem nativa. 

## <a name="benefits-of-integration"></a>Vantagens da integração

O uso de dois serviços juntos fornece:

* **Recomendações de segurança** – a central de segurança identifica os recursos do AKs e os categoriza: de clusters para máquinas virtuais individuais. Em seguida, você pode exibir as recomendações de segurança por recurso. Para obter mais informações, consulte as recomendações de contêineres na [lista de recomendações de referência](recommendations-reference.md#recs-containers). 

* **Proteção do ambiente** – a central de segurança monitora constantemente a configuração dos clusters do kubernetes e das configurações do Docker. Em seguida, ele gera recomendações de segurança que refletem os padrões do setor.

* **Proteção de tempo de execução** -por meio de análise contínua das seguintes fontes de AKs, a central de segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host *e* do AKS:
    * Eventos de segurança brutos, como dados de rede e criação de processo
    * O log de auditoria do kubernetes

    Para obter mais informações, consulte [proteção contra ameaças para contêineres do Azure](threat-protection.md#azure-containers)

    Para obter a lista de possíveis alertas, consulte estas seções na tabela de referência de alertas: [AKs alertas de nível de cluster](alerts-reference.md#alerts-akscluster) e [alertas de nível de host do contêiner](alerts-reference.md#alerts-containerhost).  

![Central de segurança do Azure e serviço de kubernetes do Azure (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alguns dos dados verificados pela central de segurança do Azure do ambiente do kubernetes podem conter informações confidenciais.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o Registro de Contêiner do Azure](azure-container-registry-integration.md)

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – descreve as políticas de dados dos serviços da Microsoft (incluindo o Azure, o Intune e o Office 365), os detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados
