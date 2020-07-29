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
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800163"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos serviços Kubernetess do Azure com a central de segurança

O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres. 

Se você estiver na camada Standard da central de segurança do Azure, poderá adicionar o pacote AKS (consulte [preços](security-center-pricing.md)) para obter uma visibilidade mais profunda dos nós AKs, do tráfego de nuvem e dos controles de segurança.

Juntos, a central de segurança e a AKS formam a melhor oferta de segurança kubernetes de nuvem nativa.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quais são os componentes da proteção kubernetes da central de segurança?

As proteções da central de segurança para kubernetes são fornecidas por uma combinação de dois elementos:

- **Proteção contra ameaças da central de segurança do Azure para máquinas virtuais** – usando o mesmo agente de log Analytics que a central de segurança usa em outras VMS, a central de segurança pode mostrar problemas de segurança que ocorrem em seus nós AKs. O agente também monitora a análise específica do contêiner.

- **Pacote kubernetes opcional da central de segurança do Azure** – o pacote kubernetes recebe logs e informações do subsistema kubernetes por meio do serviço AKs. Esses logs já estão disponíveis no Azure por meio do serviço AKS. Ao habilitar o pacote kubernetes da central de segurança, você concede acesso à central de segurança aos logs. Portanto, a central de segurança traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. Alguns dos dados verificados pela central de segurança do Azure do ambiente do kubernetes podem conter informações confidenciais.

    ![Visão geral de alto nível da central de segurança do Azure e do AKS (serviço de kubernetes do Azure)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Quais proteções são fornecidas?

O uso de dois serviços juntos fornece:

* **Recomendações de segurança** – a central de segurança identifica os recursos do AKs e os categoriza: de clusters para máquinas virtuais individuais. Em seguida, você pode exibir as recomendações de segurança por recurso. Para obter mais informações, consulte as recomendações de contêineres na [lista de recomendações de referência](recommendations-reference.md#recs-containers). 

* **Proteção do ambiente** – a central de segurança monitora constantemente a configuração dos clusters do kubernetes e das configurações do Docker. Em seguida, ele gera recomendações de segurança que refletem os padrões do setor.

* **Proteção de tempo de execução** -por meio de análise contínua das seguintes fontes de AKs, a central de segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host *e* do AKS. [Saiba mais sobre a proteção contra ameaças para contêineres](threat-protection.md#azure-containers).


     

![Central de segurança do Azure e serviço de kubernetes do Azure (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>Perguntas frequentes sobre o AKS com a central de segurança

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Ainda posso obter proteções de AKS sem o agente de Log Analytics?

Conforme mencionado acima, o pacote kubernetes opcional fornece proteções no nível de cluster, o agente de Log Analytics da camada Standard da central de segurança do Azure protege seus nós. 

É recomendável implantar ambos, para obter a proteção mais completa possível.

Se optar por não instalar o agente em seus hosts, você receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.



## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o Registro de Contêiner do Azure](azure-container-registry-integration.md)

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) -descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Microsoft 365), detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados
