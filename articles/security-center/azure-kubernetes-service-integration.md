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
ms.openlocfilehash: 0743499b019bd1c7b985636e886eee9352284a55
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616066"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integração dos serviços Kubernetess do Azure com a central de segurança (versão prévia)
O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres. 

Use o AKS junto com a camada Standard da central de segurança do Azure (consulte [preços](security-center-pricing.md)) para obter uma visibilidade mais profunda dos nós AKs, do tráfego de nuvem e dos controles de segurança.

A central de segurança traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. 

![Visão geral de alto nível da central de segurança do Azure e do AKS (serviço de kubernetes do Azure)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntas, essas duas ferramentas formam a melhor oferta de segurança de kubernetes de nuvem nativa. 

## <a name="benefits-of-integration"></a>Vantagens da integração

O uso de dois serviços juntos fornece:

* **Recomendações de segurança** – a central de segurança identifica os recursos do AKs e os categoriza: de clusters para máquinas virtuais individuais. Em seguida, você pode exibir as recomendações de segurança por recurso. Para obter mais informações, consulte as recomendações de contêineres na [lista de recomendações de referência](recommendations-reference.md#recs-computeapp). 

    > [!NOTE]
    > Se o nome de uma recomendação da central de segurança terminar com uma marca "(visualização)", ele se referirá à natureza de visualização da recomendação, não ao recurso.

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

* [Integração com o registro de contêiner do Azure](azure-container-registry-integration.md)

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – descreve as políticas de dados dos serviços da Microsoft (incluindo o Azure, o Intune e o Office 365), os detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados