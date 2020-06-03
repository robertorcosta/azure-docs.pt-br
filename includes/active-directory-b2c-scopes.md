---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbbc606497b53a0790d667a63b45101a3c18bbc4
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297473"
---
#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **Registros de Aplicativo (versão prévia)** .
1. Selecione o aplicativo *webapi1* para abrir a página de **Visão Geral**.
1. Em **Gerenciar**, selecione **Expor uma API**.
1. Ao lado de **URI do ID do Aplicativo**, selecione o link **Definir**.
1. Substitua o valor padrão (uma GUID) por `api` e selecione **Salvar**. O URI completo é mostrado e deve estar no formato `https://your-tenant-name.onmicrosoft.com/api`. Quando o aplicativo Web solicita um token de acesso à API, ele deve adicionar esse URI como prefixo para cada escopo que você definir para a API.
1. Em **Escopos definidos por esta API**, selecione **Adicionar um escopo**.
1. Insira os valores a seguir para criar um escopo que define o acesso de leitura à API e selecione **Adicionar escopo**:
    1. **Nome do escopo**: `demo.read`
    1. **Nome de exibição de consentimento do administrador**: `Read access to demo API`
    1. **Descrição do consentimento do administrador:** `Allows read access to the demo API`
1. Selecione **Adicionar escopo**, insira os seguintes valores para criar um escopo que define o acesso de leitura à API e selecione **Adicionar escopo**:
    1. **Nome do escopo**: `demo.write`
    1. **Nome de exibição de consentimento do administrador**: `Write access to demo API`
    1. **Descrição do consentimento do administrador:** `Allows write access to the demo API`

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Selecione **Aplicativos**.
1. Selecione o aplicativo *webapi1* para abrir a página de **Propriedades**.
1. Selecione **Escopos publicados**. Os escopos publicados podem ser usados para conceder a um aplicativo cliente certas permissões da API Web.
1. Em **ESCOPO**, insira `demo.read` e, na **DESCRIÇÃO**, insira `Read access to the web API`.
1. Em **ESCOPO**, insira `demo.write` e, na **DESCRIÇÃO**, insira `Write access to the web API`.
1. Clique em **Salvar**.