---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800590"
---
A Central de Segurança fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas sobre atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

A Central de Segurança fornece proteção contra ameaças em diferentes níveis: 

* **Nível de host** – o agente de log Analytics monitora o Linux em busca de atividades suspeitas. O agente dispara alertas sobre atividades suspeitas provenientes do nó ou de um contêiner em execução nele. Exemplos dessas atividades incluem a detecção e a conexão de Shell da Web com endereços IP suspeitos conhecidos.

    Para obter uma visão mais profunda da segurança do seu ambiente em contêiner, o agente monitora a análise específica do contêiner. Ele disparará alertas para eventos como a criação de contêineres privilegiados, acessos suspeitos a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

    Para obter uma lista dos alertas de nível de host AKS, consulte a [tabela de referência de alertas](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* No **nível do cluster AKS**, a proteção contra ameaças é baseada na análise de logs de auditoria do Kubernetes. Para habilitar esse monitoramento **sem agente**, adicione a opção Kubernetes à sua assinatura na página **Preços e configurações** (confira [Preços](https://docs.microsoft.com/azure/security-center/security-center-pricing)). Para gerar alertas nesse nível, a Central de Segurança monitora seus serviços gerenciados pelo AKS usando os logs recuperados pelo AKS. Exemplos de eventos nesse nível incluem painéis expostos do Kubernetes, a criação de funções com altos privilégios e a criação de montagens confidenciais.

    >[!NOTE]
    > A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem após a opção Kubernetes ser habilitada nas configurações de assinatura. 

    Para obter uma lista dos alertas no nível do cluster AKS, confira a [Tabela de referência de alertas](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).