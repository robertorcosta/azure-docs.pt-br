---
title: Porta da frente do Azure
description: Este artigo ensina como configurar um cabeçalho de segurança por meio do mecanismo de regras no Azure Front Door
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 4d698375488d4dac551f0028883fc4e18a10d8ef
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323466"
---
# <a name="add-security-headers-with-rules-engine"></a>Adicionar cabeçalhos de segurança com o Mecanismo de Regras

Implemente cabeçalhos de segurança para impedir vulnerabilidades baseadas em navegador como HSTS (HTTP Strict-Transport-Security), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra como adicionar um cabeçalho Content-Security-Policy a todas as solicitações de entrada que correspondem ao caminho definido na rota à qual a configuração do mecanismo de regras está associada. Aqui, só permitiremos a execução de scripts do nosso site confiável, **https://apis.contoso.com** , no aplicativo.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicionar um cabeçalho Content-Security-Policy no portal do Azure

1. Antes de criar essa regra específica, saiba como [criar um front door](quickstart-create-front-door.md) ou como [criar um mecanismo de regras](front-door-tutorial-rules-engine.md) se esta for a primeira vez que estiver usando o AFD ou o recurso de mecanismo de regras.

2. Clique em **Adicionar** para adicionar uma nova regra. Forneça um nome para a regra e clique em **Adicionar uma Ação** > **Cabeçalho de Resposta**.

3. Defina o Operador como **Acréscimo** para adicionar esse cabeçalho como uma resposta a todas as solicitações de entrada para essa rota.

4. Adicione o nome do cabeçalho: **Content-Security-Policy** e defina os valores que esse cabeçalho deverá aceitar. Nesse cenário, escolhemos *"script-src 'self' https://apis.contoso.com".*

5. Depois de adicionar todas as regras desejadas à configuração, não se esqueça de acessar sua rota preferencial e associar a configuração do mecanismo de regras à regra de rota. Essa etapa é necessária para permitir que a regra funcione. 

![amostra do portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Neste cenário, não adicionamos [condições de correspondência](front-door-rules-engine-match-conditions.md) à regra. Todas as solicitações de entrada que corresponderem ao caminho definido na regra de rota terão essa regra aplicada. Caso deseje que ela só se aplique a um subconjunto dessas solicitações, lembre-se de adicionar as condições de correspondência específicas a essa regra.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Mecanismo de regras de AFD](front-door-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
- Saiba mais sobre as [condições de correspondência](front-door-rules-engine-match-conditions.md) do mecanismo de regras
- Confira mais na [referência da CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest) do Mecanismo de regras do AFD. 
- Confira mais na [referência do PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0) do Mecanismo de regras do AFD. 
