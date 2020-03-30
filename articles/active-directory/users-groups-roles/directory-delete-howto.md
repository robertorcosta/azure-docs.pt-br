---
title: Excluir um diretório Azure AD - Azure Active Directory | Microsoft Docs
description: Explica como preparar um diretório Azure AD para exclusão, incluindo diretórios de autoatendimento
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961826"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Exclua um diretório no Diretório Ativo do Azure

Quando um diretório Azure AD é excluído, todos os recursos contidos no diretório também são excluídos. Prepare sua organização minimizando seus recursos associados antes de excluir. Apenas um administrador global do Azure Active Directory (Azure AD) pode excluir um diretório Azure AD do portal.

## <a name="prepare-the-directory"></a>Prepare o diretório

Você não pode excluir um diretório no Azure AD até que ele passe por várias verificações. Essas verificações reduzem o risco de que a exclusão de um diretório Azure AD impacte negativamente o acesso do usuário, como a capacidade de fazer login no Office 365 ou acessar recursos no Azure. Por exemplo, se o diretório associado a uma assinatura for excluído involuntariamente, os usuários não poderão acessar os recursos do Azure para essa assinatura. As seguintes condições são verificadas:

* Não pode haver usuários no diretório, exceto um administrador global que deve excluir o diretório. Quaisquer outros usuários devem ser excluídos antes que o diretório possa ser excluído. Se os usuários estiverem sincronizados a partir do local, a sincronização deve ser desatada primeiro e os usuários devem ser excluídos no diretório da nuvem usando o portal Azure ou os cmdlets do Azure PowerShell.
* Não pode haver nenhum aplicativo no diretório. Quaisquer aplicações devem ser removidas antes que o diretório possa ser excluído.
* Não pode haver provedores de autenticação multifatorial vinculados ao diretório.
* Não pode haver assinaturas para Microsoft Online Services, como o Microsoft Azure, Office 365 ou Azure AD Premium associadas ao diretório. Por exemplo, se um diretório padrão tiver sido criado para você no Azure, você não poderá excluir esse diretório se sua assinatura do Azure ainda depender desse diretório para autenticação. Da mesma forma, você não pode excluir um diretório se outro usuário tiver associado uma assinatura a ele.

## <a name="delete-the-directory"></a>Exclua o diretório

1. Faça login no centro de administração do [Azure AD](https://aad.portal.azure.com) com uma conta que é o Administrador Global da sua organização.

2. Selecione **O Diretório Ativo do Azure**.

3. Mude para o diretório que deseja excluir.
  
   ![Confirme a organização antes de excluir](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **Excluir diretório**.
  
   ![selecionar o comando para excluir a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o seu diretório não passar em uma ou mais verificações, você será fornecido com um link para mais informações sobre como passar. Depois de passar em todas as verificações, selecione **Excluir** para concluir o processo.

## <a name="if-you-cant-delete-the-directory"></a>Se você não pode excluir o diretório

Quando você configurou seu diretório Ad do Azure, você também pode ter ativado assinaturas baseadas em licenças para sua organização, como O Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Para evitar a perda acidental de dados, você não pode excluir um diretório até que as assinaturas sejam totalmente excluídas. As assinaturas devem estar em um estado **desprovisionado** para permitir a exclusão do diretório. Uma assinatura **expirada** ou **cancelada** se move para o estado **desativado,** e a etapa final é o estado **desativado.**

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os admins têm acesso normal ao centro de admin microsoft 365 e recursos 
Expirado (30 dias) | Dados acessíveis a todos| Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os admins têm acesso normal ao centro de admin microsoft 365 e recursos
Desabilitado (30 dias) | Dados acessíveis somente ao administrador | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Os admins podem acessar o centro de admin do Microsoft 365, mas não podem atribuir licenças ou atualizar usuários
Desprovisionado (30 dias depois de ser desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Os admins podem acessar o centro de admin microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="delete-a-subscription"></a>Excluir uma assinatura

Você pode colocar uma assinatura no estado **Deprovisionado** a ser excluído em três dias usando o centro de admin microsoft 365.

1. Faça login no [centro de administração microsoft 365](https://admin.microsoft.com) com uma conta que é um administrador global em sua organização. Se você estiver tentando excluir o diretório "Contoso" que tem o domínio padrão admin@contoso.onmicrosoft.cominicial contoso.onmicrosoft.com, faça login com uma UPN como .

2. Visualize o novo centro de admin da Microsoft 365, certificando-se de que o alternador do novo centro de **admin** esteja habilitado.

   ![Visualize a nova experiência do centro de admin M365](./media/directory-delete-howto/preview-toggle.png)

3. Uma vez que o novo centro de admin esteja ativado, você precisa cancelar uma assinatura antes de excluí-la. Selecione **Faturamento** e selecione **Produtos & serviços**e selecione **Cancelar assinatura** para a assinatura que deseja cancelar. Você será levado para uma página de comentários.

   ![Escolha uma assinatura para cancelar](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Preencha o formulário de feedback e **selecione Cancelar assinatura** para cancelar a assinatura.

   ![Cancelar comando na pré-visualização de assinatura](./media/directory-delete-howto/cancel-command.png)

5. Agora você pode excluir a assinatura. Selecione **Excluir** para a assinatura que deseja excluir. Se você não conseguir encontrar a assinatura na página **Produtos & serviços,** certifique-se de ter o **status de assinatura** definido como **"Tudo**".

   ![Link de exclusão para excluir assinatura](./media/directory-delete-howto/delete-command.png)

6. Selecione **Excluir assinatura** para excluir a assinatura e aceitar os termos e condições. Todos os dados são excluídos permanentemente em até três dias. Você pode [reativar a assinatura](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante o período de três dias se você mudar de idéia.
  
   ![leia cuidadosamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

7. Agora, o estado de assinatura mudou, e a assinatura está marcada para exclusão. A assinatura entra o **Desprovisionado** estado 72 horas mais tarde.

8. Depois de excluir uma assinatura em seu diretório e 72 horas tiverem passado, você pode assinar novamente no centro de administradores do Azure AD e não deve haver nenhuma ação necessária e nenhuma assinatura bloqueando a exclusão do diretório. Você deve ser capaz de excluir com sucesso seu diretório Azure AD.
  
   ![passar a verificação de assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Eu tenho uma assinatura de teste que bloqueia a exclusão

Existem [produtos de autoatendimento](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, usuários individuais podem se inscrever via Office 365, que também cria um usuário convidado para autenticação em seu diretório Azure AD. Esses produtos de autoatendimento bloqueiam as exclusões do diretório até que sejam totalmente excluídos do diretório, para evitar a perda de dados. Eles só podem ser excluídos pelo administrador do Azure AD se o usuário se inscreveu individualmente ou foi atribuído o produto.

Existem dois tipos de produtos de autoatendimento na forma como eles são atribuídos: 

* Atribuição em nível de org: um administrador do Azure AD atribui o produto a toda a organização e um usuário pode estar usando ativamente o serviço com essa atribuição de nível org, mesmo que eles não sejam licenciados individualmente.
* Atribuição de nível de usuário: Um usuário individual durante a inscrição de autoatendimento essencialmente atribui o produto a si mesmo sem um admin. Uma vez que a organização se torne gerenciada por um administrador (consulte [a aquisição do administrador de um diretório não gerenciado,](domains-admin-takeover.md)então o administrador pode atribuir diretamente o produto aos usuários sem o autoatendimento.  

Quando você inicia a exclusão do produto de inscrição de autoatendimento, a ação exclui permanentemente os dados e remove todo o acesso do usuário ao serviço. Qualquer usuário que tenha sido atribuído a oferta individualmente ou no nível da organização é então impedido de fazer login ou acessar quaisquer dados existentes. Se você quiser evitar a perda de dados com o produto de inscrição de autoatendimento, como [os painéis do Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou a [configuração da diretiva de serviços de gerenciamento de direitos,](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)certifique-se de que os dados sejam copiados e salvos em outros lugares.

Para obter mais informações sobre produtos e serviços de autoatendimento disponíveis atualmente, consulte [programas de autoatendimento disponíveis](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal a produtos, arquivos ou aplicativos de autoatendimento<br>Os admins têm acesso normal ao centro de admin microsoft 365 e recursos
Deleted | Dados excluídos | Os usuários não podem acessar produtos, arquivos ou aplicativos de autoatendimento<br>Os admins podem acessar o centro de admin microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso excluir um produto de inscrição de autoatendimento no portal Azure?

Você pode colocar um produto de inscrição de autoatendimento como o Microsoft Power BI ou o Azure Rights Management Services em um estado **de Exclusão** para ser imediatamente excluído no portal Azure AD.

1. Faça login no centro de administração [azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é um administrador Global na organização. Se você estiver tentando excluir o diretório "Contoso" que tem o domínio padrão admin@contoso.onmicrosoft.cominicial contoso.onmicrosoft.com, faça o login com uma UPN como .

2. Selecione **Licenças**e selecione **produtos de inscrição de autoatendimento**. Você pode ver todos os produtos de inscrição de autoatendimento separadamente das assinaturas baseadas em assentos. Escolha o produto que deseja excluir permanentemente. Aqui está um exemplo no Microsoft Power BI:

    ![o nome de usuário é digitado errado ou não encontrado](./media/directory-delete-howto/licenses-page.png)

3. Selecione **Excluir** para excluir o produto e aceitar os termos que os dados são excluídos imediatamente e irrevogavelmente. Esta ação de exclusão removerá todos os usuários e removerá o acesso da organização ao produto. Clique em Sim para avançar com a exclusão.  

    ![o nome de usuário é digitado errado ou não encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando você selecionar **Sim,** a exclusão do produto de autoatendimento será iniciada. Há uma notificação que lhe dirá sobre a exclusão em andamento.  

    ![o nome de usuário é digitado errado ou não encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado do produto de inscrição de autoatendimento mudou para **Excluído**. Ao atualizar a página, o produto deve ser removido da página **de produtos de inscrição self-service.**  

    ![o nome de usuário é digitado errado ou não encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de excluir todos os produtos, você pode entrar novamente no centro de administradores do Azure AD e não deve haver nenhuma ação necessária e nenhum produto que bloqueie a exclusão do diretório. Você deve ser capaz de excluir com sucesso seu diretório Azure AD.

    ![o nome de usuário é digitado errado ou não encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Próximas etapas

[Documentação do Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/)
