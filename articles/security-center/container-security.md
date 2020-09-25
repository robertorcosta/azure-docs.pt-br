---
title: Segurança de contêiner na central de segurança do Azure | Microsoft Docs
description: Saiba mais sobre os recursos de segurança do contêiner da central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 3edd7ddf79f8400462b52f964b7677840a7e86df
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301951"
---
# <a name="container-security-in-security-center"></a>Segurança do contêiner na central de segurança

A central de segurança do Azure é a solução nativa do Azure para proteger seus contêineres.

A central de segurança pode proteger os seguintes tipos de recursos de contêiner:

| Tipo de recurso | Proteções oferecidas pela central de segurança |
|:--------------------:|-----------|
| ![Serviço kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Clusters do AKS (serviço kubernetes do Azure)** | -Avaliação contínua de suas configurações de clusters AKS para fornecer visibilidade de configurações incorretas e diretrizes para ajudá-lo a resolver quaisquer problemas descobertos.<br>[Saiba mais sobre a proteção de ambiente por meio de recomendações de segurança](#environment-hardening).<br><br>-Proteção contra ameaças para clusters AKS e nós do Linux. Alertas para atividades suspeitas são fornecidos pelo  [Azure defender opcional para kubernetes](defender-for-kubernetes-introduction.md).<br>[Saiba mais sobre a proteção em tempo de execução para nós e clusters AKs](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Host do contêiner](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Hosts de contêiner**<br>(VMs que executam o Docker) | -Avaliação contínua das configurações do Docker para fornecer visibilidade de configurações incorretas e diretrizes para ajudá-lo a resolver quaisquer problemas descobertos com o  [Azure defender para servidores](defender-for-servers-introduction.md)opcionais.<br>[Saiba mais sobre a proteção de ambiente por meio de recomendações de segurança](#environment-hardening).|
| ![Registro de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registros de ACR (registro de contêiner do Azure)** | -Ferramentas de avaliação e gerenciamento de vulnerabilidade para as imagens em seus registros ACR baseados em Azure Resource Manager com o [Azure defender opcional para registros de contêiner](defender-for-container-registries-introduction.md).<br>[Saiba mais sobre como verificar se há vulnerabilidades em suas imagens de contêiner](#vulnerability-management---scanning-container-images). |
|||

Este artigo descreve como você pode usar a central de segurança, junto com os planos opcionais do Azure defender para registros de contêiner, servidores e kubernetes, para melhorar, monitorar e manter a segurança de seus contêineres e seus aplicativos.

Você aprenderá como a central de segurança ajuda com esses aspectos principais da segurança do contêiner:

- [Gerenciamento de vulnerabilidades-verificação de imagens de contêiner](#vulnerability-management---scanning-container-images)
- [Proteção do ambiente](#environment-hardening)
- [Proteção em tempo de execução para nós e clusters AKS](#run-time-protection-for-aks-nodes-and-clusters)

A captura de tela a seguir mostra a página de inventário de ativos e os vários tipos de recursos de contêiner protegidos pela central de segurança.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Recursos relacionados ao contêiner na página de inventário de ativos da central de segurança" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gerenciamento de vulnerabilidades-verificação de imagens de contêiner

Para monitorar imagens em seus registros de contêiner do Azure baseados em Azure Resource Manager, habilite o [Azure defender para registros de contêiner](defender-for-container-registries-introduction.md). A central de segurança examina todas as imagens recebidas nos últimos 30 dias, enviadas por push ao registro ou importadas. O scanner integrado é fornecido pelo fornecedor de verificação de vulnerabilidade líder do setor, Qualys.

Quando forem encontrados problemas – por Qualys ou pela central de segurança – você será notificado no [painel do Azure defender](azure-defender-dashboard.md). Para cada vulnerabilidade, a central de segurança fornece recomendações acionáveis, juntamente com uma classificação de gravidade, e orientações sobre como corrigir o problema. Para obter detalhes das recomendações da central de segurança para contêineres, consulte a [lista de recomendações de referência](recommendations-reference.md#recs-containers).

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.

## <a name="environment-hardening"></a>Proteção do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker

A central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o [benchmark do Docker do CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. Quando ele encontra configurações incorretas, a central de segurança gera recomendações de segurança. Use a **página recomendações** da central de segurança para exibir recomendações e corrigir problemas. As verificações de parâmetro de comparação de CIS não são executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

Quando você estiver explorando os problemas de segurança de uma VM, a central de segurança fornecerá informações adicionais sobre os contêineres no computador. Essas informações incluem a versão do Docker e o número de imagens em execução no host. 

Para monitorar contêineres não gerenciados hospedados em VMs do Linux IaaS, habilite o [Azure defender para servidores](defender-for-servers-introduction.md)opcionais.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoramento contínuo de seus clusters kubernetes
A central de segurança funciona junto com o AKS (serviço de kubernetes do Azure), o serviço de orquestração de contêiner gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

O AKS fornece controles de segurança e visibilidade da postura de segurança de seus clusters. A central de segurança usa esses recursos para:
* Monitore constantemente a configuração de seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Práticas recomendadas de proteção de carga de trabalho usando o controle de admissão kubernetes

Instale o  **complemento Azure Policy para kubernetes** para obter um pacote de recomendações para proteger as cargas de trabalho de seus contêineres do kubernetes.

Conforme explicado neste [Azure Policy para a página kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), o complemento estende o webhook do controlador de admissão do [gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)   de software livre para abrir o [agente de política](https://www.openpolicyagent.org/). Os controladores de admissão kubernetes são plug-ins que impõem como os clusters são usados. O complemento é registrado como um gancho da Web para o controle de admissão kubernetes e possibilita a aplicação de imposição em escala e proteções em seus clusters de maneira centralizada e consistente. 

Quando você tiver instalado o complemento em seu cluster do AKS, todas as solicitações ao servidor de API do kubernetes serão monitoradas em relação ao conjunto predefinido de práticas recomendadas antes de serem persistidas no cluster. Em seguida, você pode configurar o para **impor** as práticas recomendadas e mandato-las para cargas de trabalho futuras. 

Por exemplo, você pode obrigar que contêineres com privilégios não devam ser criados, e quaisquer futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [proteger suas cargas de trabalho do kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Proteção em tempo de execução para nós e clusters AKS

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você aprendeu sobre os principais elementos da segurança de contêiner na central de segurança do Azure. Para material relacionado, consulte:

- [Introdução ao Azure defender para kubernetes](defender-for-kubernetes-introduction.md)
- [Introdução ao Azure defender para registros de contêiner](defender-for-container-registries-introduction.md)