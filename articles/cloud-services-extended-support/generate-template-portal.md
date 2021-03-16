---
title: Gerar modelo ARM para serviços de nuvem (suporte estendido) usando o portal do Azure
description: Gerar e baixar o modelo do ARM e o arquivo de parâmetro para serviços de nuvem (suporte estendido) usando o portal do Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555228"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Gerar modelo ARM para serviços de nuvem (suporte estendido) usando o portal do Azure

Este artigo explica como obter o modelo do ARM e o arquivo de parâmetro do [portal do Azure](https://portal.azure.com) após a implantação do serviço de nuvem (suporte estendido). O modelo ARM e o arquivo de parâmetro podem ser usados em implantações futuras para atualizar ou atualizar um serviço de nuvem (suporte estendido)

## <a name="get-arm-template-via-portal"></a>Obter modelo do ARM por meio do portal

  1. Vá para o grupo de recursos e selecione implantações.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="Imagem mostra a seleção de implantações no grupo de recursos na portal do Azure.":::
  
  2. Selecione seu serviço de nuvem (suporte estendido) e clique em modelo.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="Imagem mostra a seleção de modelo em serviço de nuvem (suporte estendido) no portal do Azure.":::
  
  3. Baixe seus arquivos de modelo e parâmetro. Eles podem ser usados para implantações futuras por meio do PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="Imagem mostra o download do arquivo de modelo no portal do Azure.":::
  
## <a name="next-steps"></a>Próximas etapas 
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implantar um serviço de nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md)
  
