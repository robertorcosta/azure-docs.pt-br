---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 12/12/2019
ms.custom: include file
ms.openlocfilehash: 6fa21d78d766ee8e541d6d60f64628b55a84fe41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466910"
---
> [!NOTE]
> Normalmente, solicitações com várias partes requerem três partes:
> * Um **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Um **Content-Disposition**:
>   * `form-data; name="metadata"`
> * O conteúdo do arquivo que será carregado
>
> **Content-Type** e **Content-Disposition** variam dependendo do cenário de uso.

Solicitações com várias partes podem ser feitas de forma programática (usando C#), por meio de um cliente REST ou de uma ferramenta como [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Ferramentas de cliente REST podem ter diferentes níveis de suporte para solicitações com várias partes complexas. As definições de configuração também podem variar ligeiramente de uma ferramenta para outra. Verifique qual ferramenta é mais adequada às suas necessidades.

> [!IMPORTANT]
> As solicitações com várias partes feitas para as APIs de Gerenciamento dos Gêmeos Digitais do Azure normalmente são divididas em duas partes:
> * metadados de blob (como um tipo MIME associado), declarados por **Content-Type** e/ou **Content-Disposition**
> * Conteúdo do blob, que inclui o conteúdo não estruturado de um arquivo a ser carregado
>
> Nenhuma das duas partes é necessária para solicitações de **PATCH**. Ambos são necessários para **POST** ou criam operações.

O [Código-fonte do Início Rápido da Ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém exemplos de C# completos que demonstram como fazer solicitações com várias partes para as APIs de Gerenciamento dos Gêmeos Digitais do Azure.