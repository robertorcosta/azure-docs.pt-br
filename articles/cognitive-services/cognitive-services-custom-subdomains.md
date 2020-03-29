---
title: Subdomínios personalizados
titleSuffix: Azure Cognitive Services
description: Nomes de subdomínio personalizados para cada recurso do Serviço Cognitivo são criados através do portal Azure, Azure Cloud Shell ou Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647686"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomes de subdomínio personalizados para serviços cognitivos

Os Serviços Cognitivos do Azure usam nomes de subdomínio personalizados para cada recurso criado através do [portal Azure](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Ao contrário dos pontos finais regionais, que eram comuns para todos os clientes em uma região específica do Azure, nomes de subdomínio personalizados são exclusivos do recurso. Nomes de subdomínio personalizados são necessários para habilitar recursos como o Azure Active Directory (Azure AD) para autenticação.

## <a name="how-does-this-impact-existing-resources"></a>Como isso impacta os recursos existentes?

Os recursos dos Serviços Cognitivos criados até 1º de julho de 2019 utilizarão os pontos finais regionais para o serviço associado. Esses pontos finais funcionarão com recursos existentes e novos.

Se você quiser migrar um recurso existente para aproveitar nomes de subdomínio personalizados, para que você possa habilitar recursos como o Azure AD, siga estas instruções:

1. Faça login no portal Do Zure e localize o recurso de Serviços Cognitivos ao quais você gostaria de adicionar um nome de subdomínio personalizado.
2. Na **lâmina Visão Geral,** localize e selecione **Gerar nome de domínio personalizado**.
3. Isso abre um painel com instruções para criar um subdomínio personalizado exclusivo para o seu recurso.
   > [!WARNING]
   > Depois de criar um nome de subdomínio personalizado, ele **não pode** ser alterado.

## <a name="do-i-need-to-update-my-existing-resources"></a>Preciso atualizar meus recursos existentes?

Não. O ponto final regional continuará a funcionar para novos e existentes Serviços Cognitivos e o nome de subdomínio personalizado é opcional. Mesmo que um nome de subdomínio personalizado seja adicionado, o ponto final regional continuará a trabalhar com o recurso.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>E se um SDK me pedir um recurso para a região?

> [!WARNING]
> Os Serviços de Fala **não suportam** subdomínios personalizados no momento. Por favor, use os pontos finais regionais ao usar os Serviços de Fala e SDKs associados.

Os pontos finais regionais e os nomes de subdomínio personalizados são suportados e podem ser usados de forma intercambiável. No entanto, o ponto final completo é necessário.

As informações da região estão disponíveis na lâmina **Visão Geral** para o seu recurso no [portal Azure](https://portal.azure.com). Para a lista completa de pontos finais regionais, [veja Existe uma lista de pontos finais regionais?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Nomes de subdomínio personalizados são regionais?

Sim. O uso de um nome de subdomínio personalizado não altera nenhum dos aspectos regionais do recurso de serviços cognitivos.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quais são os requisitos para um nome de subdomínio personalizado?

Um nome de subdomínio personalizado é exclusivo do seu recurso. O nome só pode incluir caracteres `-` alfanuméricos e o caractere; deve ter entre 2 e 64 caracteres `-`de comprimento e não pode terminar com um .

## <a name="can-i-change-a-custom-domain-name"></a>Posso alterar um nome de domínio personalizado?

Não. Depois que um nome de subdomínio personalizado é criado e associado a um recurso, ele não pode ser alterado.

## <a name="can-i-reuse-a-custom-domain-name"></a>Posso reutilizar um nome de domínio personalizado?

Cada nome de subdomínio personalizado é único, portanto, para reutilizar um nome de subdomínio personalizado que você atribuiu a um recurso de Serviços Cognitivos, você precisará excluir o recurso existente. Depois que o recurso for excluído, você pode reutilizar o nome de subdomínio personalizado.

## <a name="is-there-a-list-of-regional-endpoints"></a>Existe uma lista de pontos finais regionais?

Sim. Esta é uma lista de pontos finais regionais que você pode usar com os recursos do Azure Cognitive Services.

> [!NOTE]
> As APIs de pesquisa de texto do tradutor e de pesquisa de bing usam pontos finais globais.

| Tipo de ponto de extremidade | Região | Ponto de extremidade |
|---------------|--------|----------|
| Público | Global (Texto tradutor & Bing) | `https://api.cognitive.microsoft.com` |
| | Leste da Austrália | `https://australiaeast.api.cognitive.microsoft.com` |
| | Sul do Brasil | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canadá Central | `https://canadacentral.api.cognitive.microsoft.com` |
| | Centro dos EUA | `https://centralus.api.cognitive.microsoft.com` |
| | Leste da Ásia | `https://eastasia.api.cognitive.microsoft.com` |
| | Leste dos EUA | `https://eastus.api.cognitive.microsoft.com` |
| | Leste dos EUA 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | França Central | `https://francecentral.api.cognitive.microsoft.com` |
| | Centro da Índia | `https://centralindia.api.cognitive.microsoft.com` |
| | Leste do Japão | `https://japaneast.api.cognitive.microsoft.com` |
| | Coreia Central | `https://koreacentral.api.cognitive.microsoft.com` |
| | Centro-Norte dos EUA | `https://northcentralus.api.cognitive.microsoft.com` |
| | Norte da Europa | `https://northeurope.api.cognitive.microsoft.com` |
| | Norte da África do Sul | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Centro-Sul dos Estados Unidos | `https://southcentralus.api.cognitive.microsoft.com` |
| | Sudeste Asiático | `https://southeastasia.api.cognitive.microsoft.com` |
| | Sul do Reino Unido | `https://uksouth.api.cognitive.microsoft.com` |
| | Centro-Oeste dos EUA | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa Ocidental | `https://westeurope.api.cognitive.microsoft.com` |
| | Oeste dos EUA | `https://westus.api.cognitive.microsoft.com` |
| | Oeste dos EUA 2 | `https://westus2.api.cognitive.microsoft.com` |
| Gov dos EUA | Gov. dos EUA – Virgínia | `https://virginia.api.cognitive.microsoft.us` |
| China | Leste da China 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Norte da China | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Confira também

* [O que são os Serviços Cognitivos?](Welcome.md)
* [Autenticação](authentication.md)
