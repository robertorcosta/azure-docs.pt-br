---
title: 'Tutorial: configurar o provisionamento de entrada do SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do SuccessFactors para o Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 97bda09870949846e333cc2845286512fd5d6386
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809894"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Tutorial: configurar o SAP SuccessFactors para o provisionamento de usuário do Azure AD
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar dados de trabalho do SuccessFactors Employee central no Azure Active Directory, com write-back opcional de endereço de email para SuccessFactors. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors forem usuários somente na nuvem que não precisam de uma conta do AD local. Se os usuários exigirem apenas conta do AD local ou conta do AD e do Azure AD, consulte o tutorial em configurar o [SAP SuccessFactors para Active Directory provisionamento de](sap-successfactors-inbound-provisioning-tutorial.md#overview) usuário. 

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário Azure Active Directory](../app-provisioning/user-provisioning.md) se integra ao [SuccessFactors Employee central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida da identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuário SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratando novos funcionários** – quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente no Azure Active Directory e, opcionalmente, no Office 365 e em [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md), com write-back do endereço de email para SuccessFactors.

* **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado em SuccessFactors (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

* **Encerramentos de funcionários** – quando um funcionário é encerrado no SuccessFactors, sua conta de usuário é automaticamente desabilitada em Azure Active Directory e, opcionalmente, no Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

* **Recontratação de funcionário** -quando um funcionário é recontratado no SuccessFactors, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo da sua preferência) para Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esse SuccessFactors para Azure Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário SuccessFactors

* Organizações que exigem o provisionamento direto de usuários do SuccessFactors para o Azure Active Directory

* Organizações que exigem que os usuários sejam provisionados usando os dados obtidos do [SuccessFactors Employee central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para usuários somente em nuvem. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – de SuccessFactors para Azure Active Directory:** Nesse fluxo, eventos de trabalho (como novas contratações, transferências, terminações) ocorrem pela primeira vez no Cloud SuccessFactors funcionário central e, em seguida, os dados de evento fluem para Azure Active Directory. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no Azure AD.
* **Fluxo de write-back de email – do Active Directory local para SuccessFactors:** Depois que a criação da conta for concluída em Azure Active Directory, o valor do atributo de email ou o UPN gerado no Azure AD poderá ser gravado novamente em SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertraçãos ou novas contratações/transferências/encerramentos) no SuccessFactors Employee central
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD determina a alteração e invoca a operação criar/atualizar/habilitar/desabilitar para o usuário no Azure AD.
4. Se o [aplicativo de write-back SuccessFactors](sap-successfactors-writeback-tutorial.md) estiver configurado, o endereço de email do usuário será recuperado do Azure AD. 
5. O serviço de provisionamento do Azure AD grava o atributo de email de volta em SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejamento da implantação

A configuração do provisionamento de usuário controlado por RH na nuvem do SuccessFactors para o Azure AD requer um planejamento considerável que abrange diferentes aspectos, como:

* Determinando a ID de correspondência 
* Mapeamento de atributos
* Transformação de atributo 
* Filtros de escopo

Consulte o plano de [implantação de RH de nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter diretrizes abrangentes sobre esses tópicos. Consulte a referência de [integração do SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) para saber mais sobre as entidades com suporte, os detalhes de processamento e como personalizar a integração para cenários de RH diferentes. 

## <a name="configuring-successfactors-for-the-integration"></a>Configurando o SuccessFactors para a integração

Um requisito comum de todos os conectores de provisionamento do SuccessFactors é que eles exigem credenciais de uma conta SuccessFactors com as permissões corretas para invocar as APIs OData do SuccessFactors. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar conta de usuário de API no SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar uma função de permissões de API](#create-an-api-permissions-role)
* [Criar um grupo de permissões para o usuário da API](#create-a-permission-group-for-the-api-user)
* [Conceder a função de permissão ao grupo de permissões](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de usuário de API no SuccessFactors
Trabalhe com sua equipe de administração do SuccessFactors ou com o parceiro de implementação para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

* Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao centro de administração.
* Procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
  ![Gerenciar funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na lista função de permissão, clique em **criar novo**.
  > [!div class="mx-imgBorder"]
  > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um **nome de função** e uma **Descrição** para a nova função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.
  > [!div class="mx-imgBorder"]
  > ![Detalhes da função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Em configurações de permissão, clique em **permissão...**, em seguida, role para baixo na lista de permissões e clique em **gerenciar ferramentas de integração**. Marque a caixa **permitir que o administrador acesse a API OData por meio da autenticação básica**.
  > [!div class="mx-imgBorder"]
  > ![Gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Role para baixo na mesma caixa e selecione **API central do funcionário**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para o SuccessFactors cenário de write-back. 
  > [!div class="mx-imgBorder"]
  > ![Permissões de leitura de gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **Concluído**. Clique em **Salvar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

* No centro de administração do SuccessFactors, procure *gerenciar grupos de permissões*e, em seguida, selecione **gerenciar grupos de permissões** nos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerenciar grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Na janela gerenciar grupos de permissões, clique em **criar novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicione o novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.
  > [!div class="mx-imgBorder"]
  > ![Nome do grupo de permissões](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, você pode selecionar **nome de usuário** no menu suspenso pool de pessoas e, em seguida, inserir o nome de usuário da conta de API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **concluído** para concluir a criação do grupo de permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a função de permissão ao grupo de permissões

* No centro de administração do SuccessFactors, procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
* Na **lista função de permissão**, selecione a função que você criou para as permissões de uso da API.
* Em **conceder esta função para...**, clique no botão **Adicionar...** .
* Selecione **grupo de permissões...** no menu suspenso e, em seguida, clique em **selecionar...** para abrir a janela grupos para pesquisar e selecionar o grupo criado acima. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Examine a concessão da função de permissão para o grupo de permissões. 
  > [!div class="mx-imgBorder"]
  > ![Função de permissão e detalhe do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Salvar Alterações**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurando o provisionamento de usuário do SuccessFactors para o Azure AD

Esta seção fornece etapas para o provisionamento de contas de usuário do SuccessFactors para o Azure AD.

* [Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o SuccessFactors para o provisionamento do Azure AD:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Pesquise **SuccessFactors para Azure Active Directory provisionamento de usuário**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **Provisioning** **modo** de provisionamento para **automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome** de usuário do administrador – insira o nome de usu of the SuccessFactors API User Account, com a ID da empresa anexada. Ele tem o formato: **username \@ CompanyID**

   * **Senha de administrador –** Insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do locatário –** Insira o nome do ponto de extremidade dos serviços de API OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou HTTPS. Esse valor deve ser semelhante a: **API-Server-Name.successfactors.com**.

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão **sincronizar usuários do SuccessFactors com o Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. Na guia provisionamento em **mapeamentos**, clique em **sincronizar SuccessFactors usuários para Azure Active Directory**.

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários em SuccessFactors devem estar no escopo para provisionamento para o Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

   * Exemplo: escopo para usuários com personIdExternal entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: personIdExternal

      * Operador: Coincidir EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas funcionários e trabalhadores não contingentes

      * Atributo: EmployeeID

      * Operador: IS NOT NULL

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda usar os filtros de escopo no **escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do SuccessFactors. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários fora do escopo. Isso pode não ser desejável em sua integração do SuccessFactors com o Azure AD. Para substituir esse comportamento padrão, consulte o artigo [Ignorar a exclusão de contas de usuário fora do escopo](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Ações do objeto de destino** é possível filtrar globalmente quais ações são executadas no Active Directory. **Criar** e **Atualizar** são as mais comuns.

1. Na seção **mapeamentos de atributo** , você pode definir como os atributos de SuccessFactors individuais são mapeados para atributos de Active Directory.

  >[!NOTE]
  >Para obter a lista completa do atributo SuccessFactors com suporte pelo aplicativo, consulte a [referência de atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

      * **Tipo de Mapeamento**

         * **Direto** – grava o valor do atributo SuccessFactors para o atributo do AD, sem alterações

         * **Constante** - grava um valor de cadeia de caracteres constante estático para o atributo do AD

         * **Expression** – permite que você grave um valor personalizado para o atributo do AD, com base em um ou mais atributos SuccessFactors. [Para obter mais informações, consulte este artigo sobre expressões](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atributo de origem** -o atributo de usuário de SuccessFactors

      * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** –  o atributo de usuário no Active Directory.

      * **Corresponder objetos usando este atributo** – se este mapeamento deve ou não ser usado para identificar exclusivamente os usuários entre SuccessFactors e Active Directory. Esse valor é normalmente definido no campo ID de trabalho para SuccessFactors, que normalmente é mapeado para um dos atributos de ID de funcionário em Active Directory.

      * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum outro atributo correspondente será avaliado.

      * **Aplicar esse mapeamento**

         * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

         * **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário

1. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

Uma vez concluída a configuração do mapeamento de atributo, você pode agora [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Save** (Salvar).

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização realizados pelo serviço de provisionamento, tais como aqueles em que os usuários estão sendo lidos do Workday e, posteriormente adicionados ou atualizados no Active Directory. 

5. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os atributos SuccessFactors com suporte para o provisionamento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar o Write-back de email para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


