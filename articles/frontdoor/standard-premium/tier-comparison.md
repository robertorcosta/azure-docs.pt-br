---
title: Comparação de SKU Standard/Premium da porta do Azure
description: Este artigo fornece uma visão geral das diferenças de SKU Standard e Premium da porta do Azure e dos recursos entre elas.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098545"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Visão geral do SKU Standard/Premium da porta do Azure (versão prévia)

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

A porta frontal do Azure é oferecida para 3 SKUs diferentes, [porta frontal do](../front-door-overview.md)Azure, Azure front door padrão (versão prévia) e Azure front door Premium (visualização). Os SKUs Standard/Premium da porta do Azure combinam recursos da porta frontal do Azure, a CDN padrão do Azure da Microsoft, o Azure WAF em uma única plataforma CDN de nuvem segura com proteção inteligente contra ameaças.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para saber mais, confira os [**Termos de uso complementares das versões prévias do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* A **SKU padrão do Azure front door** é:

    * Fornecimento de conteúdo otimizado
    * Oferecendo aceleração de conteúdo estático e dinâmico
    * Balanceamento de carga global
    * Descarregamento de SSL
    * Gerenciamento de domínio e certificado
    * Análise de tráfego aprimorada 
    * Recursos básicos de segurança

* O **SKU Premium da porta do Azure baseia-** se nos recursos do SKU Standard e adiciona:

    * Amplos recursos de segurança em WAF
    * Proteção de BOT
    * Suporte a link privado
    * Integração com o Microsoft Threat Intelligence e a análise de segurança. 

![Diagrama mostrando uma comparação entre SKUs de porta frontal.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Comparação de recursos

| Recurso |      Standard      |  Premium |
|----------|:-------------:|------:|
| Domínios personalizados | Sim | Sim |
| Descarregamento de SSL | Sim | Sim |
| Cache |  Sim  | Sim |
| Compactação | Sim | Sim   |
| Balanceamento de carga global | Sim  | Sim |
| Roteamento de camada 7 | Sim | Sim |
| Reconfiguração de URL | Sim | Sim |
| Mecanismo de Regras | Sim | Sim |
| Origem privada (link privado) | Não | Sim |
| WAF | Não | Sim |
| Proteção de bot | Não | Sim |
| Diagnósticos e métricas aprimorados | Sim | Sim |
| Relatórios de tráfego | Sim | Sim |
| Relatório de segurança | Não | Sim | 

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta frontal](create-front-door-portal.md)
