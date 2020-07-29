---
title: 'Tutorial: configurar o Write-back do SAP SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Write-back de atributo para o SAP SuccessFactors do Azure AD
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
ms.date: 07/23/2020
ms.author: chmutali
ms.openlocfilehash: 5181cc0f6457c5eb98cca828ecb91c52f47f3fbe
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273468"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: configurar o Write-back de atributo do Azure AD para o SAP SuccessFactors (versão prévia)
O objetivo deste tutorial é mostrar as etapas para os atributos de write-back do Azure AD para o SAP SuccessFactors Employee central. 

## <a name="overview"></a>Visão geral

Você pode configurar o aplicativo SAP SuccessFactors write-back para gravar atributos específicos de Azure Active Directory para o SAP SuccessFactors Employee central. O aplicativo de provisionamento de write-back SuccessFactors dá suporte à atribuição de valores aos seguintes atributos centrais do funcionário:

* Email comercial
* Nome de Usuário
* Número de telefone comercial (incluindo código de país, código de área, número e extensão)
* Sinalizador principal do número de telefone comercial
* Número de telefone celular (incluindo código de país, código de área, número)
* Sinalizador primário de telefone celular 
* Atributos do usuário custom01-custom15
* atributo loginMethod

> [!NOTE]
> Este aplicativo não tem nenhuma dependência dos aplicativos de integração de provisionamento de usuário de entrada do SuccessFactors. Você pode configurá-lo independentemente do SuccessFactors para o aplicativo de provisionamento [do AD local](sap-successfactors-inbound-provisioning-tutorial.md) ou do SuccessFactors para o aplicativo de provisionamento [do Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) .

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário SuccessFactors write-back é ideal para:

* Organizações que usam o Office 365 que desejam fazer write-back de atributos autoritativos gerenciados por ele (como endereço de email, telefone, nome de usuário) de volta para o SuccessFactors Employee central.

## <a name="configuring-successfactors-for-the-integration"></a>Configurando o SuccessFactors para a integração

Todos os conectores de provisionamento do SuccessFactors exigem credenciais de uma conta SuccessFactors com as permissões corretas para invocar as APIs OData do funcionário central. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar conta de usuário de API no SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar uma função de permissões de API](#create-an-api-permissions-role)
* [Criar um grupo de permissões para o usuário da API](#create-a-permission-group-for-the-api-user)
* [Conceder a função de permissão ao grupo de permissões](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de usuário de API no SuccessFactors
Trabalhe com sua equipe de administração do SuccessFactors ou com o parceiro de implementação para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

1. Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao centro de administração.
1. Procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.

   ![Gerenciar funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Na lista função de permissão, clique em **criar novo**.

   > [!div class="mx-imgBorder"]
   > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Adicione um **nome de função** e uma **Descrição** para a nova função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.

   > [!div class="mx-imgBorder"]
   > ![Detalhes da função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Em configurações de permissão, clique em **permissão...**, em seguida, role para baixo na lista de permissões e clique em **gerenciar ferramentas de integração**. Marque a caixa **permitir que o administrador acesse a API OData por meio da autenticação básica**.

   > [!div class="mx-imgBorder"]
   > ![Gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Role para baixo na mesma caixa e selecione **API central do funcionário**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para Write-back para o cenário SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Permissões de leitura de gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Clique em **Concluído**. Clique em **Salvar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

1. No centro de administração do SuccessFactors, procure *gerenciar grupos de permissões*e, em seguida, selecione **gerenciar grupos de permissões** nos resultados da pesquisa.

   > [!div class="mx-imgBorder"]
   > ![Gerenciar grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Na janela gerenciar grupos de permissões, clique em **criar novo**.

   > [!div class="mx-imgBorder"]
   > ![Adicione o novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.

   > [!div class="mx-imgBorder"]
   > ![Nome do grupo de permissões](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Adicione membros ao grupo. Por exemplo, você pode selecionar **nome de usuário** no menu suspenso pool de pessoas e, em seguida, inserir o nome de usuário da conta de API que será usada para a integração. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Clique em **concluído** para concluir a criação do grupo de permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a função de permissão ao grupo de permissões

1. No centro de administração do SuccessFactors, procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
1. Na **lista função de permissão**, selecione a função que você criou para as permissões de uso da API.
1. Em **conceder esta função para...**, clique no botão **Adicionar...** .
1. Selecione **grupo de permissões...** no menu suspenso e, em seguida, clique em **selecionar...** para abrir a janela grupos para pesquisar e selecionar o grupo criado acima. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Examine a concessão da função de permissão para o grupo de permissões. 
   > [!div class="mx-imgBorder"]
   > ![Função de permissão e detalhe do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Clique em **Salvar Alterações**.

## <a name="preparing-for-successfactors-writeback"></a>Preparando para Write-back de SuccessFactors

O aplicativo de provisionamento de write-back SuccessFactors usa determinados valores de *código* para definir email e números de telefone no centro do funcionário. Esses valores de *código* são definidos como valores constantes na tabela de mapeamento de atributo e são diferentes para cada instância de SuccessFactors. Esta seção usa o [postmaster](https://www.postman.com/downloads/) para buscar os valores de código. Você pode usar a [ondulação](https://curl.haxx.se/), o [Fiddler](https://www.telerik.com/fiddler) ou qualquer outra ferramenta semelhante para enviar solicitações HTTP. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Baixar e configurar o postmaster com seu locatário do SuccessFactors

1. Baixar o [postmaster](https://www.postman.com/downloads/)
1. Crie uma "nova coleção" no aplicativo do postmaster. Chame-o de "SuccessFactors". 

   > [!div class="mx-imgBorder"]
   > ![Nova coleção do postmaster](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. Na guia "autorização", insira as credenciais do usuário da API configurada na seção anterior. Configure o tipo como "autenticação básica". 

   > [!div class="mx-imgBorder"]
   > ![Autorização do postmaster](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Salve a configuração. 

### <a name="retrieve-constant-value-for-emailtype"></a>Recuperar valor constante para emailtype

1. No postmaster, clique nas reticências (...) associadas à coleção SuccessFactors e adicione uma "nova solicitação" chamada "obter tipos de email", conforme mostrado abaixo. 

   > [!div class="mx-imgBorder"]
   > ![Solicitação de email do postmaster](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Abra o painel de solicitação "obter tipo de email". 
1. Na URL GET, adicione a seguinte URL, substituindo `successFactorsAPITenantName` pelo locatário da API para sua instância do SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Obter tipo de email do postmaster](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. A guia "autorização" herdará a autenticação configurada para a coleção. 
1. Clique em "enviar" para invocar a chamada à API. 
1. No corpo da resposta, exiba o conjunto de resultados JSON e procure a ID correspondente ao `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![Resposta do tipo de email do postmaster](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Anote esse valor como a constante a ser usada com *emailtype* na tabela de mapeamento de atributo.

### <a name="retrieve-constant-value-for-phonetype"></a>Recuperar o valor constante para o PhoneType

1. No postmaster, clique nas reticências (...) associadas à coleção SuccessFactors e adicione uma "nova solicitação" chamada "obter tipos de telefone", conforme mostrado abaixo. 

   > [!div class="mx-imgBorder"]
   > ![Solicitação de telefone do postmaster](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Abra o painel de solicitação "obter tipo de telefone". 
1. Na URL GET, adicione a seguinte URL, substituindo `successFactorsAPITenantName` pelo locatário da API para sua instância do SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Obter tipo de telefone do postmaster](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. A guia "autorização" herdará a autenticação configurada para a coleção. 
1. Clique em "enviar" para invocar a chamada à API. 
1. No corpo da resposta, exiba o conjunto de resultados JSON e procure a *ID* correspondente ao `externalCode = B` e `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Postmaster-telefone](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Anote esses valores como as constantes a serem usadas com *businessPhoneType* e o *celulartype* na tabela de mapeamento de atributo.

## <a name="configuring-successfactors-writeback-app"></a>Configurando o aplicativo SuccessFactors write-back

Esta seção fornece as etapas para 

* [Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o Write-back SuccessFactors:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Pesquise por **write-back de SuccessFactors**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **Provisioning** **modo** de provisionamento para **automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome** de usuário do administrador – insira o nome de usu of the SuccessFactors API User Account, com a ID da empresa anexada. Ele tem o formato: **username \@ CompanyID**

   * **Senha de administrador –** Insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do locatário –** Insira o nome do ponto de extremidade dos serviços de API OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou HTTPS. Esse valor deve ser semelhante a: `api4.successfactors.com` .

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão. Atualize a página se os mapeamentos de atributo não estiverem visíveis.  

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. Na guia provisionamento, em **mapeamentos**, clique em **provisionar Azure Active Directory usuários**.

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários no Azure ad devem ser considerados para Write-back, definindo um conjunto de filtros baseados em atributo. O escopo padrão é “todos os usuários no Azure AD”. 
   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento de aplicativo pela primeira vez, você precisará testar e verificar seus mapeamentos de atributo e expressões para certificar-se de que ele está dando a você o resultado desejado. A Microsoft recomenda usar os filtros de escopo no **escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do Azure AD. Após ter verificado que os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. O campo de **ações do objeto de destino** só dá suporte à operação de **atualização** .

1. Na tabela mapeamento na seção **mapeamentos de atributo** , você pode mapear os seguintes atributos de Azure Active Directory para SuccessFactors. A tabela a seguir fornece orientação sobre como mapear os atributos write-back. 

   | \# | Atributo do Azure AD | Atributo SuccessFactors | Comentários |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Por padrão, esse atributo é o identificador correspondente. Em vez de employeeId, você pode usar qualquer outro atributo do Azure AD que possa armazenar o valor igual a personIdExternal em SuccessFactors.    |
   | 2 | mail | email | Origem do atributo de email do mapa. Para fins de teste, você pode mapear userPrincipalName para email. |
   | 3 | 8448 | emailtype | Esse valor constante é o valor de ID de SuccessFactors associado ao email comercial. Atualize esse valor para corresponder ao seu ambiente SuccessFactors. Consulte a seção [recuperar o valor constante para emailtype](#retrieve-constant-value-for-emailtype) para as etapas para definir esse valor. |
   | 4 | true | emailIsPrimary | Use este atributo para definir emails comerciais como primários no SuccessFactors. Se email comercial não for primário, defina esse sinalizador como false. |
   | 5 | userPrincipalName | [custom01 – custom15] | Usando **Adicionar novo mapeamento**, você pode opcionalmente escrever userPrincipalName ou qualquer atributo do Azure ad para um atributo personalizado disponível no objeto de usuário SuccessFactors.  |
   | 6 | samAccountName local | Nome de Usuário | Usando **Adicionar novo mapeamento**, você pode, opcionalmente, mapear sAMAccountName local para o atributo de nome de usuário SuccessFactors. |
   | 7 | SSO | loginMethod | Se o locatário do SuccessFactors for configurado para [SSO parcial](https://apps.support.sap.com/sap/support/knowledge/en/2320766)e, em seguida, usar Adicionar novo mapeamento, você poderá, opcionalmente, definir loginMethod como um valor constante de "SSO" ou "pwd". |
   | 8 | telephoneNumber | businessPhoneNumber | Use esse mapeamento para fluir *telephoneNumber* do Azure ad para o número de telefone comercial/trabalho do SuccessFactors. |
   | 9 | 10605 | businessPhoneType | Esse valor constante é o valor de ID de SuccessFactors associado ao telefone comercial. Atualize esse valor para corresponder ao seu ambiente SuccessFactors. Consulte a seção [recuperar o valor constante de PhoneType](#retrieve-constant-value-for-phonetype) para obter as etapas para definir esse valor. |
   | 10 | true | businessPhoneIsPrimary | Use este atributo para definir o sinalizador primário para o número de telefone comercial. Os valores válidos são true ou false. |
   | 11 | Serviço Móvel | cellPhoneNumber | Use esse mapeamento para fluir *telephoneNumber* do Azure ad para o número de telefone comercial/trabalho do SuccessFactors. |
   | 12 | 10606 | Celular | Esse valor constante é o valor de ID SuccessFactors associado ao telefone celular. Atualize esse valor para corresponder ao seu ambiente SuccessFactors. Consulte a seção [recuperar o valor constante de PhoneType](#retrieve-constant-value-for-phonetype) para obter as etapas para definir esse valor. |
   | 13 | false | cellPhoneIsPrimary | Use este atributo para definir o sinalizador principal para o número de telefone celular. Os valores válidos são true ou false. |
 
1. Valide e revise os mapeamentos de atributo. 
 
    >[!div class="mx-imgBorder"]
    >![Mapeamento de atributo de write-back](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Clique em **salvar** para salvar os mapeamentos. Em seguida, atualizaremos as expressões de API de caminho JSON para usar os códigos de PhoneType em sua instância do SuccessFactors. 
1. Selecione **Mostrar opções avançadas**. 

    >[!div class="mx-imgBorder"]
    >![Mostrar opções avançadas](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Clique em **Editar lista de atributos para SuccessFactors**. 

   > [!NOTE] 
   > Se a opção **Editar lista de atributos para SuccessFactors** não aparecer no portal do Azure, use a URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para acessar a página. 

1. A coluna **expressão de API** nessa exibição exibe as expressões de caminho JSON usadas pelo conector. 
1. Atualize as expressões de caminho JSON para telefone comercial e telefone celular para usar o valor de ID (*businessPhoneType* e o *teltype*) correspondente ao seu ambiente. 

    >[!div class="mx-imgBorder"]
    >![Alterar caminho do JSON do telefone](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Clique em **salvar** para salvar os mapeamentos.

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Save** (Salvar).

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos da central do funcionário e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Após a sincronização inicial ser concluída, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Cenários com suporte, problemas conhecidos e limitações

Consulte a [seção cenários de write-back](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) do guia de referência de integração do SAP SuccessFactors. 

## <a name="next-steps"></a>Próximas etapas

* [Aprofunde-se no Azure AD e referência de integração do SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

