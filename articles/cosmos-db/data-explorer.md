---
title: Usar o gerenciador do Azure Cosmos DB para gerenciar os dados
description: O gerenciador do Azure Cosmos DB é uma interface baseada na Web autônoma que permite exibir e gerenciar os dados armazenados no Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: 691a2e56230d312416aed3d68bffd361f1d63558
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008575"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Trabalhar com os dados usando o Azure Cosmos Explorer 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O gerenciador do Azure Cosmos DB é uma interface baseada na Web autônoma que permite exibir e gerenciar os dados armazenados no Azure Cosmos DB. O gerenciador do Azure Cosmos DB é equivalente à guia **Data Explorer** existente disponível no portal do Azure quando você cria uma conta do Azure Cosmos DB. As principais vantagens do gerenciador do Azure Cosmos DB sobre o gerenciador de Data Explorer são:

* Você tem um estado real de tela inteira para exibir os dados, executar consultas, procedimentos armazenados, gatilhos e exibir os resultados.  

* Você pode fornecer acesso temporário ou permanente de leitura ou leitura/gravação à conta do banco de dados e coleções para outros usuários que não tenham acesso à assinatura ou portal do Azure.  

* Você pode compartilhar os resultados da consulta com outros usuários que não tenham acesso à assinatura ou portal do Azure.  

## <a name="access-azure-cosmos-db-explorer"></a>Acessar o gerenciador do Azure Cosmos DB

1. Entre no [Portal do Azure](https://portal.azure.com/). 

2. Em **Todos os recursos**, localize e navegue até a conta do Azure Cosmos DB, selecione Chaves e copie a **Cadeia de Conexão Primária**.  

3. Vá para https://cosmos.azure.com/, cole a cadeia de conexão e selecione **Conectar**. Usando a cadeia de conexão, é possível acessar o gerenciador do Azure Cosmos DB sem quaisquer limites de tempo.  

   Se quiser fornecer a outros usuários o acesso temporário à sua conta do Azure Cosmos DB, você poderá fazer isso usando as URLs de acesso de leitura e leitura/gravação. 

4. Abra a folha **Data Explorer**, selecione **Abrir Modo de Exibição de Tela Inteira**. Na caixa de diálogo pop-up, é possível exibir duas URLs de acesso – **Leitura/Gravação** e **Leitura**. Essas URLs permitem que você compartilhe a conta do Azure Cosmos DB temporariamente com outros usuários. O acesso à conta expira em 24 horas, após esse período será possível conectar novamente usando uma nova URL de acesso ou a cadeia de conexão. 

   **Leitura/Gravação** – Ao compartilhar a URL de leitura/gravação com outros usuários, eles poderão exibir e modificar os bancos de dados, as coleções, consultas e outros recursos associados a essa conta específica.

   **Leitura** - Ao compartilhar a URL somente leitura com outros usuários, eles poderão exibir os bancos de dados, as coleções, consultas e outros recursos associados a essa conta específica. Por exemplo, se quiser compartilhar os resultados de uma consulta com os colegas de equipe que não tenham acesso ao portal do Azure ou à conta do Azure Cosmos DB, forneça essa URL aos colegas.

   Escolha o tipo de acesso com o qual gostaria de abrir a conta e clique em **Abrir**. Após abrir o gerenciador, a experiência é a mesma que você tinha com a guia Data Explorer no portal do Azure.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Abrir Azure Cosmos DB Explorer":::

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a experiência de **Abrir Modo de Exibição de Tela Inteira** que permite compartilhar acesso de leitura ou leitura/gravação temporário ainda não tem suporte para contas de API de Tabela e Gremlin do Azure Cosmos DB. Ainda é possível exibir as contas de API de Tabela e Gremlin, passando a cadeia de conexão para o Gerenciador do Azure Cosmos DB. 

No momento, não há suporte para a exibição de documentos que contenham um UUID no Data Explorer. Isso não afeta o carregamento de coleções, apenas a exibição de documentos individuais ou consultas que incluam esses documentos. Para exibir e gerenciar esses documentos, os usuários devem continuar a usar a ferramenta que foi usada originalmente para criar esses documentos.

Os clientes que recebem erros HTTP-401 podem ser devido a permissões do RBAC do Azure insuficientes para a conta do Azure do cliente, especialmente se a conta tiver uma função personalizada. Qualquer função personalizada deve ter `Microsoft.DocumentDB/databaseAccounts/listKeys/*` a ação de usar data Explorer se estiver entrando usando suas credenciais de Azure Active Directory.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como começar a usar o gerenciador do Azure Cosmos DB para gerenciar os dados, em seguida, poderá:

* Começar a definir[consultas](./sql-query-getting-started.md) usando a sintaxe SQL e executar [programação do lado do servidor](stored-procedures-triggers-udfs.md), usando procedimentos armazenados, UDFs, gatilhos.
