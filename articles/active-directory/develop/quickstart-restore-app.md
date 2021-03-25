---
title: 'Como: restaurar ou remover um aplicativo excluído recentemente com a plataforma de identidade da Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Neste "como", você aprende a restaurar ou excluir permanentemente um aplicativo excluído recentemente registrado com a plataforma de identidade da Microsoft.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: a5cf8f42bb15d3ce566506d85fac192027a1c69f
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039001"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Restaurar ou remover um aplicativo excluído recentemente com a plataforma de identidade da Microsoft
Depois de excluir um registro de aplicativo, o aplicativo permanecerá em um estado suspenso por 30 dias. Durante esse período de 30 dias, o registro do aplicativo pode ser restaurado, juntamente com todas as suas propriedades. Depois que a janela de 30 dias passa, os registros do aplicativo não podem ser restaurados e o processo de exclusão permanente pode ser iniciado automaticamente.  Essa funcionalidade só se aplica a aplicativos associados a um diretório.  Ele não está disponível para aplicativos de um conta Microsoft pessoal, que não pode ser restaurado.

Você pode exibir os aplicativos excluídos, restaurar um aplicativo excluído ou excluir permanentemente um aplicativo usando a experiência de Registros de aplicativo em Azure Active Directory (AD do Azure) no portal do Azure.

Observe que nem você nem o suporte ao cliente da Microsoft podem restaurar um aplicativo excluído permanentemente ou um aplicativo excluído há mais de 30 dias.

## <a name="required-permissions"></a>Permissões necessárias
Você deve ter uma das funções a seguir para excluir aplicativos permanentemente.

- Administrador global

- Administrador de aplicativos

- Administrador de aplicativos de nuvem

- Administrador de identidade híbrida

- Proprietário do aplicativo

Você deve ter uma das funções a seguir para restaurar aplicativos.

- Administrador global

- Proprietário do aplicativo

## <a name="deleted-applications-ui--preview"></a>Interface do usuário de aplicativos excluídos | Visualizar

> [!IMPORTANT]
> O recurso de interface do usuário do portal de aplicativos excluídos [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

### <a name="view-your-deleted-applications"></a>Exibir seus aplicativos excluídos
Você pode ver todos os aplicativos em um estado de exclusão reversível.  Somente aplicativos excluídos há menos de 30 dias podem ser restaurados.

#### <a name="to-view-your-restorable-applications"></a>Para exibir seus aplicativos restauráveis
1. Entre no [portal do Azure](https://portal.azure.com/).

2. Pesquise e selecione **Azure Active Directory**, selecione **registros de aplicativo** e, em seguida, selecione a guia **aplicativos excluídos (versão prévia)** .

    Examine a lista de aplicativos. Somente os aplicativos que foram excluídos nos últimos 30 dias estão disponíveis para restauração. Se estiver usando a visualização de Registros de aplicativo pesquisa, você poderá filtrar pela coluna "data de exclusão" para ver apenas esses aplicativos.

### <a name="restore-a-recently-deleted-application"></a>Restaurar um aplicativo excluído recentemente

Quando um registro de aplicativo é excluído da organização, o aplicativo fica em um estado suspenso e suas configurações são preservadas. Quando você restaura um registro de aplicativo, suas configurações também são restauradas.  No entanto, se houver configurações específicas da organização em **aplicativos empresariais** para o locatário inicial do aplicativo, elas não serão restauradas.  

Isso ocorre porque as configurações específicas da organização são armazenadas em um objeto separado, chamado de entidade de serviço.  As configurações mantidas na entidade de serviço incluem contenções de permissão e atribuições de usuário e grupo para uma determinada organização; essas configurações não serão restauradas quando o aplicativo for restaurado. Para obter mais informações, consulte [objetos de entidade de serviço e de aplicativo](app-objects-and-service-principals.md). 


#### <a name="to-restore-an-application"></a>Para restaurar um aplicativo
1. Na guia **aplicativos excluídos (versão prévia)** , procure e selecione um dos aplicativos excluídos há menos de 30 dias.

2. Selecione **restaurar registro de aplicativo**.

### <a name="permanently-delete-an-application"></a>Excluir permanentemente um aplicativo
Você pode excluir permanentemente um aplicativo de sua organização manualmente. Um aplicativo excluído permanentemente não pode ser restaurado por você, por outro administrador ou pelo atendimento ao cliente da Microsoft.

#### <a name="to-permanently-delete-an-application"></a>Para excluir permanentemente um aplicativo

1. Na guia **aplicativos excluídos (versão prévia)** , procure e selecione um dos aplicativos disponíveis.

2. Selecione **Excluir permanentemente**.

3. Leia o texto de aviso e selecione **Sim**.

## <a name="next-steps"></a>Próximas etapas
Depois de ter restaurado ou excluído permanentemente seu aplicativo, você pode:

- [Adicionar um aplicativo](quickstart-register-app.md)

- Saiba mais sobre os [objetos de entidade de serviço e aplicativo](app-objects-and-service-principals.md) na plataforma de identidade da Microsoft.
