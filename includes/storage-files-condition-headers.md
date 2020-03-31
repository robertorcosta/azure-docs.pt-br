---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737481"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Condição de erroCabeçalhosNão suportados de um aplicativo da Web usando arquivos Azure do navegador

O erro ConditionHeadersNotSupported ocorre ao acessar conteúdo hospedado em Arquivos Azure através de um aplicativo que faz uso de cabeçalhos condicionais, como um navegador da Web, o acesso falha. O erro afirma que os cabeçalhos de condição não são suportados.

![Erro de cabeçalhos condicionais do Azure Files](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Os cabeçalhos condicionais ainda não estão suportados. Os aplicativos que os implementarem precisarão solicitar o arquivo completo toda vez que o arquivo for acessado.

### <a name="workaround"></a>Solução alternativa

Quando um novo arquivo é carregado, a propriedade de controle de cache por padrão é "sem cache". Para forçar o aplicativo a solicitar o arquivo todas as vezes, a propriedade de controle de cache do arquivo precisa ser atualizada de "no-cache" para "no-cache, no-store, must-revalid". Isso pode ser conseguido usando [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modificação do cache do explorador de armazenamento para cabeçalhos condicionais do Azure Files](media/storage-files-condition-headers/storage-explorer-cache.png)