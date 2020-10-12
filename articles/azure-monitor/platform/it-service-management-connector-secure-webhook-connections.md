---
title: Conector de Gerenciamento de Serviços de TI-exportação segura no Azure Monitor
description: Este artigo mostra como conectar seus produtos/serviços de ITSM com exportação segura em Azure Monitor para monitorar e gerenciar centralmente os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 9b6180f2480d8a92dc0ebdd2cad474a9eef3cbe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328846"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Conectar o Azure a ferramentas de ITSM usando a exportação segura

Este artigo mostra como configurar a conexão entre seu produto ou serviço de ITSM (gerenciamento de serviço de ti) usando a exportação segura.

A exportação segura é uma versão atualizada do [conector de gerenciamento de serviços de ti (ITSMC)](./itsmc-overview.md). Ambas as versões permitem que você crie itens de trabalho em uma ferramenta de ITSM quando Azure Monitor envia alertas. A funcionalidade inclui os alertas de métrica, log e log de atividades.

ITSMC usa credenciais de nome de usuário e senha. A exportação segura tem autenticação mais forte porque usa Azure Active Directory (Azure AD). O Azure AD é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Ele ajuda os usuários a entrar e acessar recursos internos ou externos. Usar o Azure AD com o ITSM ajuda a identificar alertas do Azure (por meio da ID do aplicativo do Azure AD) que foram enviados para o sistema externo.

> [!NOTE]
> A capacidade de conectar o Azure às ferramentas de ITSM usando a exportação segura está em versão prévia.

## <a name="secure-export-architecture"></a>Arquitetura de exportação segura

A arquitetura de exportação segura apresenta os seguintes novos recursos:

* **Novo grupo de ações**: os alertas são enviados para a ferramenta de ITSM por meio do grupo de ação de webhook seguro, em vez do grupo de ações de ITSM que o ITSMC usa.
* **Autenticação do Azure ad**: a autenticação ocorre por meio do Azure AD em vez de credenciais de nome de usuário/senha.

## <a name="secure-export-data-flow"></a>Fluxo de dados de exportação segura

As etapas do fluxo de dados de exportação segura são:

1. Azure Monitor envia um alerta que está configurado para usar a exportação segura.
1. A carga do alerta é enviada por uma ação de webhook segura para a ferramenta ITSM.
1. O aplicativo ITSM verifica com o Azure AD se o alerta está autorizado a entrar na ferramenta de ITSM.
1. Se o alerta for autorizado, o aplicativo:
   
   1. Cria um item de trabalho (por exemplo, um incidente) na ferramenta de ITSM.
   1. Associa a ID do item de configuração (CI) ao banco de dados de gerenciamento do cliente (CMDB).

![Diagrama que mostra como a ferramenta de ITSM se comunica com o Azure A D, alertas do Azure e um grupo de ações.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Conexão com BMC Helix

A exportação segura dá suporte ao BMC Helix. Alguns benefícios da integração são:

* **Autenticação melhor**: o Azure ad fornece autenticação mais segura sem os tempos limite que normalmente ocorrem no ITSMC.
* **Alertas resolvidos na ferramenta ITSM: os alertas de**métrica implementam os Estados "disparados" e "resolvidos". Quando a condição for atendida, o estado do alerta será "disparado". Quando a condição não for mais atendida, o estado do alerta será "resolvido". No ITSMC, os alertas não podem ser resolvidos automaticamente. Com a exportação segura, o estado resolvido flui para a ferramenta de ITSM e, portanto, é atualizado automaticamente.
* **[Esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)**: em ITSMC, o esquema da carga de alerta difere com base no tipo de alerta. Na exportação segura, há um esquema comum para todos os tipos de alertas. Esse esquema comum contém o CI para todos os tipos de alerta. Todos os tipos de alertas poderão associar seu IC ao CMDB.

Comece a usar a ferramenta de Conector ITSM com estas etapas:

1. Registrar um aplicativo no Azure AD.
2. Crie um grupo de ação de webhook seguro.
3. Configure seu ambiente de parceiro.

## <a name="register-with-azure-active-directory"></a>Registrar com Azure Active Directory

Siga estas etapas para registrar o aplicativo com o Azure AD:

1. Siga as etapas em [registrar um aplicativo com a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).
1. No Azure AD, selecione **expor aplicativo**.
1. Selecione **definir** para o **URI da ID do aplicativo**.

   [![Captura de tela da opção de configuração do U R I do aplicativo I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
1. Selecione **Salvar**.

## <a name="create-a-secure-webhook-action-group"></a>Criar um grupo de ação de webhook seguro

Depois que o aplicativo for registrado no Azure AD, você poderá criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure, usando a ação proteger webhook em grupos de ações.

Os grupos de ações fornecem uma maneira modular e reutilizável de disparar ações para alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas de Log Analytics do Azure no portal do Azure.
Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

Use o procedimento a seguir no ambiente Helix do BMC:

1. Faça logon no Integration Studio.
1. Procure o fluxo **criar incidente do Azure Alerts** .
1. Copie a URL do webhook.
   
   ![Captura de tela do webhook U R L no Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Para adicionar um webhook a uma ação, siga estas instruções para o webhook seguro:

1. No [portal do Azure](https://portal.azure.com/), pesquise e selecione **Monitor**. O painel **Monitor** consolida todas as configurações e dados de monitoramento em uma exibição.
1. Selecione **alertas**  >  **Gerenciar ações**.
1. Selecione [Adicionar grupo de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal) e preencha os campos.
1. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.
1. Selecione **proteger webhook**.
1. Selecione estes detalhes:
   1. Selecione a ID de objeto da instância de Azure Active Directory que você registrou.
   1. Para o URI, Cole a URL do webhook que você copiou do ambiente do BMC Helix.
   1. Defina **habilitar o esquema de alerta comum** como **Sim**. 

   A imagem a seguir mostra a configuração de uma ação de webhook segura de exemplo:

   ![Captura de tela que mostra uma ação de webhook segura.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-partner-environment"></a>Configurar o ambiente de parceiro

### <a name="connect-bmc-helix-to-azure-monitor"></a>Conectar o BMC Helix ao Azure Monitor

As seções a seguir fornecem detalhes sobre como conectar seu produto BMC Helix e exportação segura no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você atendeu aos seguintes pré-requisitos:

* O Azure AD está registrado.
* Você tem a versão com suporte do gerenciamento de serviços de várias nuvens do BMC Helix (versão 19, 8 ou posterior).

### <a name="configure-the-bmc-helix-connection"></a>Configurar a conexão do BMC Helix

1. Siga as instruções de Accor para a versão:
   * [Habilitando a integração predefinida com o Azure monitor para a versão 20, 2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Habilitando a integração predefinida com o Azure monitor para a versão 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

1. Como parte da configuração da conexão no BMC Helix, acesse sua instância de integração do BMC e siga estas instruções:

   1. Selecione **Catálogo**.
   1. Selecione **alertas do Azure**.
   1. Selecione **conectores**.
   1. Selecione **configuração**.
   1. Selecione **Adicionar nova** configuração de conexão.
   1. Preencha as informações da seção de configuração:
      - **Nome**: Crie o seu próprio.
      - **Tipo de autorização**: **nenhum**
      - **Descrição**: Crie o seu próprio.
      - **Site**: **nuvem**
      - **Número de instâncias**: **2**, o valor padrão.
      - **Marque**: selecionado por padrão para habilitar o uso.
      - A ID do locatário do Azure e a ID do aplicativo do Azure são obtidas do aplicativo que você definiu anteriormente.

![Captura de tela que mostra a configuração do BMC.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar itens de trabalho de ITSM desde alertas do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview)
