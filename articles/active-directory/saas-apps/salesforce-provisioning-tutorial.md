---
title: 'Tutorial: configurar o Salesforce para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060514"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: configurar o Salesforce para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Locatário de Salesforce.com

> [!IMPORTANT]
> Se você estiver usando uma conta Salesforce.com de avaliação, não será possível configurar o provisionamento automatizado de usuários. Contas de avaliação não têm o acesso à API necessária habilitado até que sejam compradas. Você pode contornar essa limitação usando uma [conta de desenvolvedor gratuita](https://developer.salesforce.com/signup) para concluir este tutorial.

Se você estiver usando um ambiente de área restrita do Salesforce, consulte o [Tutorial de integração de área restrita do Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Como atribuir usuários ao Salesforce

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários ou grupos no Azure AD precisam de acesso ao aplicativo Salesforce. Depois de decidir, você pode atribuir esses usuários ao aplicativo Salesforce seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir usuários ao Salesforce

* Recomendamos a atribuição de um único usuário do Azure AD ao Salesforce para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Salesforce, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento

    > [!NOTE]
    > Este aplicativo importa perfis do Salesforce como parte do processo de provisionamento, que o cliente deve desejar selecionar durante a atribuição de usuários no Azure AD. Observe que os perfis que são importados do Salesforce aparecem como Funções no Azure AD.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você através da conexão do Azure AD à [API de provisionamento de conta de usuário da Salesforce - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm), e configurando o serviço de provisionamento para criar, atualizar e desativar contas de usuário atribuídas no Salesforce com base na atribuição de usuário e grupo no Azure AD.

> [!Tip]
> Você também pode optar por habilitar o Logon Único baseado em SAML para o Salesforce, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Salesforce.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Salesforce para logon único, pesquise sua instância do Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Salesforce** na galeria de aplicativos. Selecione o Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Salesforce e selecione a guia **Provisionamento**.

4. Defina o **modo de provisionamento** como **automático**.

    ![provisionamento](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, forneça as seguintes definições de configuração:

    a. Na caixa de texto **Nome de Usuário Administrador**, digite o nome da conta do Salesforce com o perfil **Administrador do Sistema** no Salesforce.com atribuído.

    b. Na caixa de texto **Senha do Administrador**, digite a senha dessa conta.

6. Para obter o token de segurança do Salesforce, abra uma nova guia e entre na mesma conta de administrador do Salesforce. No canto superior direito da página, clique em seu nome e em **Configurações**.

    ![Habilitar o provisionamento automático de usuários.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento automático de usuários.")

7. No painel de navegação esquerdo, clique em **Minhas Informações Pessoais** para expandir a seção correspondente e clique em **Redefinir Meu Token de Segurança**.
  
    ![Habilitar o provisionamento automático de usuários.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento automático de usuários.")

8. Na página **Redefinir Token de Segurança**, clique no botão **Redefinir Token de Segurança**.

    ![Habilitar o provisionamento automático de usuários.](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento automático de usuários.")

9. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce.com que contenha o novo token de segurança.

10. Copie o token, vá até a janela do Azure AD e cole-o no campo **Token Secreto**.

11. A **URL do locatário** deve ser inserida se a instância do Salesforce está na Salesforce Government Cloud. Caso contrário, ela é opcional. Insira a URL do locatário usando o formato "https://\<sua-instância\>.my.salesforce.com", substituindo \<sua-instância\> pelo nome de sua instância do Salesforce.

12. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Salesforce.

13. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

14. Clique **em Salvar.**  

15. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Salesforce.**

16. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Salesforce. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Salesforce para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

17. Para habilitar o serviço de provisionamento do Azure AD para o Salesforce, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

18. Clique **em Salvar.**

> [!NOTE]
> Uma vez que os usuários são provisionados no aplicativo Salesforce, o administrador precisa configurar as configurações específicas do idioma para eles. Consulte [este](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artigo para obter mais detalhes sobre a configuração do idioma.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Salesforce na seção Usuários e Grupos. Observe que a sincronização inicial leva mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Salesforce.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Problemas comuns
* Se você estiver tendo problemas que autorizam o acesso ao Salesforce, certifique-se do seguinte:
    * As credenciais utilizadas têm acesso ao Salesforce.
    * A versão do Salesforce que você está usando suporta o Web Access (por exemplo, edições de Desenvolvedor, Enterprise, Sandbox e Ilimitado do Salesforce.)
    * O acesso à API da Web está habilitado para o usuário.
* O serviço de provisionamento Azure AD suporta provisionamento de idioma, local e fuso horário para um usuário. Esses atributos estão nos mapeamentos de atributos padrão, mas não têm um atributo de origem padrão. Certifique-se de selecionar o atributo de origem padrão e que o atributo de origem está no formato esperado pela SalesForce. Por exemplo, localeSidKey para inglês (Estados Unidos) é en_US. Revise as orientações fornecidas [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) para determinar o formato localeSidKey adequado. Os formatos languageLocaleKey podem ser encontrados [aqui](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Além de garantir que o formato esteja correto, você pode precisar garantir que o idioma esteja habilitado para seus usuários conforme descrito [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExdo:** O usuário não pôde ser criado no aplicativo de destino porque não há licenças disponíveis para este usuário. Obtenha licenças adicionais para o aplicativo de destino ou revise suas atribuições de usuário e configuração de mapeamento de atributos para garantir que os usuários corretos sejam atribuídos aos atributos corretos.
* **SalesforceDuplicateUserName:** O usuário não pode ser provisionado porque tem um Salesforce.com 'Nome de usuário' que é duplicado em outro inquilino Salesforce.com.Em Salesforce.com, os valores para o atributo 'Nome de Usuário' devem ser únicos em todos os Salesforce.com inquilinos.Por padrão, o usuário PrincipalName no Azure Active Directory torna-se seu 'Nome de usuário' em Salesforce.com.Você tem duas opções.Uma opção é encontrar e renomear o usuário com a duplicada 'Nome de usuário' no outro Salesforce.com inquilino, se você administrar esse outro inquilino também.A outra opção é remover o acesso do usuário do Azure Active Directory para o inquilino Salesforce.com com o qual seu diretório está integrado. Vamos tentar novamente esta operação na próxima tentativa de sincronização. 
* **SalesforceRequiredFieldAusente:** Salesforce requer que certos atributos estejam presentes no usuário para criar ou atualizar o usuário com sucesso. Este usuário está perdendo um dos atributos necessários. Certifique-se de que atributos como e-mail e alias sejam preenchidos em todos os usuários que você gostaria de ser provisionados no Salesforce. Você pode escopo de usuários que não têm esses atributos usando [filtros de escopo baseados em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* O mapeamento de atributo padrão para provisionamento no Salesforce inclui a expressão SingleAppRoleAssignments para mapear appRoleAssignments no Azure AD para ProfileName in Salesforce. Certifique-se de que os usuários não tenham várias atribuições de função de aplicativo no Azure AD, pois o mapeamento de atributos suporta apenas o provisionamento de uma função. 
* O Salesforce exige que as atualizações de e-mail sejam aprovadas manualmente antes de serem alteradas. Como resultado, você pode ver várias entradas nos registros de provisionamento para atualizar o e-mail do usuário (até que a alteração de e-mail tenha sido aprovada).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](tutorial-list.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar o único sinal de inscrição](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
