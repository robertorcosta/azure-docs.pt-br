---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945185"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação oficialmente suportados `/home/site/wwwroot` fazem alterações nos arquivos da pasta do seu aplicativo. Esses arquivos são usados para executar o seu aplicativo. Portanto, a implantação pode falhar por causa de arquivos bloqueados. O aplicativo também pode se comportar de forma imprevisível durante a implantação, porque nem todos os arquivos atualizados ao mesmo tempo. Isso é indesejável para um aplicativo voltado para o cliente. Há algumas maneiras diferentes para evitar esses problemas:

- [Execute seu aplicativo diretamente do pacote ZIP](../articles/app-service/deploy-run-package.md) sem desempacotá-lo.
- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [Lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) habilitada. 
