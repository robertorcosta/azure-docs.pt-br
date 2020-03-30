---
title: 'Tutorial: Configure SuccessFactors writeback no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar a gravação de atributo susta da Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060041"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: Configure a regravação de atributos do Azure AD para o SAP SuccessFactors (Preview)
O objetivo deste tutorial é mostrar os passos que você precisa executar para escrever atributos do Azure AD para a SuccessFactors Employee Central. O único atributo atualmente suportado para writeback é o atributo de e-mail. 

## <a name="overview"></a>Visão geral

Depois de configurar a integração de provisionamento de entrada usando o SuccessFactors para o aplicativo de provisionamento AD no local ou o aplicativo de provisionamento SuccessFactors para o aplicativo de provisionamento [Azure AD,](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) você pode configurar opcionalmente o aplicativo SuccessFactors Writeback para escrever o endereço [de e-mail](sap-successfactors-inbound-provisioning-tutorial.md) de volta para SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esta solução de provisionamento do usuário SuccessFactors Writeback é ideal para:

* Organizações que usam o Office 365 que desejam regravar atributos autoritários gerenciados pela TI (como endereço de e-mail) de volta ao SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurando Fatores de Sucesso para a integração

Um requisito comum de todos os conectores de provisionamento SuccessFactors é que eles requerem credenciais de uma conta SuccessFactors com as permissões certas para invocar as APIs de Dados de Sucesso. Esta seção descreve etapas para criar a conta de serviço em SuccessFactors e conceder permissões apropriadas. 

* [Criar/identificar a conta de usuário da API no SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Crie uma função de permissões de API](#create-an-api-permissions-role)
* [Crie um grupo de permissão para o usuário da API](#create-a-permission-group-for-the-api-user)
* [Função de permissão de concessão para o grupo de permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar a conta de usuário da API no SuccessFactors
Trabalhe com sua equipe de administração de SucessoFactors ou parceiro de implementação para criar ou identificar uma conta de usuário em SuccessFactors que será usada para invocar as APIs de OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Crie uma função de permissões de API

* Faça login no SAP SuccessFactors com uma conta de usuário que tenha acesso ao Centro de Administração.
* Pesquisar para *gerenciar funções de permissão*e, em seguida, selecionar **Gerenciar funções** de permissão a partir dos resultados de pesquisa.
  ![Gerenciar funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na lista de papéis de permissão, clique **em Criar novo**.
  > [!div class="mx-imgBorder"]
  > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um **nome de função** e uma **descrição** para a nova função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso de API.
  > [!div class="mx-imgBorder"]
  > ![Detalhe da função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Em Configurações de permissão, clique **em Permissão...** e role a lista de permissões e clique **em Gerenciar ferramentas de integração**. Verifique a caixa para **permitir que o Admin acesse a API de OData através da Autenticação Básica**.
  > [!div class="mx-imgBorder"]
  > ![Gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Role para baixo na mesma caixa e selecione **API central do funcionário**. Adicione permissões conforme mostrado abaixo para ler usando a API ODATA e editar usando a API ODATA. Selecione a opção de edição se você planeja usar a mesma conta para o cenário Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Leia permissões de gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **Concluído**. Clique em **Salvar alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Crie um grupo de permissão para o usuário da API

* No Centro de Gerenciamento de Fatores de Sucesso, procure *gerenciar grupos de permissão*e selecione Gerenciar grupos de **permissão** nos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerenciar grupos de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Na janela Gerenciar grupos de permissão, clique **em Criar novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicione o novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.
  > [!div class="mx-imgBorder"]
  > ![Nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, você pode selecionar Nome de **usuário** no menu suspenso do Pool de pessoas e, em seguida, digitar o nome de usuário da conta aPI que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique **em Concluído** para terminar de criar o Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Função de permissão de concessão para o grupo de permissão

* No SuccessFactors Admin Center, procure *gerenciar funções de permissão*e selecione **Gerenciar funções** de permissão nos resultados da pesquisa.
* Na **lista de papéis de permissão,** selecione a função criada para permissões de uso da API.
* Em **Grant este papel para...**, clique em **Adicionar...** botão.
* Selecione Grupo de **permissão...** no menu suspenso e clique em **Selecionar...** para abrir a janela Grupos para pesquisar e selecionar o grupo criado acima. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Revise a concessão da função de permissão ao Grupo de Permissões. 
  > [!div class="mx-imgBorder"]
  > ![Detalhes da função de permissão e do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Salvar alterações**.

## <a name="configuring-successfactors-writeback"></a>Configurando successfactors writeback

Esta seção fornece etapas para 

* [Adicione o aplicativo conector de provisionamento e configure a conectividade ao SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione o aplicativo de conector de provisionamento e configure a conectividade ao SuccessFactors

**Para configurar o SuccessFactors Writeback:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Procure **por SuccessFactors Writeback**e adicione esse aplicativo na galeria.

6. Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7. Altere o **Modo de** **Provisionamento** para **Automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de usuário do Administrador** – Digite o nome de usuário da conta de usuário da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de admin -** Digite a senha da conta de usuário da API SuccessFactors. 

   * **URL do inquilino –** Digite o nome do ponto final dos serviços de API da SuccessFactors OData. Digite apenas o nome de host do servidor sem http ou https. Este valor deve se parecer com: **api-server-name.successfactors.com**.

   * **Email de notificação –** digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique duas vezes se as credenciais e URL successfactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Uma vez que as credenciais sejam salvas com sucesso, a seção **Mapeamentos** exibirá o mapeamento padrão **Sincronizar usuários do Diretório Ativo do Azure para SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem de SuccessFactors para Active Directory.

1. Na guia Provisionamento em **Mapeamentos,** clique **em Sincronizar usuários do diretório ativo do Azure para SuccessFactors**.

1. No campo **'Escopo do objeto de** origem', você pode selecionar quais conjuntos de usuários no Azure AD devem ser considerados para Writeback, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários no Azure AD". 
   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda o uso dos filtros de escopo no **Origin Object Scope** para testar seus mapeamentos com alguns usuários de teste do Azure AD. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. O **campo Ações de Objeto alvo** só suporta a operação **Atualizar.**

1. Na seção **Mapeamentos de atributos,** você só pode alterar o ID correspondente que é usado para vincular um perfil de usuário successFactors com o usuário Azure AD e qual atributo no Azure AD serve como fonte de e-mail. 
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >O retorno de gravação do SuccessFactors só suporta o atributo de e-mail. Por favor, não use **Adicionar novo mapeamento** para adicionar novos atributos. 

1. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de atributos.

Uma vez concluída a configuração do mapeamento de atributo, você pode agora [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Uma vez concluídas as configurações do aplicativo de provisionamento SuccessFactors, você pode ativar o serviço de provisionamento no portal Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Salvar**.

3. Esta operação iniciará a sincronização inicial, que pode levar um número variável de horas, dependendo de quantos usuários estão no inquilino SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização realizados pelo serviço de provisionamento, tais como aqueles em que os usuários estão sendo lidos do Workday e, posteriormente adicionados ou atualizados no Active Directory. 

5. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso do provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o login único entre SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

