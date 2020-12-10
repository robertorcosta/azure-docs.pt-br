---
title: Conector de Gerenciamento de Serviços de TI no Azure Monitor
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviço de TI (ITSMC) no Azure Monitor para monitorar e gerenciar os itens de trabalho ITSM de forma centralizada.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a0ef8d9f28f90db77686c3139c8b45eccfb669aa
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938810"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Conectar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviços de TI
Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM e o ITSMC (Conector de Gerenciamento de Serviços de TI) no Log Analytics para gerenciar seus itens de trabalho de forma centralizada. Para obter mais informações sobre o ITSMC, consulte [Visão geral](./itsmc-overview.md).

Há suporte para os seguintes produtos/serviços ITSM. Selecione o produto para exibir informações detalhadas sobre como conectar o produto ao ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Sugerimos que nossos clientes Cherwell e Provance usem a [ação de webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Conectar o System Center Service Manager ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto System Center Service Manager ao ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- O aplicativo Web do Service Manager (aplicativo da Web) é implantado e configurado. Veja informações sobre o aplicativo Web [aqui](#create-and-deploy-service-manager-web-app-service).
- Conexão híbrida criada e configurada. Mais informações: [Configurar a conexão híbrida](#configure-the-hybrid-connection).
- Versões do Service Manager com suporte:  2012 R2 ou 2016.
- Função de usuário:  [Operador avançado](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Hoje, os alertas enviados do Azure Monitor podem ser criados em incidentes System Center Service Manager.

> [!NOTE]
> 
> - O Conector ITSM pode se conectar apenas a instâncias do ServiceNow baseadas em nuvem. No momento, não há suporte para instâncias locais do ServiceNow.
> - Para usar [modelos](./itsmc-overview.md#template-definitions) personalizados como parte das ações, o parâmetro "geometertype" no modelo SCSM deve ser mapeado para "IncidentManagement! System. WorkItem. Incident. Projetype "

### <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para conectar a instância do System Center Service Manager ao ITSMC:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.

    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do System Center Service Manager que você deseja conectar ao ITSMC.  Use esse nome posteriormente quando você configurar os itens de trabalho nesta instância / exibir a análise de logs detalhado. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **Servidor URL**   | Digite a URL do aplicativo Web do Service Manager. Veja mais sobre o aplicativo Web do Service Manager [aqui](#create-and-deploy-service-manager-web-app-service).
| **ID do Cliente**   | Digite a ID de cliente gerado (usando o script automática) para autenticar o aplicativo Web. Veja mais informações sobre o script automatizado [aqui.](./itsmc-service-manager-script.md)|
| **Segredo do Cliente**   | Digite o segredo do cliente gerado para essa ID.   |
| **Sincronizar Dados**   | Selecione os itens de trabalho do Service Manager que você deseja sincronizar por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, Solicitações de Alteração.|
| **Escopo de Sincronização de Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o Log Analytics cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

![Conexão do Service Manager](media/itsmc-connections/service-manager-connection.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados no Service Manager são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Service Manager.


Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implantar o serviço de aplicativo Web do Service Manager

Para conectar o Service Manager local ao ITSMC no Azure, a Microsoft criou um aplicativo Web do Service Manager no GitHub.

Para configurar o aplicativo Web ITSM para o Service Manager, faça o seguinte:

- **Implantar o aplicativo Web** – implantar o aplicativo da Web, defina as propriedades e autenticar com o AD do Azure. Você pode implantar o aplicativo web usando o [automatizada script](./itsmc-service-manager-script.md) que a Microsoft forneceu a você.
- **Configurar a conexão híbrida** - [configurar essa conexão](#configure-the-hybrid-connection), manualmente.

#### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web
Use o automated [script](./itsmc-service-manager-script.md) para implantar o aplicativo Web, defina as propriedades e autenticar com o AD do Azure.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da assinatura do Azure
- Nome do grupo de recursos
- Location
- Detalhes do servidor do Service Manager (nome do servidor, domínio, nome de usuário e senha)
- Prefixo de nome do site para seu aplicativo Web
- Namespace do ServiceBus.

O script cria um aplicativo Web usando o nome especificado (junto com algumas cadeias de caracteres adicionais para torná-la exclusiva). Ele gera o **URL do aplicativo Web**, a **ID do cliente** e o **segredo do cliente**.

Salve os valores, você os usará quando criar uma conexão com o ITSMC.

**Verificar a instalação do aplicativo Web**

1. Vá para o **portal do Azure** > **Recursos**.
2. Selecione o aplicativo Web, clique em **Configurações** > **Configurações do Aplicativo**.
3. Confirme as informações sobre a instância do Gerenciador de serviço que você forneceu no momento da implantação do aplicativo por meio do script.

### <a name="configure-the-hybrid-connection"></a>Configurar a conexão híbrida

Use o procedimento a seguir para configurar a conexão híbrida que conecta a instância do Service Manager ao ITSMC no Azure.

1. Localize o aplicativo Web do Service Manager em **Recursos do Azure**.
2. Clique em **Configurações** > **Rede**.
3. Em **conexões híbridas**, clique em **configurar seus pontos de extremidade de conexão híbrida**.

    ![Rede de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na folha **Conexões Híbridas**, clique em **Adicionar conexão híbrida**.

    ![Adição de conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. No **adicionar conexões híbridas** folha, clique em **criar novo híbrida Conexão**.

    ![Nova Conexão híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome do Ponto de Extremidade**: Especifique um nome para a nova conexão híbrida.
   - **Host do Ponto de Extremidade**: FQDN do servidor de gerenciamento do Service Manager.
   - **Porta do Ponto de Extremidade**: Digite 5724
   - **Namespace de Servicebus**: Use um namespace de servicebus existente ou crie um novo.
   - **Local**: selecione o local.
   - **Name**: Especifique um nome para o servicebus se você estiver criando ele.

     ![Valores da conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique em **Okey** para fechar o **criar conexão híbrida** folha e começar a criar a conexão híbrida.

    Depois que a conexão híbrida é criada, ela será exibida em folha.

7. Depois que a conexão híbrida é criada, selecione a conexão e clique em **adicionar selecionada conexão híbrida**.

    ![Nova conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurar a instalação do ouvinte

Use o procedimento a seguir para definir a configuração de ouvinte para a conexão híbrida.

1. No **conexões híbridas** folha, clique em **o Download do Gerenciador de Conexão** e instalá-lo no computador em que a instância do System Center Service Manager está em execução.

    Quando a instalação for concluída, **Gerenciador de Conexão híbrida UI** opção está disponível em **iniciar** menu.

2. Clique em **Gerenciador de Conexão híbrida UI** , você será solicitado para suas credenciais do Azure.

3. Faça logon com suas credenciais do Azure e selecione sua assinatura em que a conexão híbrida foi criada.

4. Clique em **Save** (Salvar).

Sua conexão híbrida foi conectada com êxito.

![conexão híbrida com êxito](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a criação da conexão híbrida, verifique e teste a conexão ao visitar o aplicativo Web do Service Manager. Verifique se a conexão foi bem-sucedida antes de tentar se conectar ao ITSMC no Azure.

O exemplo a seguir mostra os detalhes de uma conexão bem-sucedida:

![Teste de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow ao ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos
Verifique se os seguintes pré-requisitos foram atendidos:
- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- Versões com suporte do ServiceNow: Orlando, Nova York, Madri, Londres, Kingston, Jacarta, Istambul, Helsinque, Geneva.
- Hoje, os alertas enviados do Azure Monitor podem criar no ServiceNow um dos seguintes elementos: eventos, incidentes ou alertas.
> [!NOTE]
> O ITSMC dá suporte apenas à oferta de SaaS oficial do Service Now. Não há suporte para implantações particulares do Service Now. 

**Os administradores do ServiceNow devem fazer o seguinte em sua instância do ServiceNow**:
- Gere a ID do cliente e o segredo do cliente para o produto ServiceNow. Para saber mais sobre como gerar o ID e o segredo do cliente, consulte as informações conforme o necessário:

    - [Configurar o OAuth para Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Nova Iorque](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Madri](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Genebra](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Helsinque](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Como parte da definição de “Configurar o OAuth”, recomendamos:
>
> 1) **Atualize o tempo de vida do token de atualização para 90 dias (7.776.000 segundos):** Como parte de [Configurar o OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) na fase 2: [Crie um ponto de extremidade para os clientes acessarem a instância](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0). Após a definição do ponto de extremidade, na folha do ServiceNow, procure “System OAuth” e depois selecione “Registro de Aplicativo”. Escolha o nome do OAuth que foi definido e atualize o campo de tempo de vida do token de atualização para 7.776.000 (90 dias em segundos).
> No final, clique em “Atualizar”.
> 2) **Recomendamos estabelecer um procedimento interno para garantir que a conexão permaneça ativa:** De acordo com o tempo de vida do token de atualização para atualizar o token. Execute as seguintes operações antes do tempo de expiração esperado do token de atualização (alguns dias antes da expiração do tempo de vida do token de atualização, recomendamos):
>
> 1. [Concluir um processo de sincronização manual para configuração do conector de ITSM](./itsmc-resync-servicenow.md)
> 2. Revogue o token de atualização antigo porque não é recomendado manter chaves antigas por motivos de segurança. Na folha do ServiceNow, procure “System OAuth” e depois selecione “Gerenciar tokens”. Escolha o token antigo na lista de acordo com o nome OAuth e a data de validade.
> ![Definição de OAuth do sistema SNOW](media/itsmc-connections/snow-system-oauth.png)
> 3. Clique em “Revogar acesso” e em “Revogar”.

- Instale o aplicativo de usuário para integração com o Microsoft Log Analytics (aplicativo ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> O ITSMC dá suporte apenas ao aplicativo de usuário oficial para a integração do Microsoft Log Analytics que é baixada do ServiceNow Store. ITSMC não dão suporte a qualquer ingestão de código no lado do ServiceNow ou ao aplicativo que não faz parte da solução oficial do ServiceNow. 
- Crie função de usuário de integração para o aplicativo de usuário instalado. Veja informações sobre como criar a função de usuário de integração [aqui](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedimento de conexão**
Use o procedimento a seguir para criar uma conexão do ServiceNow:


1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.
    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.


> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do ServiceNow que você deseja conectar com o ITSMC.  Use esse nome posteriormente no Log Analytics quando você configurar os itens de trabalho nesse ITSM/exibir o Log Analytics detalhado. |
| **Tipo de parceiro**   | Selecione **ServiceNow**. |
| **Nome de usuário**   | Digite o nome de usuário de integração que você criou no aplicativo ServiceNow para dar suporte à conexão com o ITSMC. Mais informações: [Criar função de usuário de aplicativo do ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação**: O nome de usuário e a senha são usados apenas para gerar tokens de autenticação, não sendo armazenados em nenhum lugar dentro do serviço ITSMC.  |
| **Servidor URL**   | Digite a URL da instância do ServiceNow que você deseja conectar ao ITSMC. A URL deve apontar para uma versão de SaaS com suporte com o sufixo ".servicenow.com".|
| **ID do Cliente**   | Digite a ID do cliente que você deseja usar para autenticação de OAuth2, que você gerou anteriormente.  Para saber mais sobre como gerar a ID do cliente e o segredo:   [Configuração OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do Cliente**   | Digite o segredo do cliente gerado para essa ID.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do ServiceNow que você deseja sincronizar com o Azure Log Analytics por meio do ITSMC.  Os valores selecionados são importados para a análise de logs.   **Opções:**  Incidentes e Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM compatível. **Padrão**: desabilitado. |

![Conexão do ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados na instância do ServiceNow são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do ServiceNow.

Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> No ServiceNow, há um limite de taxa para solicitações por hora. Para configurar o limite, use isso definindo "limitação da taxa da API REST de entrada" na instância do ServiceNow.

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar função de usuário de integração no aplicativo do ServiceNow

Use este procedimento:

1. Visite o [ServiceNow repositório](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale o **aplicativo de usuário para ServiceNow e integração de OMS do Microsoft** em sua instância do ServiceNow.
   
   >[!NOTE]
   >Como parte da transição do Microsoft Operations Management Suite (OMS) em andamento para o Azure Monitor, o OMS agora é conhecido como Log Analytics.     
2. Após a instalação, visite a barra de navegação à esquerda da instância do ServiceNow, pesquisa e selecione Microsoft OMS integrador.  
3. Clique em **Lista de Verificação de Instalação**.

   O status é exibido como **concluir não** se a função de usuário ainda não foi criado.

4. Nas caixas de texto, ao lado de **Criar usuário de integração**, insira o nome de usuário que pode se conectar ao ITSMC no Azure.
5. Insira a senha para esse usuário e clique em **OK**.  

> [!NOTE]
> 
> Use essas credenciais para fazer a conexão do ServiceNow no Azure.

O usuário recém-criado é exibido com as funções padrão atribuídas.

**Funções padrão**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Depois que o usuário é criado com êxito, o status de **lista de verificação de instalação verificar** Move como concluída, listar os detalhes da função de usuário criado para o aplicativo.

> [!NOTE]
> 
> O Conector ITSM pode enviar incidentes ServiceNow sem quaisquer outros módulos instalados na instância do ServiceNow. Se você estiver usando o módulo de EventManagement na sua instância do ServiceNow e deseja criar Eventos ou Alertas no ServiceNow usando o conector, adicione as seguintes funções ao usuário de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Conectar o Provance ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto Provance ao ITSMC no Azure.

> [!NOTE]
> 
> Sugerimos que nossos clientes Provance usem a [ação de webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:


- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- Provance aplicativo deve ser registrado com o Azure AD - ID do cliente é disponibilizada. Para obter informações detalhadas, consulte [como configurar a autenticação do active directory](../../app-service/configure-authentication-provider-aad.md).

- Função de usuário:  Administrador.

### <a name="connection-procedure"></a>Procedimento de Conexão

Use o procedimento a seguir para criar uma conexão do Provance:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.
    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do Provance que você deseja conectar com o ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/análise de logs detalhado da exibição. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de usuário**   | Digite o nome de usuário que pode se conectar ao ITSMC.    |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** O nome de usuário e a senha são usados apenas para gerar tokens de autenticação, não sendo armazenados em nenhum lugar dentro do serviço ITSMC._|
| **Servidor URL**   | Digite a URL da sua instância do Provance que você deseja conectar ao ITSMC. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância de Provance.  Para saber mais sobre a ID do cliente, consulte [como configurar a autenticação do active directory](../../app-service/configure-authentication-provider-aad.md). |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Provance que você deseja sincronizar com o Azure Log Analytics por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:**   Incidentes, Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM compatível. **Padrão**: desabilitado.|

![Captura de tela que realça as listas nome da conexão e tipo de parceiro.](media/itsmc-connections/itsm-connections-provance-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados nessa instância do Provance são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Provance.

Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Conectar o Cherwell ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto Cherwell ao ITSMC no Azure.

> [!NOTE]
> 
> Sugerimos que nossos clientes Cherwell usem a [ação de webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- ID do Cliente gerada. Mais informações: [Gerar a ID do Cliente do Cherwell](#generate-client-id-for-cherwell).
- Função de usuário:  Administrador.

### <a name="connection-procedure"></a>Procedimento de Conexão

Use o procedimento a seguir para criar uma conexão do Cherwell:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.
    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do Cherwell que você deseja conectar ao ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/análise de logs detalhado da exibição. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de usuário**   | Digite o nome de usuário do Cherwell que pode se conectar ao ITSMC. |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** O nome de usuário e a senha são usados apenas para gerar tokens de autenticação, não sendo armazenados em nenhum lugar dentro do serviço ITSMC.|
| **Servidor URL**   | Digite a URL da sua instância do Cherwell que você deseja conectar ao ITSMC. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância do Cherwell.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Cherwell que você deseja sincronizar por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:**  Incidentes, Solicitações de Alteração. |
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM compatível. **Padrão**: desabilitado. |


![Conexão do Provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados nessa instância do Cherwell são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Cherwell.

Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar a ID de Cliente do Cherwell

Para gerar o chave/ID do cliente para Cherwell, use o procedimento a seguir:

1. Fazer logon em sua instância de Cherwell como administrador.
2. Clique em **Segurança** > **Editar configurações de cliente de API REST**.
3. Selecione **Criar novo cliente** > **segredo do cliente**.

    ![Id de usuário do Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Próximas etapas
 - [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
