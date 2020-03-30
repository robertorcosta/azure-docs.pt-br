---
title: 'Tutorial: Configure o provisionamento de entrada de fatores de entrada no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada de SuccessFactors para Azure AD
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
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063215"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Tutorial: Configure SAP SuccessFactors para o provisionamento do usuário Azure AD (Preview)
O objetivo deste tutorial é mostrar os passos que você precisa executar para provisionar dados do trabalhador da SuccessFactors Employee Central para o Azure Active Directory, com o backup opcional do endereço de e-mail para SuccessFactors. Essa integração está na pré-visualização pública e suporta recuperar mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de usuários da SuccessFactors Employee Central. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors são usuários somente na nuvem que não precisam de uma conta AD no local. Se os usuários exigirem apenas uma conta AD no local ou a conta AD e a Azure AD, consulte o tutorial sobre [configurar SAP SuccessFactors para](sap-successfactors-inbound-provisioning-tutorial.md#overview) provisionamento de usuários do Active Directory. 

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuários do Azure Active Directory](../app-provisioning/user-provisioning.md) integra-se à [Central de Funcionários SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida da identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuários SuccessFactors suportados pelo serviço de provisionamento de usuários Azure AD permitem a automação dos seguintes cenários de gerenciamento de recursos humanos e de gerenciamento do ciclo de vida da identidade:

* **Contratação de novos funcionários** - Quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente no Azure Active Directory e, opcionalmente, no Office 365 e [em outros aplicativos SaaS suportados pelo Azure AD,](../app-provisioning/user-provisioning.md)com o write-back do endereço de e-mail para SuccessFactors.

* **Atualizações de atributos e perfis** de funcionários - Quando um registro de funcionário é atualizado em SuccessFactors (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente o Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

* **Demissões de funcionários** - Quando um funcionário é demitido no SuccessFactors, sua conta de usuário é automaticamente desativada no Azure Active Directory e, opcionalmente, no Office 365 e [em outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratações de funcionários** - Quando um funcionário é recontratado em SuccessFactors, sua conta antiga pode ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) para o Azure Active Directory e, opcionalmente, para o Office 365 e [outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esta solução de provisionamento de usuários do Azure Active Directory é ideal para:

* Organizações que desejam uma solução pré-construída baseada em nuvem para o provisionamento de usuários SuccessFactors

* Organizações que exigem provisionamento direto do usuário de SuccessFactors para o Azure Active Directory

* Organizações que exigem que os usuários sejam provisionados usando dados obtidos da [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura de solução de provisionamento de usuário de ponta a ponta para usuários somente em nuvem. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – de SuccessFactors a Azure Active Directory:** Neste fluxo, eventos de trabalhadores (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na central de funcionários da nuvem SuccessFactors e, em seguida, os dados do evento fluem para o Azure Active Directory. Dependendo do evento, ele pode levar a criar/atualizar/ativar/desativar as operações no Azure AD.
* **Fluxo de regravação de e-mails – do Diretório Ativo no local para os fatores de sucesso:** Uma vez que a criação da conta esteja concluída no Azure Active Directory, o valor do atributo de e-mail ou UPN gerado no Azure AD pode ser gravado de volta para SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH realiza transações de trabalhadores (Marceneiros/Movers/Leavers ou Novas Contratações/Transferências/Rescisões) na Central de Funcionários Da SuccessFactors
2. O Azure AD Provisioning Service executa sincronizações programadas de identidades da SuccessFactors EC e identifica alterações que precisam ser processadas para sincronização com o Active Directory no local.
3. O Azure AD Provisioning Service determina a alteração e invoca a operação criar/atualizar/ativar/desativar para o usuário no Azure AD.
4. Se o [aplicativo SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurado, o endereço de e-mail do usuário será recuperado do Azure AD. 
5. O serviço de provisionamento Azure AD regrava o atributo de e-mail para SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejamento da implantação

Configurar o provisionamento de usuários orientados pelo CLOUD HR de SuccessFactors para a Azure AD requer um planejamento considerável cobrindo diferentes aspectos, como:

* Determinando o ID correspondente 
* Mapeamento de atributos
* Transformação de atributos 
* Filtros de escopo

Consulte o [plano de implantação](../app-provisioning/plan-cloud-hr-provision.md) de RH na nuvem para obter diretrizes abrangentes em torno desses tópicos. 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurando o provisionamento do usuário de SuccessFactors para Azure AD

Esta seção fornece etapas para o provisionamento de contas de usuário de SuccessFactors para Azure AD.

* [Adicione o aplicativo conector de provisionamento e configure a conectividade ao SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione o aplicativo de conector de provisionamento e configure a conectividade ao SuccessFactors

**Para configurar o dispositivo SuccessFactors para o provisionamento do Azure AD:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Procure **por SuccessFactors para o Azure Active Directory User Provisioning**e adicione esse aplicativo da galeria.

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
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Uma vez que as credenciais sejam salvas com sucesso, a seção **Mapeamentos** exibirá o mapeamento padrão **Sincronizar usuários de fatores de sucesso para o Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem de SuccessFactors para Active Directory.

1. Na guia Provisionamento em **Mapeamentos,** clique **em Sincronizar usuários de fatores de sucesso no Azure Active Directory**.

1. No campo **'Escopo do objeto de** origem', você pode selecionar quais conjuntos de usuários em SuccessFactors devem estar no escopo de provisionamento para o Azure AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

   * Exemplo: Escopo para usuários com personIdExterno entre 1000000 e 2000000 (excluindo 2000000)

      * Atributo: personIdExterno

      * Operador: COINCIDIR.EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: somente funcionários e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO NULO

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda o uso dos filtros de escopo no **Origin Object Scope** para testar seus mapeamentos com alguns usuários de teste da SuccessFactors. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desativar/excluir usuários que saem do escopo. Isso pode não ser desejável na integração do Azure AD. Para substituir esse comportamento padrão, consulte o artigo [Pular a exclusão de contas de usuário que saem do escopo](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Ações do objeto de destino** é possível filtrar globalmente quais ações são executadas no Active Directory. **Criar** e **Atualizar** são as mais comuns.

1. Na seção Mapeamentos de **atributos,** você pode definir como o Mapa de Fatores de Sucesso individual atribui o mapa aos atributos do Active Directory.

  >[!NOTE]
  >Para obter a lista completa do atributo SuccessFactors suportado pelo aplicativo, consulte [A referência de atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

      * **Tipo de Mapeamento**

         * **Direct** – Grava o valor do atributo SuccessFactors ao atributo AD, sem alterações

         * **Constante** - grava um valor de cadeia de caracteres constante estático para o atributo do AD

         * **Expressão** – Permite que você escreva um valor personalizado para o atributo AD, com base em um ou mais atributos SuccessFactors. [Para obter mais informações, consulte este artigo sobre expressões](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atributo de origem** - O atributo do usuário de SuccessFactors

      * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** –  o atributo de usuário no Active Directory.

      * **Combine objetos usando este atributo** – Se esse mapeamento deve ou não ser usado para identificar exclusivamente os usuários entre SuccessFactors e Active Directory. Esse valor é normalmente definido no campo ID do Trabalhador para SuccessFactors, que normalmente é mapeado para um dos atributos de ID do funcionário no Active Directory.

      * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum outro atributo correspondente será avaliado.

      * **Aplicar esse mapeamento**

         * **Sempre** – Aplique este mapeamento em ações de criação e atualização de usuários

         * **Somente durante a criação** - Aplique este mapeamento somente em ações de criação de usuários

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

* [Saiba mais sobre os atributos SuccessFactors suportados para provisionamento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a gravação de e-mail de volta para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o login único entre SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


