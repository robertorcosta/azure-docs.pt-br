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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800589"
---
# <a name="container-security-in-security-center"></a>Segurança do contêiner na central de segurança

A central de segurança do Azure é a solução nativa do Azure para proteger seus contêineres. A central de segurança pode proteger os seguintes tipos de recursos de contêiner:



|Recurso |Nome  |Detalhes  |
|:---------:|---------|---------|
|![Host do contêiner](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Hosts de contêiner (máquinas virtuais que estão executando o Docker)|A Central de Segurança examina as configurações do Docker e fornece visibilidade sobre configurações incorretas, oferecendo uma lista de todas as regras com falha que foram avaliadas. A Central de Segurança fornece diretrizes para ajudá-lo a resolver esses problemas rapidamente e economizar tempo. A Central de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente delas.|
|![Serviço do Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Clusters do AKS (serviço kubernetes do Azure)|Tenha uma visibilidade mais profunda dos nós AKS, do tráfego de nuvem e dos controles de segurança com o [pacote AKs opcional da central de segurança](azure-kubernetes-service-integration.md) para usuários da camada Standard.|
|![Registro de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Registros de ACR (registro de contêiner do Azure)|Tenha uma visibilidade mais profunda das vulnerabilidades das imagens em seus registros ACR baseados em ARM com o [pacote ACR opcional da central de segurança](azure-kubernetes-service-integration.md) para usuários da camada Standard.|
||||


Este artigo descreve como você pode usar esses pacotes para melhorar, monitorar e manter a segurança de seus contêineres e seus aplicativos. Você aprenderá como a central de segurança ajuda com esses aspectos principais da segurança do contêiner:

- [Gerenciamento de vulnerabilidades-verificação de imagens de contêiner](#vulnerability-management---scanning-container-images)
- [Fortalecimento do ambiente-monitoramento contínuo de seus clusters de configuração e kubernetes do Docker](#environment-hardening)
- [Proteção de tempo de execução-detecção de ameaças em tempo real](#run-time-protection---real-time-threat-detection)

[![Guia Segurança do contêiner da central de segurança do Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Para obter instruções sobre como usar esses recursos, consulte [monitorando a segurança de seus contêineres](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Gerenciamento de vulnerabilidades-verificação de imagens de contêiner
Para monitorar seu registro de contêiner do Azure baseado em ARM, verifique se você está na camada Standard da central de segurança (consulte [preços](/azure/security-center/security-center-pricing)). Em seguida, habilite o pacote opcional de registros de contêiner. Quando uma nova imagem é enviada por push, a central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys.

Quando forem encontrados problemas – por Qualys ou pela central de segurança – você será notificado no painel da central de segurança. Para cada vulnerabilidade, a central de segurança fornece recomendações acionáveis, juntamente com uma classificação de gravidade, e orientações sobre como corrigir o problema. Para obter detalhes das recomendações da central de segurança para contêineres, consulte a [lista de recomendações de referência](recommendations-reference.md#recs-containers).

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.

## <a name="environment-hardening"></a>Proteção do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker
A central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o [benchmark do Docker do CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. Quando ele encontra configurações incorretas, a central de segurança gera recomendações de segurança. Use a **página recomendações** para exibir as recomendações e corrigir os problemas. Você também verá as recomendações na guia **contêineres** que exibe todas as máquinas virtuais implantadas com o Docker. 

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

Quando você estiver explorando os problemas de segurança de uma VM, a central de segurança fornecerá informações adicionais sobre os contêineres no computador. Essas informações incluem a versão do Docker e o número de imagens em execução no host. 

>[!NOTE]
> Essas verificações de parâmetro de comparação de CIS não serão executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoramento contínuo de seus clusters kubernetes
A central de segurança funciona junto com o AKS (serviço de kubernetes do Azure), o serviço de orquestração de contêiner gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

O AKS fornece controles de segurança e visibilidade da postura de segurança de seus clusters. A central de segurança usa esses recursos para:
* Monitore constantemente a configuração de seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes sobre as recomendações relevantes da central de segurança que podem ser exibidas para esse recurso, consulte a [seção contêiner](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

## <a name="run-time-protection---real-time-threat-detection"></a>Proteção de tempo de execução-detecção de ameaças em tempo real

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você aprendeu sobre os principais elementos da segurança de contêiner na central de segurança do Azure. Continue a [monitorar a segurança de seus contêineres](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Monitorando a segurança de seus contêineres](monitor-container-security.md)