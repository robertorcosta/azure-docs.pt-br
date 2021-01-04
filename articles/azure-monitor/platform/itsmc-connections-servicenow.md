---
title: Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI
description: Este artigo fornece informações sobre como o ServiceNow com o Conector de Gerenciamento de Serviços de TI (ITSMC) em Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729590"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI

Este artigo fornece informações sobre como configurar a conexão entre sua instância do ServiceNow e o Conector de Gerenciamento de Serviços de TI (ITSMC) em Log Analytics para gerenciar centralmente seus itens de trabalho.

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow ao ITSMC no Azure.

## <a name="prerequisites"></a>Pré-requisitos
Verifique se os seguintes pré-requisitos foram atendidos:
- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
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
>     1. [Concluir um processo de sincronização manual para configuração do conector de ITSM](./itsmc-resync-servicenow.md)
>     2. Revogue o token de atualização antigo porque não é recomendado manter chaves antigas por motivos de segurança. Na folha do ServiceNow, procure “System OAuth” e depois selecione “Gerenciar tokens”. Escolha o token antigo na lista de acordo com o nome OAuth e a data de validade.
> ![Definição de OAuth do sistema SNOW](media/itsmc-connections/snow-system-oauth.png)
>     3. Clique em “Revogar acesso” e em “Revogar”.

- Instale o aplicativo de usuário para integração com o Microsoft Log Analytics (aplicativo ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> O ITSMC dá suporte apenas ao aplicativo de usuário oficial para a integração do Microsoft Log Analytics que é baixada do ServiceNow Store. ITSMC não dão suporte a qualquer ingestão de código no lado do ServiceNow ou ao aplicativo que não faz parte da solução oficial do ServiceNow. 
- Crie função de usuário de integração para o aplicativo de usuário instalado. Veja informações sobre como criar a função de usuário de integração [aqui](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Procedimento de conexão**
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

> [!NOTE]
> No ServiceNow, há um limite de taxa para solicitações por hora. Para configurar o limite, use isso definindo "limitação da taxa da API REST de entrada" na instância do ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Criar função de usuário de integração no aplicativo do ServiceNow

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
> O Conector ITSM pode enviar incidentes ServiceNow sem quaisquer outros módulos instalados na instância do ServiceNow. Se você estiver usando o módulo de EventManagement na sua instância do ServiceNow e deseja criar Eventos ou Alertas no ServiceNow usando o conector, adicione as seguintes funções ao usuário de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Próximas etapas

* [Visão geral de Conector ITSM](itsmc-overview.md)
* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)