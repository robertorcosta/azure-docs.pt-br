---
title: Central de segurança do Azure e registro de contêiner do Azure
description: Saiba mais sobre a integração da central de segurança do Azure com o registro de contêiner do Azure
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
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970460"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integração do registro de contêiner do Azure com a central de segurança

O ACR (registro de contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no registro do Docker 2,0 de código aberto.

Se você estiver na camada Standard da central de segurança do Azure, poderá adicionar o pacote de registros de contêiner. Esse recurso opcional traz uma visibilidade mais profunda das vulnerabilidades das imagens em seus registros baseados em ARM. Habilite ou desabilite o pacote no nível de assinatura para abranger todos os registros em uma assinatura. Esse recurso é cobrado por imagem, conforme mostrado na [página de preços](security-center-pricing.md). Habilitando o pacote de registros de contêiner, o garante que a central de segurança esteja pronta para verificar imagens que são enviadas por push ao registro. 


## <a name="availability"></a>Disponibilidade

- Estado da versão: **disponibilidade geral**
- Funções necessárias: **leitor de segurança** e [função de leitor do registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Nuvens: 
    - ✔ Nuvens comerciais
    - Nuvem do governo ✘ dos EUA
    - Nuvem do governo ✘ na China, outras nuvens gov


## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Sempre que uma imagem é enviada para o registro, a central de segurança verifica a imagem automaticamente. Para disparar a verificação de uma imagem, envie-a por push para o repositório.

Quando a verificação for concluída (normalmente após cerca de 10 minutos, mas pode ter até 40 minutos), as descobertas estarão disponíveis como recomendações da central de segurança como esta:

[![Exemplo de recomendação da central de segurança do Azure sobre vulnerabilidades descobertas em uma imagem hospedada do registro de contêiner do Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantagens da integração

A central de segurança identifica registros ACR baseados em ARM em sua assinatura e fornece diretamente:

* **Azure-verificação de vulnerabilidades nativas** para todas as imagens do Linux enviadas por push. A central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys. Essa solução nativa é totalmente integrada por padrão.

* **Recomendações de segurança** para imagens do Linux com vulnerabilidades conhecidas. A central de segurança fornece detalhes de cada vulnerabilidade relatada e uma classificação de gravidade. Além disso, ele fornece orientação sobre como corrigir as vulnerabilidades específicas encontradas em cada imagem enviada por push para o registro.

![Visão geral de alto nível da central de segurança do Azure e do ACR (registro de contêiner do Azure)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>Perguntas frequentes sobre ACR com a central de segurança

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quais tipos de imagens a central de segurança do Azure pode verificar?
A central de segurança examina as imagens baseadas no SO Linux que fornecem acesso ao shell. 

O scanner Qualys não dá suporte a imagens de super plataforma, como imagens de [rascunho do Docker](https://hub.docker.com/_/scratch/) , ou imagens "Distroless" que contêm apenas seu aplicativo e suas dependências de tempo de execução sem um Gerenciador de pacotes, Shell ou sistema operacional.

### <a name="how-does-azure-security-center-scan-an-image"></a>Como a central de segurança do Azure examina uma imagem?
A imagem é extraída do registro. Em seguida, ele é executado em uma área restrita isolada com o scanner Qualys, que extrai uma lista de vulnerabilidades conhecidas.

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Com que frequência a central de segurança do Azure verifica minhas imagens?
As verificações de imagem são disparadas em cada Push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da verificação por meio da API REST?
Sim. Os resultados estão sob a [API REST de subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o grafo de recursos do Azure (ARG), a API do tipo Kusto para todos os seus recursos: uma consulta pode buscar uma verificação específica.
 



## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o Serviço de Kubernetes do Azure](azure-kubernetes-service-integration.md)

* [Proteção da máquina virtual](security-center-virtual-machine-protection.md) – descreve as recomendações da central de segurança
