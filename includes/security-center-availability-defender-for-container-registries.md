---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95529972"
---
## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|o **Azure Defender para registros de contêiner** é cobrado conforme mostrado [na página de preços](../articles/security-center/security-center-pricing.md)|
|Registros e imagens com suporte:|Imagens do Linux em registros do ACR acessíveis na Internet pública com acesso de shell|
|Registros e imagens sem suporte:|Imagens do Windows<br>Registros "privados"<br>Registros com acesso limitado com um firewall, ponto de extremidade de serviço ou pontos de extremidades privados, como o Link Privado do Azure<br>Imagens extremamente minimalistas, como imagens [scratch do Docker](https://hub.docker.com/_/scratch/), ou imagens "Distroless" que contêm apenas um aplicativo e as dependências de runtime dele sem gerenciador de pacotes, shell nem SO|
|Funções e permissões necessárias:|**Leitor de segurança** e [funções e permissões do Registro de Contêiner do Azure](../articles/container-registry/container-registry-roles.md)|
|Nuvens:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Nuvens comerciais<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov – no momento, apenas o recurso de verificação ao efetuar push é compatível. Saiba mais em [Quando as imagens são verificadas?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: Governo da China, outros governos|
|||