---
title: configurar credenciais de implantação
description: Saiba quais tipos de credenciais de implantação estão no serviço Azure App e como configurá-las e usá-las.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c4e7a66a9535812da505045c26e7b1e6fbc6c661
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669965"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implantação do Serviço de Aplicativo do Azure
O [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714) oferece suporte a dois tipos de credenciais para a [implantação local do Git](deploy-local-git.md) e a [implantação de FTP/S](deploy-ftp.md). Essas credenciais não são as mesmas que suas credenciais de assinatura do Azure.

* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser usado para implantar no Serviço de Aplicativo para qualquer aplicativo e em qualquer assinatura que a conta do Azure tem permissão para acessar. É o conjunto padrão que aparece na GUI do portal (como a **Visão geral** e **Propriedades** da página de recursos [do aplicativo](../azure-resource-manager/manage-resources-portal.md#manage-resources)). Quando um usuário recebe acesso de aplicativo via RBAC (controle de acesso baseado em função) ou permissões de coadministrador, esse usuário pode usar suas próprias credenciais de nível de usuário até que o acesso seja revogado. Não compartilhe essas credenciais com outros usuários do Azure.

* **Credenciais de nível de aplicativo**: um conjunto de credenciais para cada aplicativo. Podem ser usadas para implantar nesse aplicativo somente. As credenciais de cada aplicativo são geradas automaticamente na criação do aplicativo. Eles não podem ser configurados manualmente, mas podem ser redefinidos a qualquer momento. Para que um usuário tenha acesso às credenciais no nível do aplicativo via (RBAC), esse usuário deve ser colaborador ou superior no aplicativo. Os leitores não têm permissão para publicar e não pode acessar essas credenciais.

## <a name="userscope"></a>Configurar credenciais de nível de usuário

Você pode configurar as credenciais de usuário na [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources) de qualquer aplicativo. Independentemente de para qual aplicativo você configura essas credenciais, elas se aplicam a todos os aplicativos e a todas as assinaturas na conta do Azure. 

### <a name="in-the-cloud-shell"></a>No Cloud Shell

Para configurar o usuário de implantação no [Cloud Shell](https://shell.azure.com), execute o comando [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Substitua \<username> e \<password> pelo nome de usuário e a senha do usuário de implantação. 

- O nome de usuário deve ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a senha como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

### <a name="in-the-portal"></a>No portal

No portal do Azure, você deve ter pelo menos um aplicativo para poder acessar a página de credenciais de implantação. Para configurar as credenciais de usuário:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **serviços de aplicativos** >  **\<Any_app >**  > **central de implantação** > **painel**de > de **FTP** .

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ou, se você já tiver configurado a implantação do git, selecione **serviços de aplicativos** >  **&lt;any_app >**  > **central de implantação** > **FTP/credenciais**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecione **credenciais de usuário**, configure o nome de usuário e a senha e, em seguida, selecione **salvar credenciais**.

Depois de definir suas credenciais de implantação, você pode encontrar o nome de usuário da implantação do *git* na página de **visão geral** do seu aplicativo,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implantação do git estiver configurada, a página mostrará um **nome de usuário do git/implantação**; caso contrário, um **nome de usuário de FTP/implantação**.

> [!NOTE]
> O Azure não mostra sua senha de implantação de usuário. Se você esquecer a senha, poderá redefinir suas credenciais seguindo as etapas desta seção.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Usar credenciais de nível de usuário com FTP/FTPS

A autenticação em um ponto de extremidade de FTP/FTPS usando credenciais de nível de usuário exige um nome de usuário no seguinte formato: `<app-name>\<user-name>`

Como as credenciais de nível de usuário são vinculadas ao usuário e não a um recurso específico, o nome de usuários deve estar nesse formato para direcionar a ação de entrada para o ponto de extremidade do aplicativo certo.

## <a name="appscope"></a>Definir e redefinir credenciais de aplicativo
Para definir e redefinir credenciais de aplicativo:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **serviços de aplicativos** >  **&lt;Any_app >**  > **central de implantação** > **FTP/credenciais**.

2. Selecione **credenciais do aplicativo**e selecione o link **copiar** para copiar o nome de usuário ou a senha.

Para redefinir as credenciais de nível de aplicativo, selecione **Redefinir credenciais** na mesma caixa de diálogo.

## <a name="next-steps"></a>Próximos passos

Saiba como usar essas credenciais para implantar seu aplicativo do [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
