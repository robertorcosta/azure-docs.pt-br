---
title: 'Tutorial: Configure o provisionamento de entrada de fatores de entrada no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar provisionamento de entrada a partir de SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249679"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Tutorial: Configure SAP SuccessFactors para provisionamento de usuários do Active Directory (Preview)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisão de usuários da SuccessFactors Employee Central para Active Directory (AD) e Azure AD, com o endereçamento opcional de e-mail para SuccessFactors. Essa integração está na pré-visualização pública e suporta recuperar mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de usuários da SuccessFactors Employee Central.

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar da SuccessFactors precisarem de uma conta AD no local e, opcionalmente, de uma conta AD do Azure. Se os usuários do SuccessFactors precisarem apenas da conta Azure AD (usuários somente na nuvem), consulte o tutorial sobre configurar SAP SuccessFactors para o provisionamento de usuários [Do Azure AD.](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 


## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuários do Azure Active Directory](../app-provisioning/user-provisioning.md) integra-se à [Central de Funcionários SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida da identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuários SuccessFactors suportados pelo serviço de provisionamento de usuários Azure AD permitem a automação dos seguintes cenários de gerenciamento de recursos humanos e de gerenciamento do ciclo de vida da identidade:

* **Contratação de novos funcionários** - Quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente no Active Directory, No Azure Active Directory e, opcionalmente, no Office 365 e [em outros aplicativos SaaS suportados pelo Azure AD,](../app-provisioning/user-provisioning.md)com o write-back do endereço de e-mail para SuccessFactors.

* **Atualizações de atributos e perfis** de funcionários - Quando um registro de funcionário é atualizado em SuccessFactors (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente no Active Directory, Azure Active Directory e, opcionalmente, no Office 365 e [outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

* **Demissões de funcionários** - Quando um funcionário é demitido no SuccessFactors, sua conta de usuário é automaticamente desativada no Active Directory, No Azure Active Directory e, opcionalmente, no Office 365 e [em outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratações de funcionários** - Quando um funcionário é recontratado em SuccessFactors, sua conta antiga pode ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) para o Active Directory, Azure Active Directory e, opcionalmente, office 365 e [outros aplicativos SaaS suportados pelo Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Esta solução de provisionamento de usuários do SuccessFactors to Active Directory é ideal para:

* Organizações que desejam uma solução pré-construída baseada em nuvem para o provisionamento de usuários SuccessFactors

* Organizações que exigem provisionamento direto do usuário de SuccessFactors para Active Directory

* Organizações que exigem que os usuários sejam provisionados usando dados obtidos da [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem a adesão, movimentação e deixar os usuários serem sincronizados com uma ou mais Florestas de Diretório Ativo, Domínios e OUs com base apenas nas informações de alteração detectadas na [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção descreve a arquitetura da solução de provisionamento do usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – de SuccessFactors a Active Directory on-premises:** Neste fluxo, eventos de trabalhadores (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na central de funcionários da empresa de sucesso na nuvem e, em seguida, os dados do evento fluem para o Active Directory on-premises através do Azure AD e do Agente de Provisionamento. Dependendo do evento, pode levar a operações de criar/atualizar/habilitar/desabilitar no AD.
* **Fluxo de regravação de e-mails – do Diretório Ativo no local para os fatores de sucesso:** Uma vez que a criação da conta esteja concluída no Active Directory, ela é sincronizada com o Azure AD através da sincronização azure AD Connect e o atributo de e-mail pode ser gravado de volta para SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH realiza transações de trabalhadores (Marceneiros/Movers/Leavers ou Novas Contratações/Transferências/Rescisões) na Central de Funcionários Da SuccessFactors
2. O Azure AD Provisioning Service executa sincronizações programadas de identidades da SuccessFactors EC e identifica alterações que precisam ser processadas para sincronização com o Active Directory no local.
3. O Azure AD Provisioning Service invoca o agente de provisionamento Azure AD Connect no local com uma carga útil de solicitação contendo operações de criação/atualização/ativação/desativação da conta AD.
4. O Agente de Provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados de conta do AD.
5. O motor Azure AD Connect Sync executa o delta sync para puxar atualizações em AD.
6. As atualizações do Active Directory são sincronizadas com o Azure Active Directory.
7. Se o [aplicativo SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurado, ele gravará o atributo de e-mail para SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejamento da implantação

Configurar o provisionamento de usuários orientados por RH na nuvem de SuccessFactors para AD requer um planejamento considerável cobrindo diferentes aspectos, como:
* Configuração do agente de provisionamento Azure AD Connect 
* Número de fatores de sucesso para aplicativos de provisionamento de usuários aD para implantar
* Correspondente de ID, Mapeamento de Atributos, Transformação e Filtros de escopo

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

  >[!NOTE]
  >Para obter a lista completa de atributos recuperados por este aplicativo de provisionamento, consulte [SuccessFactors Attribute Reference](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurando o provisionamento do usuário de SuccessFactors para Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário de SuccessFactors para cada domínio do Active Directory no âmbito de sua integração.

* [Adicione o aplicativo de conector de provisionamento e baixe o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar Agentes de Provisionamento locais](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure a conectividade para SuccessFactors e Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurar mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicione o aplicativo de conector de provisionamento e baixe o Agente de Provisionamento

**Para configurar o dispositivo SuccessFactors para provisionamento de diretórioativo ativo:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5. Procure **por SuccessFactors para provisionamento**de usuários do diretório ativo e adicione esse aplicativo da galeria.

6. Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7. Altere o **Modo de** **Provisionamento** para **Automático**

8. Clique no banner de informações exibido para baixar o Agente de Provisionamento. 
   > [!div class="mx-imgBorder"]
   > ![Agente de download](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Baixar tela do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar agentes de provisionamento no local

Para prover o Active Directory no local, o agente de provisionamento deve ser instalado em um servidor que tenha o Framework .NET 4.7.1+ e o acesso à rede ao domínio(s) desejado do Active Directory.

> [!TIP]
> Você pode verificar a versão do .NET Framework em seu servidor usando as instruções fornecidas [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se o servidor não tiver o .NET 4.7.1 ou superior instalado, você poderá baixá-lo [aqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador de agente baixado para o host do servidor e siga as etapas abaixo para concluir a configuração do agente.

1. Faça login no Servidor Windows onde deseja instalar o novo agente.

1. Inicie o instalador do provisionamento, concorde com os termos e clique no botão **Instalar.**

   ![Instalar tela](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalar tela")
   
1. Após a instalação ser concluída, o assistente será iniciado e você verá a tela **Conectar o Azure AD**. Clique no botão **Autenticar** para se conectar à sua instância do Azure AD.

   ![Conectar o Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Conectar Azure AD")
   
1. Autentique-se na instância do Azure AD usando Credenciais de Administrador Global.

   ![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > As credenciais de administrador do AD do Azure são usadas apenas para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação bem-sucedida no Azure AD, você verá a tela **Conectar o Active Directory**. Nesta etapa, insira seu nome de domínio do AD e clique no botão **Adicionar Diretório**.

   ![Adicionar diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, será solicitado que você insira as credenciais necessárias para se conectar ao domínio do AD. Na mesma tela, você pode usar **Selecionar prioridade do controlador de domínio** para especificar os controladores de domínio que o agente deve usar para enviar solicitações de provisionamento.

   ![Credenciais do domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibe uma lista de domínios configurados. Nessa tela, você pode repetir as etapas 5 e 6 para adicionar mais domínios ou clicar em **Avançar** para prosseguir para o registro do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Não é suficiente apenas adicionar o domínio pai (por exemplo, contoso.com). Você deve registrar cada domínio filho com o agente.
   
1. Examine os detalhes de configuração e clique em **Confirmar** para registrar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registro de Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro de Agente")
   
1. Depois que o registro do agente for bem-sucedido, você poderá clicar em **Sair** para sair do Assistente.
  
   ![Tela de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Tela de saída")
   
1. Verifique a instalação do Agente e certifique-se de que ele esteja em execução abrindo o Snap-In de “Serviços” e procurando pelo Serviço chamado “Agente de Provisionamento do Microsoft Azure AD Connect”
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: No aplicativo de provisionamento, configure conectividade para SuccessFactors e Active Directory
Nesta etapa, estabelecemos conectividade com SuccessFactors e Active Directory no portal Azure. 

1. No portal Azure, volte ao aplicativo de provisionamento de usuários do Diretório Ativo criado na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de usuário do Administrador** – Digite o nome de usuário da conta de usuário da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de admin -** Digite a senha da conta de usuário da API SuccessFactors. 

   * **URL do inquilino –** Digite o nome do ponto final dos serviços de API da SuccessFactors OData. Digite apenas o nome de host do servidor sem http ou https. Esse valor deve parecer: **<api-server-name>.successfactors.com**.

   * **Floresta do Active Directory –** o “Nome” de seu domínio do Active Directory, conforme registrado com o agente. Use a lista suspensa para selecionar o domínio de destino para o provisionamento. Geralmente, esse valor é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner do Active Directory –** insira o DN do contêiner em que o agente deve criar contas de usuário por padrão.
        Exemplo: *OU=Usuários,DC=contoso,DC=com*
        > [!NOTE]
        > Essa configuração só entra em vigor para criações de contas de usuário quando o atributo *parentDistinguishedName* não está configurado nos mapeamentos de atributo. Essa configuração não é usada para operações de atualização e pesquisa de usuários. A subárvore de todo o domínio se enquadra no escopo da operação de pesquisa.

   * **Email de notificação –** digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.
    > [!NOTE]
    > O Serviço de Provisionamento do Azure AD envia uma notificação por email quando o trabalho de provisionamento entra no estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique duas vezes se as credenciais SuccessFactors e as credenciais de Anúncio configuradas na configuração do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Uma vez que as credenciais sejam salvas com sucesso, a seção **Mapeamentos** exibirá o mapeamento padrão **Sincronizar usuários de fatores de sucesso para o diretório ativo em instalações**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configure mapeamentos de atributos

Nesta seção, você configurará como os dados do usuário fluem de SuccessFactors para Active Directory.

1. Na guia Provisionamento em **Mapeamentos,** clique **em Sincronizar usuários de fatores de sucesso para o diretório ativo em instalações**.

1. No campo **'Escopo do objeto de** origem', você pode selecionar quais conjuntos de usuários em SuccessFactors devem estar no escopo de provisionamento para AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

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
   > O comportamento padrão do mecanismo de provisionamento é desativar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração SuccessFactors to AD. Para substituir esse comportamento padrão, consulte o artigo [Pular a exclusão de contas de usuário que saem do escopo](../app-provisioning/skip-out-of-scope-deletions.md)
  
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
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros nos problemas de dados de mapeamento ou SuccessFactors, o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Salvar**.

3. Esta operação iniciará a sincronização inicial, que pode levar um número variável de horas, dependendo de quantos usuários estão no inquilino SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os registros de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, como quais usuários estão sendo lidos fora de SuccessFactors e, posteriormente, adicionados ou atualizados ao Active Directory. 

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
