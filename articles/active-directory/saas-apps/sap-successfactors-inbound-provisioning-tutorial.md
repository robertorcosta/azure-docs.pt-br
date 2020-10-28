---
title: 'Tutorial: configurar o provisionamento de entrada do SuccessFactors no AD e no Azure AD | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: d57afbe2ebdde7755eec659f56e402315a60ec7d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676619"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Tutorial: configurar o SAP SuccessFactors para Active Directory provisionamento de usuário 
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar usuários do SuccessFactors Employee central no Active Directory (AD) e no Azure AD, com write-back opcional de endereço de email para SuccessFactors. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors precisarem de uma conta do AD local e, opcionalmente, uma conta do Azure AD. Se os usuários de SuccessFactors precisarem apenas de uma conta do Azure AD (usuários somente em nuvem), consulte o tutorial em [Configurar o SAP SuccessFactors para o provisionamento de usuário do Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) . 


## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário Azure Active Directory](../app-provisioning/user-provisioning.md) se integra ao [SuccessFactors Employee central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida da identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuário SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratando novos funcionários** – quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md), com write-back do endereço de email para SuccessFactors.

* **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado em SuccessFactors (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

* **Encerramentos de funcionários** – quando um funcionário é encerrado no SuccessFactors, sua conta de usuário é automaticamente desabilitada em Active Directory, Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

* **Recontratação de funcionários** -quando um funcionário é recontratado no SuccessFactors, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo de sua preferência) para Active Directory, Azure Active Directory e, opcionalmente, Microsoft 365 e [outros aplicativos SaaS com suporte do Azure ad](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esse SuccessFactors para Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário SuccessFactors

* Organizações que exigem o provisionamento direto de usuários do SuccessFactors para o Active Directory

* Organizações que exigem que os usuários sejam provisionados usando os dados obtidos do [SuccessFactors Employee central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem ingressar, mover e deixar os usuários serem sincronizados com um ou mais Active Directory florestas, domínios e UOs com base apenas nas informações de alteração detectadas no [SuccessFactors Employee central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam Microsoft 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento do usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – de SuccessFactors para local Active Directory:** Nesse fluxo, eventos de trabalho (como novas contratações, transferências, terminações) ocorrem pela primeira vez no Cloud SuccessFactors funcionário central e, em seguida, os dados de evento fluem para Active Directory locais por meio do Azure AD e do agente de provisionamento. Dependendo do evento, pode levar a operações de criar/atualizar/habilitar/desabilitar no AD.
* **Fluxo de write-back de email – do Active Directory local para SuccessFactors:** Depois que a criação da conta for concluída em Active Directory, ela será sincronizada com o Azure AD por meio do Azure AD Connect sincronização e o atributo de email poderá ser gravado no SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertraçãos ou novas contratações/transferências/encerramentos) no SuccessFactors Employee central
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O Serviço de Provisionamento do Azure AD invoca o Agente de Provisionamento do Azure AD Connect local com um conteúdo de solicitação que contém operações de criar/atualizar/habilitar/desabilitar contas do AD.
4. O Agente de Provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados de conta do AD.
5. O mecanismo de sincronização Azure AD Connect executa a sincronização Delta para efetuar pull de atualizações no AD.
6. As atualizações do Active Directory são sincronizadas com o Azure Active Directory.
7. Se o [aplicativo SuccessFactors write-back](sap-successfactors-writeback-tutorial.md) estiver configurado, ele gravará o atributo de email novamente em SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejamento da implantação

A configuração do provisionamento de usuário controlado por RH na nuvem do SuccessFactors para o AD requer um planejamento considerável que abrange diferentes aspectos, como:
* Instalação do agente de provisionamento do Azure AD Connect 
* Número de SuccessFactors para aplicativos de provisionamento de usuário do AD a serem implantados
* ID de correspondência, mapeamento de atributo, transformação e filtros de escopo

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
* Procure *gerenciar funções de permissão* e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
  ![Gerenciar funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na lista função de permissão, clique em **criar novo** .
  > [!div class="mx-imgBorder"]
  > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um **nome de função** e uma **Descrição** para a nova função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.
  > [!div class="mx-imgBorder"]
  > ![Detalhes da função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Em configurações de permissão, clique em **permissão...** , em seguida, role para baixo na lista de permissões e clique em **gerenciar ferramentas de integração** . Marque a caixa **permitir que o administrador acesse a API OData por meio da autenticação básica** .
  > [!div class="mx-imgBorder"]
  > ![Gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Role para baixo na mesma caixa e selecione **API central do funcionário** . Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para o SuccessFactors cenário de write-back. 
  > [!div class="mx-imgBorder"]
  > ![Permissões de leitura de gravação](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Para obter a lista completa de atributos recuperados por este aplicativo de provisionamento, consulte a [referência de atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

* Clique em **Concluído** . Clique em **Salvar Alterações** .

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

* No centro de administração do SuccessFactors, procure *gerenciar grupos de permissões* e, em seguida, selecione **gerenciar grupos de permissões** nos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerenciar grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Na janela gerenciar grupos de permissões, clique em **criar novo** .
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

* No centro de administração do SuccessFactors, procure *gerenciar funções de permissão* e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
* Na **lista função de permissão** , selecione a função que você criou para as permissões de uso da API.
* Em **conceder esta função para...** , clique no botão **Adicionar...** .
* Selecione **grupo de permissões...** no menu suspenso e, em seguida, clique em **selecionar...** para abrir a janela grupos para pesquisar e selecionar o grupo criado acima. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Examine a concessão da função de permissão para o grupo de permissões. 
  > [!div class="mx-imgBorder"]
  > ![Função de permissão e detalhe do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Salvar Alterações** .

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurando o provisionamento de usuário do SuccessFactors para o Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário do SuccessFactors para cada domínio Active Directory dentro do escopo da sua integração.

* [Adicionar o aplicativo do conector de provisionamento e baixar o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar Agentes de Provisionamento locais](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurar a conectividade com o SuccessFactors e o Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurar mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e baixar o Agente de Provisionamento

**Para configurar o SuccessFactors para Active Directory provisionamento:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos** .

4. Selecione **Adicionar um aplicativo** e a categoria **Todos** .

5. Pesquise **SuccessFactors para Active Directory provisionamento de usuário** e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **Provisioning** **modo** de provisionamento para **automático**

8. Clique na faixa de informações exibida para baixar o Agente de Provisionamento. 
   > [!div class="mx-imgBorder"]
   > ![Baixar Agente](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Tela Baixar Agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar Agentes de Provisionamento local

Para provisionar no Active Directory local, o agente de Provisionamento deverá ser instalado em um servidor que tenha o .NET Framework 4.7.1+ e acesso à rede para o(s) domínio(s) do Active Directory desejado(s).

> [!TIP]
> Você pode verificar a versão do .NET Framework em seu servidor usando as instruções fornecidas [aqui](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se o servidor não tiver o .NET 4.7.1 ou superior instalado, você poderá baixá-lo [aqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador do agente baixado para o host do servidor e siga as etapas exibidas abaixo para concluir a configuração do agente.

1. Conecte-se ao Windows Server em que você deseja instalar o novo agente.

1. Inicie o Instalador do Agente de Provisionamento, aceite os termos e clique no botão **Instalar** .

   ![Tela de Instalação](./media/workday-inbound-tutorial/pa_install_screen_1.png "Tela de instalação")
   
1. Após a instalação ser concluída, o assistente será iniciado e você verá a tela **Conectar o Azure AD** . Clique no botão **Autenticar** para se conectar à sua instância do Azure AD.

   ![Conectar o Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Conectar Azure AD")
   
1. Autentique-se na instância do Azure AD usando Credenciais de Administrador Global.

   ![Autenticação de Administrador](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticação de Administrador")

   > [!NOTE]
   > As credenciais de administrador do AD do Azure são usadas apenas para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação bem-sucedida no Azure AD, você verá a tela **Conectar o Active Directory** . Nesta etapa, insira seu nome de domínio do AD e clique no botão **Adicionar Diretório** .

   ![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, será solicitado que você insira as credenciais necessárias para se conectar ao domínio do AD. Na mesma tela, você pode usar **Selecionar prioridade do controlador de domínio** para especificar os controladores de domínio que o agente deve usar para enviar solicitações de provisionamento.

   ![Credenciais do domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibe uma lista de domínios configurados. Nessa tela, você pode repetir as etapas 5 e 6 para adicionar mais domínios ou clicar em **Avançar** para prosseguir para o registro do agente.

   ![Domínios Configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Não é suficiente apenas adicionar o domínio pai (por exemplo, contoso.com). Você deve registrar cada domínio filho com o agente.
   
1. Examine os detalhes de configuração e clique em **Confirmar** para registrar o agente.
  
   ![Confirmar Tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registro do Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro de Agente")
   
1. Depois que o registro do agente for bem-sucedido, você poderá clicar em **Sair** para sair do Assistente.
  
   ![Sair da Tela](./media/workday-inbound-tutorial/pa_install_screen_9.png "Sair da tela")
   
1. Verifique a instalação do Agente e verifique se ele está em execução abrindo o Snap-In de “Serviços” e procurando pelo Serviço chamado “Agente de Provisionamento do Microsoft Azure AD Connect”
  
   ![Captura de tela do agente de provisionamento do Microsoft Azure AD Connect em execução em serviços.](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: no aplicativo de provisionamento, configure a conectividade com o SuccessFactors e o Active Directory
Nesta etapa, estabelecemos a conectividade com SuccessFactors e Active Directory no portal do Azure. 

1. Na portal do Azure, volte para o SuccessFactors para Active Directory aplicativo de provisionamento de usuário criado na [parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Conclua a seção **Credenciais de Administrador** , conforme a seguir:

   * **Nome** de usuário do administrador – insira o nome de usu of the SuccessFactors API User Account, com a ID da empresa anexada. Ele tem o formato: **username \@ CompanyID**

   * **Senha de administrador –** Insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do locatário –** Insira o nome do ponto de extremidade dos serviços de API OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou HTTPS. Esse valor deve ser semelhante a: **<API-Server-name>. successfactors.com** .

   * **Floresta do Active Directory –** o “Nome” de seu domínio do Active Directory, conforme registrado com o agente. Use a lista suspensa para selecionar o domínio de destino para o provisionamento. Geralmente, esse valor é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner do Active Directory –** insira o DN do contêiner em que o agente deve criar contas de usuário por padrão.
        Exemplo: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Essa configuração só entra em vigor para criações de contas de usuário quando o atributo *parentDistinguishedName* não está configurado nos mapeamentos de atributo. Essa configuração não é usada para operações de atualização e pesquisa de usuários. A subárvore de todo o domínio se enquadra no escopo da operação de pesquisa.

   * **Email de Notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Clique no botão **Conexão de Teste** . Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se ele falhar, verifique se as credenciais do SuccessFactors e as credenciais do AD configuradas na instalação do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão **sincronizar usuários do SuccessFactors no local Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. Na guia provisionamento, em **mapeamentos** , clique em **sincronizar usuários do SuccessFactors para o local Active Directory** .

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários em SuccessFactors devem estar no escopo para provisionamento no AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

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
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários fora do escopo. Isso pode não ser desejável em sua integração do SuccessFactors com o AD. Para substituir esse comportamento padrão, consulte o artigo [Ignorar a exclusão de contas de usuário fora do escopo](../app-provisioning/skip-out-of-scope-deletions.md)
  
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
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou problemas de dados de SuccessFactors, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento** , defina o **Status de Provisionamento** para **Em** .

2. Clique em **Save** (Salvar).

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos de SuccessFactors e, posteriormente, adicionados ou atualizados para Active Directory. 

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