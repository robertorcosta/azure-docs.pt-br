---
title: Centro de Segurança Azure e Registro de Contêineres Azure
description: Saiba mais sobre a integração do Azure Security Center com o Registro de Contêineres do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 1c1b48d3715d838827f88f99fc0849d25677fdcc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585734"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integração do Registro de Contêineres do Azure com o Security Center

O Acr (ACR) é um serviço de registro docker gerenciado e privado que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. É baseado no Registro Docker de código aberto 2.0.

Se você estiver no nível padrão do Azure Security Center, você pode adicionar o pacote Registros de Contêineres. Esse recurso opcional traz uma visibilidade mais profunda sobre as vulnerabilidades das imagens em seus registros baseados em ARM. Habilite ou desative o pacote no nível de assinatura para cobrir todos os registros em uma assinatura. Esse recurso é cobrado por imagem, como mostrado na [página de preços](security-center-pricing.md). Habilitando o pacote Registros de Contêineres, garante que o Security Center esteja pronto para escanear imagens que são empurradas para o registro. 

Sempre que uma imagem é empurrada para o seu registro, o Security Center verifica automaticamente essa imagem. Para ativar a varredura de uma imagem, empurre-a para o repositório.

Quando a varredura é concluída (normalmente após aproximadamente 10 minutos), os resultados estão disponíveis em recomendações do Security Center como esta:

[![Exemplo de recomendação do Azure Security Center sobre vulnerabilidades descobertas em uma imagem hospedada no Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantagens da integração

O Security Center identifica registros aCR baseados em ARM em sua assinatura e fornece perfeitamente:

* **Varredura de vulnerabilidade nativa do Azure** para todas as imagens do Linux empurradas. O Security Center verifica a imagem usando um scanner do fornecedor de varredura de vulnerabilidades líder do setor, qualys. Esta solução nativa é perfeitamente integrada por padrão.

* **Recomendações de segurança** para imagens Linux com vulnerabilidades conhecidas. O Security Center fornece detalhes de cada vulnerabilidade relatada e uma classificação de gravidade. Além disso, ele dá orientações sobre como remediar as vulnerabilidades específicas encontradas em cada imagem empurrada para o registro.

![Visão geral de alto nível do Azure Security Center e do Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança de contêineres do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contêineres](container-security.md)

* [Integração com o Serviço de Kubernetes do Azure](azure-kubernetes-service-integration.md)

* [Proteção de máquinas virtuais](security-center-virtual-machine-protection.md) - Descreve as recomendações do Security Center