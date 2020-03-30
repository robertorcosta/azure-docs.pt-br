---
title: incluir arquivo
description: incluir arquivo
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325786"
---
1. Faça login no [portal Azure].

2. Clique **em Criar um recurso**.

3. Na caixa de pesquisa, digite **Web App**.
    
4. Na lista de resultados, selecione **Web App** do Marketplace.

5. Selecione seu **Grupo de Assinaturas** e **Recursos** (selecione um grupo de recursos existente _ou_ crie um novo (usando o mesmo nome do seu aplicativo)).

6. Escolha um **nome** exclusivo do seu aplicativo web.

7. Escolha a opção **Publicar** padrão como **Código**.

8. Na **pilha De tempo de execução,** você precisa selecionar uma versão em **ASP.NET** ou **Node**. Se você estiver construindo um backend .NET, selecione uma versão em ASP.NET. Caso contrário, se você estiver mirando em um aplicativo baseado em nó, selecione uma das versões do Nó.

9. Selecione o **sistema operacional**certo, seja Linux ou Windows. 

10. Selecione a **Região** onde deseja que este aplicativo seja implantado. 

11. Selecione o Plano de **Serviço de Aplicativo** apropriado e aperte Revisão e **crie**. 

12. Em **Grupo de Recursos**, selecione um grupo de recursos existente _ou_ crie um novo (usando o mesmo nome que o aplicativo).

13. Clique em **Criar**. Aguarde alguns minutos para que o serviço seja implantado com êxito antes de continuar. Observe o ícone de notificações (sino) no cabeçalho do portal para atualizações de status.

14. Uma vez concluída a implantação, clique na seção **Desemeamento de detalhes** e clique no recurso do tipo **Microsoft.Web/sites**. Ele irá navegar até o App Service Web App que você acabou de criar. 

15. Clique na **lâmina de configuração** em **Configurações** e nas configurações do **aplicativo,** clique no botão **Configuração Do novo aplicativo.**

16. Na página **de configuração de aplicativo Adicionar/Editar,** digite **Nome** como **MobileAppsManagement_EXTENSION_VERSION** e Valor como **mais recente** e aperte OK.

Você está pronto para usar este aplicativo web recém-criado do App Service como um aplicativo mobile.

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com/