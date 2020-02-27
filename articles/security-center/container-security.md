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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 62728f5b66825eb6698e37bb7ad3adbad831b465
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615341"
---
# <a name="container-security-in-security-center"></a>Segurança do contêiner na central de segurança

A central de segurança do Azure é a solução Azure-Native para segurança de contêiner. A central de segurança também é o ideal painel único de experiência de vidro para a segurança de suas cargas de trabalho de nuvem, VMs, servidores e contêineres.

Este artigo descreve como a central de segurança ajuda você a melhorar, monitorar e manter a segurança de seus contêineres e seus aplicativos. Você aprenderá como a central de segurança ajuda com esses aspectos principais da segurança do contêiner:

* Gerenciamento de vulnerabilidades
* Proteção do ambiente do contêiner
* Proteção de tempo de execução

[![guia Segurança do contêiner da central de segurança do Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Para obter instruções sobre como usar esses recursos, consulte [monitorando a segurança de seus contêineres](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images-preview"></a>Gerenciamento de vulnerabilidades-verificação de imagens de contêiner (visualização)
Para monitorar seu registro de contêiner do Azure baseado em ARM, verifique se você está na camada Standard da central de segurança (consulte [preços](/azure/security-center/security-center-pricing)). Em seguida, habilite o pacote opcional de registros de contêiner. Quando uma nova imagem é enviada por push, a central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys.

Quando forem encontrados problemas – por Qualys ou pela central de segurança – você será notificado no painel da central de segurança. Para cada vulnerabilidade, a central de segurança fornece recomendações acionáveis, juntamente com uma classificação de gravidade, e orientações sobre como corrigir o problema. Para obter detalhes das recomendações da central de segurança para contêineres, consulte a [lista de recomendações de referência](recommendations-reference.md#recs-containers).

## <a name="environment-hardening"></a>Proteção do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker
A central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o [benchmark do Docker do CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. Quando ele encontra configurações incorretas, a central de segurança gera recomendações de segurança. Use a **página recomendações** para exibir as recomendações e corrigir os problemas. Você também verá as recomendações na guia **contêineres** que exibe todas as máquinas virtuais implantadas com o Docker. 

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

Quando você estiver explorando os problemas de segurança de uma VM, a central de segurança fornecerá informações adicionais sobre os contêineres no computador. Essas informações incluem a versão do Docker e o número de imagens em execução no host. 

>[!NOTE]
> Essas verificações de parâmetro de comparação de CIS não serão executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Monitoramento contínuo de seus clusters kubernetes (visualização)
A central de segurança funciona junto com o AKS (serviço de kubernetes do Azure), o serviço de orquestração de contêiner gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

O AKS fornece controles de segurança e visibilidade da postura de segurança de seus clusters. A central de segurança usa esses recursos para:
* Monitore constantemente a configuração de seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

## <a name="run-time-protection---real-time-threat-detection"></a>Proteção de tempo de execução-detecção de ameaças em tempo real

A central de segurança fornece detecção de ameaças em tempo real para seus ambientes em contêineres e gera alertas para atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

Detectamos ameaças no nível de cluster host e AKS. Para obter detalhes completos, consulte [proteção contra ameaças para contêineres do Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Perguntas frequentes sobre segurança do contêiner

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quais tipos de imagens a central de segurança do Azure pode verificar?
A central de segurança examina as imagens baseadas no SO Linux que fornecem acesso ao shell. 

O scanner Qualys não dá suporte a imagens de super plataforma, como imagens de [rascunho do Docker](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contêm apenas seu aplicativo e suas dependências de tempo de execução (sem um Gerenciador de pacotes, Shell ou sistema operacional).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Como Digitalizamos a central de segurança do Azure digitalizar uma imagem?
A imagem é extraída do registro. Em seguida, ele é executado em uma área restrita isolada com o scanner Qualys, que extrai uma lista de vulnerabilidades conhecidas.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Com que frequência a central de segurança do Azure verifica minhas imagens?
As verificações de imagem são disparadas em cada Push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da verificação por meio da API REST?
Sim. Os resultados estão sob a [API REST de subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o grafo de recursos do Azure (ARG), a API do tipo Kusto para todos os seus recursos: uma consulta pode buscar uma verificação específica.
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança de contêiner na central de segurança do Azure, consulte estes artigos relacionados:

* Para exibir a postura de segurança de seus recursos relacionados ao contêiner, consulte a seção Contêineres de [proteger seus computadores e aplicativos](security-center-virtual-machine-protection.md#containers).

* Detalhes da [integração com o serviço kubernetes do Azure](azure-kubernetes-service-integration.md)

* Detalhes da [integração com o registro de contêiner do Azure](azure-container-registry-integration.md)