---
title: Executar manualmente Azure Functions não disparadas por HTTP
description: Use uma solicitação HTTP para executar Azure Functions não disparadas por HTTP
author: craigshoemaker
ms.topic: article
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6571482d738549d2708fd8ab23eaf8c9f6fb1f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80892352"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não disparada por HTTP

Este artigo demonstra como executar manualmente uma função não disparada por HTTP por meio de solicitação HTTP especialmente formatada.

Em alguns contextos, talvez seja necessário executar "sob demanda" uma Função do Azure disparada indiretamente.  Exemplos de gatilhos indiretos incluem [funções em um agendamento](./functions-create-scheduled-function.md) ou funções que são executadas como resultado da [ação do outro recurso](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) é usado no exemplo a seguir, mas você pode usar [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) ou qualquer outro como a ferramenta para enviar solicitações HTTP.

## <a name="define-the-request-location"></a>Definir a localização da solicitação

Para executar uma função não disparada por HTTP, você precisa encontrar uma maneira de enviar uma solicitação ao Azure para executar a função. A URL usada para fazer essa solicitação usa um formulário específico.

![Definir a localização da solicitação: nome de host + caminho da pasta + nome da função](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome de host:** A localização pública do aplicativo de funções que é composta pelo nome do aplicativo de funções mais *azurewebsites.net* ou seu domínio personalizado.
- **Caminho da pasta:** Para acessar as funções não disparadas por HTTP por meio de uma solicitação HTTP, você precisa enviar a solicitação por meio das pastas *admin/funções*.
- **Nome da função:** O nome da função que você deseja executar.

Você pode usar esta localização de solicitação no Postman, juntamente com a chave mestra da função da solicitação para o Azure para executar a função.

> [!NOTE]
> Ao executar localmente, a chave mestra da função não é necessária. É possível [chamar a função](#call-the-function) diretamente omitindo o cabeçalho `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Obter a chave mestra da função

Navegue até sua função no portal do Azure e clique em **Gerenciar** e localize a seção **Chaves de Host**. Clique no botão **Copiar** na *_master* para copiar a chave mestra para a área de transferência.

![Copiar chave mestra de tela Gerenciamento de Função](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Depois de copiar a chave mestra, clique no nome da função para retornar à janela de arquivo de código. Em seguida, clique na guia **Logs**. Você verá mensagens da função registrada aqui quando executar manualmente a função do Postman.

> [!CAUTION]  
> Devido às permissões elevadas no aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em um aplicativo.

## <a name="call-the-function"></a>Chamar a função

Abra Postman e siga estas etapas:

1. Insira a **localização de solicitação na caixa de texto URL**.
2. Garanta que o método HTTP seja definido como **POST**.
3. **Clique** na guia **Cabeçalhos**.
4. Insira **x-functions-key** como a primeira **chave** e cole a chave mestra (da área de transferência) na caixa **valor**.
5. Insira **Content-Type** como a segunda **chave** e insira **application/json** como o **valor**.

    ![Configurações de cabeçalhos do Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Clique** na guia **Corpo**.
7. Insira **{ "input": "test" }** como o corpo da solicitação.

    ![Configurações de corpo do Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Clique em **Enviar**.

    ![Enviando uma solicitação com Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

O Postman então relata um status **202 Aceito**.

Em seguida, volte à sua função no portal do Azure. Localize a janela *Logs* e verá mensagens provenientes da chamada manual para a função.

![Resultados de log de função da chamada manual](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Próximas etapas

- [Estratégias para testar seu código no Azure Functions](./functions-test-a-function.md)
- [Depuração local do gatilho da Grade de Eventos do Azure Functions](./functions-debug-event-grid-trigger-local.md)
