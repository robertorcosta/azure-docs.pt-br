---
title: Segurança de contêineres no Azure Security Center | Microsoft Docs
description: Conheça os recursos de segurança de contêineres do Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125135"
---
# <a name="container-security-in-security-center"></a>Segurança de contêineres no Centro de Segurança

O Azure Security Center é a solução nativa do Azure para a segurança de contêineres. O Security Center também é o ideal de experiência de vidro para a segurança de suas cargas de trabalho na nuvem, VMs, servidores e contêineres.

Este artigo descreve como o Security Center ajuda você a melhorar, monitorar e manter a segurança de seus contêineres e seus aplicativos. Você aprenderá como o Security Center ajuda com esses aspectos principais da segurança dos contêineres:

* Gerenciamento de vulnerabilidades
* Endurecimento do ambiente do contêiner
* Proteção em tempo de execução

[![Guia de segurança de contêineres do Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Para obter instruções sobre como usar esses recursos, consulte [Monitorando a segurança de seus contêineres](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Gerenciamento de vulnerabilidades - digitalização de imagens de contêineres
Para monitorar o registro de contêineres do Azure baseado em ARM, certifique-se de que você está no nível padrão do Security Center (veja [preços).](/azure/security-center/security-center-pricing) Em seguida, habilite o pacote de registros de contêineres opcional. Quando uma nova imagem é empurrada, o Security Center verifica a imagem usando um scanner do fornecedor de varredura de vulnerabilidades líder do setor, qualys.

Quando os problemas forem encontrados – por Qualys ou Security Center – você será notificado no painel do Security Center. Para cada vulnerabilidade, o Security Center fornece recomendações acionáveis, juntamente com uma classificação de gravidade e orientação sobre como corrigir o problema. Para obter detalhes sobre as recomendações do Security Center para contêineres, consulte a [lista de recomendações de referência](recommendations-reference.md#recs-containers).

O Security Center filtra e classifica as conclusões do scanner. Quando uma imagem é saudável, o Centro de Segurança a marca como tal. O Security Center gera recomendações de segurança apenas para imagens que tenham problemas a serem resolvidos. Ao notificar apenas quando há problemas, o Security Center reduz o potencial de alertas informativos indesejados.

## <a name="environment-hardening"></a>Endurecimento do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker
O Azure Security Center identifica contêineres não gerenciados hospedados em VMs Linux IaaS ou outras máquinas Linux executando contêineres Docker. O Security Center avalia continuamente as configurações desses contêineres. Em seguida, compara-os com o [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

O Security Center inclui todo o conjunto de regras do CIS Docker Benchmark e alerta se seus contêineres não satisfazem nenhum dos controles. Quando encontra configurações erradas, o Security Center gera recomendações de segurança. Use a **página de recomendações** para visualizar recomendações e corrigir problemas. Você também verá as recomendações na guia **Containers** que exibe todas as máquinas virtuais implantadas com o Docker. 

Para obter detalhes sobre as recomendações relevantes do Security Center que podem aparecer para este recurso, consulte a [seção](recommendations-reference.md#recs-containers) de contêiner da tabela de referência de recomendações.

Quando você está explorando os problemas de segurança de uma VM, o Security Center fornece informações adicionais sobre os contêineres na máquina. Essas informações incluem a versão Docker e o número de imagens em execução no host. 

>[!NOTE]
> Essas verificações de benchmark do CIS não serão executadas em instâncias gerenciadas pela AKS ou em VMs gerenciados pela Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoramento contínuo de seus clusters Kubernetes
O Security Center trabalha em conjunto com o Azure Kubernetes Service (AKS), serviço gerenciado de orquestração de contêineres da Microsoft para o desenvolvimento, implantação e gerenciamento de aplicativos contêiner.

A AKS fornece controles de segurança e visibilidade sobre a postura de segurança de seus clusters. O Security Center usa esses recursos para:
* Monitore constantemente a configuração de seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes sobre as recomendações relevantes do Security Center que podem aparecer para este recurso, consulte a [seção](recommendations-reference.md#recs-containers) de contêiner da tabela de referência de recomendações.

## <a name="run-time-protection---real-time-threat-detection"></a>Proteção em tempo de execução - Detecção de ameaças em tempo real

O Security Center fornece detecção de ameaças em tempo real para seus ambientes contêineres e gera alertas para atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

Detectamos ameaças no nível de cluster host e AKS. Para obter detalhes completos, consulte [a detecção de ameaças para contêineres Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Perguntas frequentes sobre segurança de contêineres

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Que tipos de imagens o Azure Security Center pode fazer a varredura?
O Security Center verifica imagens baseadas no Sistema Operacional Linux que fornecem acesso ao shell. 

O scanner Qualys não suporta imagens super minimalistas, como imagens [de arranhões do Docker](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contêm apenas seu aplicativo e suas dependências de tempo de execução sem um gerenciador de pacotes, shell ou sistema operacional.

### <a name="how-does-azure-security-center-scan-an-image"></a>Como o Azure Security Center escaneia uma imagem?
A imagem é retirada do registro. Em seguida, é executado em uma caixa de areia isolada com o scanner Qualys que extrai uma lista de vulnerabilidades conhecidas.

O Security Center filtra e classifica as conclusões do scanner. Quando uma imagem é saudável, o Centro de Segurança a marca como tal. O Security Center gera recomendações de segurança apenas para imagens que tenham problemas a serem resolvidos. Ao notificar apenas quando há problemas, o Security Center reduz o potencial de alertas informativos indesejados.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Com que frequência o Azure Security Center verifica minhas imagens?
As varreduras de imagem são acionadas a cada empurrão.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da varredura através da API REST?
Sim. Os resultados estão em [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o Azure Resource Graph (ARG), a API semelhante ao Kusto para todos os seus recursos: uma consulta pode buscar uma varredura específica.
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança de contêineres no Azure Security Center, consulte estes artigos relacionados:

* Para ver a postura de segurança dos recursos relacionados ao contêiner, consulte a seção de contêineres do [Protect your machines and Applications](security-center-virtual-machine-protection.md#containers).

* Detalhes da [integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Detalhes da [integração com o Registro de Contêineres do Azure](azure-container-registry-integration.md)