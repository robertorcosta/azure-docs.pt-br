---
title: Azure Security Center e Azure Kubernetes Service
description: Conheça a integração do Azure Security Center com o Azure Kubernetes Services
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436170"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração do Azure Kubernetes Services com o Security Center

O Azure Kubernetes Service (AKS) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos contêineres. 

Use o AKS juntamente com o nível padrão do Azure Security Center (ver [preços)](security-center-pricing.md)para obter uma visibilidade mais profunda dos seus ádenos AKS, tráfego na nuvem e controles de segurança.

O Security Center traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. 

![Visão geral de alto nível do Azure Security Center e do Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntas, essas duas ferramentas formam a melhor oferta de segurança kubernetes nativa da nuvem. 

## <a name="benefits-of-integration"></a>Vantagens da integração

O uso dos dois serviços em conjunto fornece:

* **Recomendações de segurança** - O Security Center identifica seus recursos AKS e os categoriza: de clusters a máquinas virtuais individuais. Em seguida, você pode visualizar recomendações de segurança por recurso. Para obter mais informações, consulte as recomendações dos contêineres na [lista de referência das recomendações](recommendations-reference.md#recs-containers). 

* **Endurecimento do ambiente** - O Security Center monitora constantemente a configuração dos clusters Kubernetes e configurações do Docker. Em seguida, gera recomendações de segurança que refletem os padrões do setor.

* **Proteção por tempo de execução** - Através da análise contínua das seguintes fontes aks, o Security Center alerta para ameaças e atividades maliciosas detectadas no nível de cluster host *e* AKS:
    * Eventos de segurança brutos, como dados de rede e criação de processos
    * O registro de auditoria do Kubernetes

    Para obter mais informações, consulte [proteção contra ameaças para contêineres Azure](threat-protection.md#azure-containers)

    Para obter a lista de possíveis alertas, consulte estas seções na tabela de referência de alertas: [alertas de nível de cluster AKS](alerts-reference.md#alerts-akscluster) e [alertas de nível de host de contêiner](alerts-reference.md#alerts-containerhost).  

![Azure Security Center e Azure Kubernetes Service (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alguns dos dados digitalizados pelo Azure Security Center a partir do seu ambiente Kubernetes podem conter informações confidenciais.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança de contêineres do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contêineres](container-security.md)

* [Integração com o Registro de Contêiner do Azure](azure-container-registry-integration.md)

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - Descreve as políticas de dados dos serviços microsoft (incluindo Azure, Intune e Office 365), detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados
