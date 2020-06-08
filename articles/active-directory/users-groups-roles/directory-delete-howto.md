---
title: Excluir uma organização do Azure Active Directory (locatário) - Azure Active Directory | Microsoft Docs
description: Explica como preparar uma organização do Azure Active Directory (locatário) para exclusão, incluindo organizações de autoatendimento
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5ea42f5196b2c4ffe06c139e595dd4641752d35
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816192"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Excluir um novo locatário no Azure Active Directory

Quando uma organização do Azure Active Directory (locatário) é excluída, todos os recursos contidos na organização também são excluídos. Você precisa preparar sua organização minimizando seus recursos associados antes da exclusão. Somente um administrador global do Azure Active Directory (Azure Active Directory) pode excluir uma organização do Azure Active Directory do portal.

## <a name="prepare-the-organization"></a>Preparar a organização

Você poderá apenas excluir uma organização do Azure Active Directory depois que ela passar por várias verificações. Essas verificações reduzem o risco de que a exclusão de uma organização do Azure Active Directory prejudique o acesso do usuário, como a capacidade de entrar no Office 365 ou de acessar recursos no Azure. Por exemplo, se a organização associada a uma assinatura for excluída acidentalmente, os usuários não poderão acessar os recursos do Azure dessa assinatura. As seguintes condições são verificadas:

* Não pode haver nenhum usuário na organização do Azure Active Directory (locatário), exceto um administrador global que excluirá a organização. Todos os outros usuários precisam ser excluídos para que a organização possa ser excluída. Se os usuários estiverem sincronizados localmente, primeiro a sincronização precisará ser desabilitada e os usuários precisarão ser excluídos da organização de nuvem usando o portal do Azure ou os cmdlets do Azure PowerShell.
* Não pode haver nenhum aplicativo na organização. Todos os aplicativos precisam ser removidos para que a organização possa ser excluída.
* Não pode haver nenhum provedor de autenticação multifator vinculado à organização.
* Não pode haver nenhuma assinatura de nenhum Serviço Online da Microsoft como o Microsoft Azure, o Office 365 ou o Azure Active Directory Premium associada à organização. Por exemplo, se uma organização padrão do Azure Active Directory tiver sido criada para você no Azure, não será possível excluir essa organização se a autenticação da sua assinatura do Azure ainda depender dela. Da mesma forma, você não poderá excluir uma organização se outro usuário tiver associado uma assinatura a ele.

## <a name="delete-the-organization"></a>Excluir a organização

1. Entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja o administrador global da organização.

2. Selecione **Azure Active Directory**.

3. Mude para a organização que deseja excluir.
  
   ![Confirme organização antes de excluir](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **Excluir locatário**.
  
   ![Selecione o comando para excluir a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se a organização não passar em uma ou mais verificações, você receberá um link para obter mais informações de como passar. Depois de passar em todas as verificações, selecione **Excluir** para concluir o processo.

## <a name="if-you-cant-delete-the-organization"></a>Se você não puder excluir a organização

Quando você configurou sua organização do Azure Active Directory, talvez você também tenha ativado assinaturas baseadas em licença para a organização, como o Azure Active Directory Premium P2, o Office 365 Business Premium ou Enterprise Mobility + Security E5. Para evitar a perda acidental de dados, não é possível excluir uma organização até que as assinaturas sejam totalmente excluídas. As assinaturas precisam estar em um estado **Desprovisionado** para permitir a exclusão da organização. Uma assinatura **Expirada** ou **Cancelada** passa para o estado **Desabilitado** e a fase final é o estado **Desprovisionado**.

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os administradores têm acesso normal ao centro de administração e aos recursos do Microsoft 365 
Expirado (30 dias) | Dados acessíveis a todos| Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os administradores têm acesso normal ao centro de administração e aos recursos do Microsoft 365
Desabilitado (30 dias) | Dados acessíveis somente ao administrador | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Os administradores podem acessar o centro de administração do Microsoft 365, mas não podem atribuir licenças ou atualizar usuários
Desprovisionado (30 dias depois de ser desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Os administradores podem acessar o centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="delete-a-subscription"></a>Excluir uma assinatura

Você pode colocar uma assinatura no estado **Desprovisionado** a ser excluída após três dias usando o centro de administração do Microsoft 365.

1. Entre no [centro de administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja o administrador global da sua organização. Se você está tentando excluir a organização "Contoso" que tem o domínio inicial padrão contoso.onmicrosoft.com, entre com um UPN como admin@contoso.onmicrosoft.com.

2. Para visualizar a versão prévia do novo centro de administração do Microsoft 365, verifique se **Experimentar o novo centro de administração** está habilitado.

   ![Visualizar a nova experiência do centro de administração do M365](./media/directory-delete-howto/preview-toggle.png)

3. Depois que o novo centro de administração estiver habilitado, você precisará cancelar uma assinatura para poder excluí-la. Selecione **Faturamento** e selecione **Produtos e serviços** e, em seguida, selecione **Cancelar assinatura** para a assinatura que você deseja cancelar. Você será levado a uma página de comentários.

   ![Escolher uma assinatura que será cancelada](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Preencha o formulário de comentários e selecione **Cancelar assinatura** para cancelar a assinatura.

   ![Cancelar comando na versão prévia da assinatura](./media/directory-delete-howto/cancel-command.png)

5. Agora você pode excluir a assinatura. Selecione **Excluir** para a assinatura que você deseja excluir. Se você não conseguir encontrar a assinatura na página **Produtos e serviços**, verifique se **Status da assinatura** está definido como **Todos**.

   ![Link de exclusão para excluir assinatura](./media/directory-delete-howto/delete-command.png)

6. Selecione **Excluir assinatura** para excluir a assinatura e aceitar os termos e condições. Todos os dados são excluídos permanentemente em até três dias. Se você mudar de ideia, será possível [reativar a assinatura](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante esse período de três dias.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

7. Agora o estado da assinatura foi alterado e a assinatura está marcada para exclusão. A assinatura entra o **Desprovisionado** estado 72 horas mais tarde.

8. Depois de excluir uma assinatura da organização e 72 horas tiverem se passado, você poderá entrar novamente no centro de administração do Azure Active Directory, e não deve haver nenhuma ação necessária e nenhuma assinatura bloqueando a exclusão da organização. Você poderá excluir a organização do Azure Active Directory.
  
   ![passar a verificação de assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Tenho uma assinatura de avaliação que bloqueia a exclusão

Há [produtos de inscrição para autoatendimento](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, usuários individuais podem se inscrever por meio do Office 365, que também cria um usuário convidado para autenticação na sua organização do Azure Active Directory. Esses produtos de autoatendimento bloqueiam exclusões de diretório até que os produtos sejam totalmente excluídos da organização, para evitar a perda de dados. Eles podem ser excluídos somente pelo administrador do Azure Active Directory, apesar de o usuário ter se inscrito individualmente ou ter atribuído o produto.

Há dois tipos de produtos de inscrição para autoatendimento, dependendo de como eles são atribuídos: 

* Atribuição de nível de organização: Um administrador do Azure Active Directory atribui o produto a toda a organização e um usuário pode usar ativamente o serviço com essa atribuição de nível de organização, mesmo que ele não seja licenciado individualmente.
* Atribuição de nível de usuário: Um usuário individual durante a inscrição para autoatendimento atribui o produto a si mesmo sem um administrador. Depois que a organização for gerenciada por um administrador (consulte [tomada pelo administrador de uma organização não gerenciada](domains-admin-takeover.md), o administrador poderá atribuir diretamente o produto a usuários sem inscrição para autoatendimento.  

Quando você inicia a exclusão do produto de inscrição para autoatendimento, a ação exclui permanentemente os dados e remove todo o acesso do usuário ao serviço. Qualquer usuário atribuído à oferta individualmente ou no nível da organização será impedido de entrar ou acessar os dados existentes. Se você quiser evitar a perda de dados com o produto de inscrição de autoatendimento, como [dashboards do Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou [configuração de política de serviço do Rights Management](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), verifique se os dados foram copiados em backup e salvos em outro lugar.

Para obter mais informações sobre produtos e serviços de inscrição para autoatendimento disponíveis no momento, consulte [Programas de autoatendimento disponíveis](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal ao produto, aos arquivos ou aos aplicativos de inscrição para autoatendimento<br>Os administradores têm acesso normal ao centro de administração e aos recursos do Microsoft 365
Deleted | Dados excluídos | Os usuários não podem acessar o produto, os arquivos ou os aplicativos de inscrição para autoatendimento<br>Os administradores podem acessar o centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso excluir um produto de inscrição para autoatendimento no portal do Azure?

Você pode colocar um produto de inscrição para autoatendimento, como o Microsoft Power BI ou o Azure Rights Management Services, em um estado **Excluir** para ser excluído imediatamente no portal do Azure Active Directory.

1. Entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um Administrador global na organização. Se você está tentando excluir a organização "Contoso" que tem o domínio inicial padrão contoso.onmicrosoft.com, entre com um UPN como admin@contoso.onmicrosoft.com.

2. Selecione **Licenças** e, em seguida, selecione **Produtos de inscrição para autoatendimento**. Você pode ver todos os produtos de inscrição para autoatendimento separadamente das assinaturas baseadas em estação. Escolha o produto que deseja excluir permanentemente. Veja um exemplo no Microsoft Power BI:

    ![o nome de usuário foi digitado incorretamente ou não foi encontrado](./media/directory-delete-howto/licenses-page.png)

3. Selecione **Excluir** para excluir o produto e aceitar os termos que a exclusão dos dados é imediata e Irrevogável. Esta ação de exclusão removerá todos os usuários e removerá o acesso da organização ao produto. Clique em “Sim” para prosseguir com a exclusão.  

    ![O nome de usuário foi digitado incorretamente ou não foi encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando você seleciona **Sim**, a exclusão do produto de autoatendimento será iniciada. Há uma notificação informando sobre a exclusão em andamento.  

    ![O nome de usuário foi digitado incorretamente ou não foi encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado do produto de inscrição para autoatendimento foi alterado para **Excluído**. Quando você atualiza a página, o produto deve ser removido da página **Produtos de inscrição para autoatendimento**.  

    ![O nome de usuário foi digitado incorretamente ou não foi encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de excluir todos os produtos, você poderá entrar novamente no centro de administração do Azure Active Directory, e não deve haver nenhuma ação necessária e nenhum produto bloqueando a exclusão da organização. Você poderá excluir a organização do Azure Active Directory.

    ![O nome de usuário foi digitado incorretamente ou não foi encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Próximas etapas

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
