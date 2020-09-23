---
title: Azure defender para kubernetes-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e72875135243733a6acf03bb3aa6fb9405392d9d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933721"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure defender para kubernetes

O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

A central de segurança do Azure e o AKS formam a melhor oferta de segurança kubernetes de nuvem nativa e, juntas, fornecem proteção de ambiente, proteção de carga de trabalho, e segurança em tempo de execução, conforme descrito abaixo.

Para detecção de ameaças para seus clusters kubernetes, habilite o **Azure defender para kubernetes**.

A detecção de ameaças no nível de host para seus nós AKS do Linux estará disponível se você habilitar o [Azure defender para servidores](defender-for-servers-introduction.md).

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Refere|O **Azure defender para kubernetes** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Funções e permissões necessárias:|O **administrador de segurança** pode ignorar alertas.<br>**O leitor de segurança** pode exibir as conclusões.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure defender para kubernetes?

### <a name="run-time-protection"></a>Proteção de tempo de execução

Por meio da análise contínua das seguintes fontes de AKS, a central de segurança fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas para ameaças e atividades mal-intencionadas detectadas no nível de cluster do host *e* do AKS. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

A Central de Segurança fornece proteção contra ameaças em diferentes níveis: 

- **Nível de host (fornecido pelo Azure defender para servidores)** -usando o mesmo agente de log Analytics que a central de segurança usa em outras VMS, o Azure defender monitora seus nós AKs do Linux em busca de atividades suspeitas, como detecção de Shell da Web e conexão com endereços IP suspeitos conhecidos. O agente também monitora a análise específica de contêiner, como a criação de contêiner privilegiado, acesso suspeito a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    Para obter uma lista dos alertas de nível de host AKS, consulte a [tabela de referência de alertas](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

- **Nível de cluster AKs (fornecido pelo Azure defender para kubernetes)** – no nível do cluster, a proteção contra ameaças baseia-se na análise dos logs de auditoria do kubernetes. Para habilitar esse monitoramento **sem agente** , habilite o Azure defender. Para gerar alertas nesse nível, a Central de Segurança monitora seus serviços gerenciados pelo AKS usando os logs recuperados pelo AKS. Exemplos de eventos nesse nível incluem painéis expostos do Kubernetes, a criação de funções com altos privilégios e a criação de montagens confidenciais.

    Para obter uma lista dos alertas no nível do cluster AKS, confira a [Tabela de referência de alertas](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

    >[!NOTE]
    > A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem após a opção Kubernetes ser habilitada nas configurações de assinatura. 

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Como funciona a proteção kubernetes da central de segurança?

Veja abaixo um diagrama de alto nível da interação entre a central de segurança do Azure, o serviço kubernetes do Azure e o Azure Policy.

Você pode ver que os itens recebidos e analisados pela central de segurança incluem:

- logs de auditoria do servidor de API
- eventos de segurança brutos do agente de Log Analytics
- informações de configuração de cluster do cluster AKS
- configuração de carga de trabalho de Azure Policy (por meio do **complemento de Azure Policy para kubernetes**). [Saiba mais sobre as práticas recomendadas de proteção de carga de trabalho usando o controle de admissão kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Arquitetura de alto nível da interação entre a central de segurança do Azure, o serviço kubernetes do Azure e o Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure defender para kubernetes-perguntas frequentes

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Ainda posso obter proteções de AKS sem o agente de Log Analytics?

Conforme mencionado acima, o plano opcional do **Azure defender para kubernetes** fornece proteções no nível do cluster, o agente de log Analytics do **Azure defender para servidores** protege seus nós. 

É recomendável implantar ambos, para obter a proteção mais completa possível.

Se optar por não instalar o agente em seus hosts, você receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a proteção kubernetes da central de segurança, incluindo o Azure defender para kubernetes. 

Para obter material relacionado, consulte os seguintes artigos: 

- [Habilitar o Azure Defender](security-center-pricing.md)
- [Exportar alertas para uma sentinela do Azure ou um SIEM de terceiros](continuous-export.md)
- [Tabela de referência de alertas](alerts-reference.md)