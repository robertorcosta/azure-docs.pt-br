---
title: Conector de Gerenciamento de Serviços de TI-exportação segura no Azure Monitor
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com a exportação segura no Azure Monitor para monitorar e gerenciar centralmente os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568635"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Conectar o Azure a ferramentas de ITSM usando exportação segura

Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM usando a exportação segura.

A exportação segura é e a versão atualizada do [ITSMC](./itsmc-overview.md) (gerenciamento de serviços de ti). Ambas as versões permitem que você crie itens de trabalho em uma ferramenta de ITSM quando Azure Monitor alertas são acionados. A funcionalidade inclui alertas de métrica, log e log de atividades.

O [ITSMC](./itsmc-overview.md) usa credenciais de usuário e senha, enquanto a exportação segura tem autenticação mais forte porque usa Azure Active Directory (Azure AD). O Azure AD (Azure Active Directory) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Ele ajuda os usuários a entrar e acessar recursos internos ou externos. Usar o Azure AD com o ITSM ajuda a identificar alertas do Azure (usando a ID do aplicativo do Azure AD) que foram enviados para o sistema externo.

> [!NOTE]
> As ferramentas conectar Azure a ITSM usando exportação segura está em versão prévia

## <a name="secure-export-architecture"></a>Arquitetura de exportação segura

A arquitetura de exportação segura apresenta os seguintes novos recursos:

* **Novo grupo de ação** -os alertas são enviados para a ferramenta de ITSM usando o grupo de ações do webhook seguro (em vez do grupo de ações de ITSM usando o no ITSMC).
* **Autenticação do Azure ad** -a autenticação ocorre usando o Azure AD em vez de credenciais de usuário/senha.

## <a name="secure-export-data-flow"></a>Fluxo de dados de exportação segura

As etapas de fluxo de dados de exportação segura são:

1) Um alerta que está configurado para usar a exportação segura é disparado no Azure Monitor
2) A carga do alerta é enviada a uma ação de webhook segura para a ferramenta ITSM.
3) O aplicativo ITSM verifica com o Azure AD se o alerta está autorizado a entrar na ferramenta de ITSM.
4) Se o alerta estiver autorizado no aplicativo:
    1) Cria um item de trabalho (por exemplo, incidente) na ferramenta ITSM.
    2) Associa a ID do item de configuração (CI) ao banco de dados de gerenciamento do cliente (CMDB).
![Diagrama de ITSM](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Conexão com BMC Helix

A exportação segura dá suporte ao BMC Helix. Alguns benefícios da integração são:

* **Melhor autenticação** – o Azure ad fornece autenticação mais segura sem os tempos limite que normalmente ocorrem no ITSMC.
* **Alertas resolvidos na ferramenta de ITSM** – os alertas de métrica implementam um estado "acionado" e "resolvido". Quando a condição for atendida, o estado do alerta será "disparado". Quando a condição não for mais atendida, o estado do alerta será "resolvido". No ITSMC, os alertas não puderam ser resolvidos automaticamente. Com a exportação segura, o estado resolvido flui para a ferramenta de ITSM e, portanto, é atualizado automaticamente.
* O **[esquema comum permite](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – em ITSMC, o esquema da carga de alerta difere com base no tipo de alerta. Em exportação segura, nós temos um esquema comum para todos os tipos de alertas. Esse novo esquema comum contém o CI para todos os tipos de alerta. Por isso, todos os tipos de alerta poderão associar seu IC ao CMDB.

Comece a usar o Conector ITSM com estas etapas:

1. Registre seu aplicativo com Azure Active Directory.
2. Crie um grupo de ação de webhook seguro.
3. Configure seu ambiente de parceiro.

## <a name="register-with-azure-active-directory"></a>Registrar com Azure Active Directory

Siga estas etapas para registrar o aplicativo do Azure AD com o Azure Active Directory

1) [Criação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) Na Azure Active Directory selecione "expor aplicativo"
3) Selecione definir no URI da ID [ ![ do aplicativo Azure ad](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Clique em Salvar.

## <a name="create-a-secure-webhook-action-group"></a>Criar um grupo de ação de webhook seguro

Depois de registrar o Azure AD, você pode criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure, usando a ação proteger webhook em grupos de ações.
Os Grupos de Ações fornecem uma maneira modular e reutilizável de disparar ações para os Alertas do Azure. Use Grupos de Ação com alertas de métricas, alertas do Log de Atividades e alertas do Azure Log Analytics no portal do Azure.
Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Use este procedimento:

No BMC Helix Environment:

1. Faça logon no Integration Studio.
2. Procure o fluxo criar incidente do Azure Alerts.
3. Copie a URL do webhook.
![URL DO BMC](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Para adicionar um webhook a uma ação, siga as instruções para um webhook seguro:

1. No [portal do Azure](https://portal.azure.com/), pesquise e selecione **Monitor**. O painel **Monitor** consolida todas as configurações e dados de monitoramento em uma exibição.
2. Selecione **Alertas**, em seguida, selecione **Gerenciar ações**.
3. Selecione [Adicionar grupo de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal) e preencha os campos.
4. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.
5. Selecionar **webhook protegido**
6. Selecione Editar detalhes. A imagem a seguir mostra uma ação de webhook protegida por exemplo:
    1. Selecione a ID de objeto correta da Azure Active Directory que você registrou
    2. Cole o campo no URI a URL do webhook que você copiou do "ambiente Helix do BMC"
    3. Defina o **esquema de alerta comum** como **Sim**. 
7. A imagem a seguir mostra um exemplo de configuração de ação de webhook protegida: ![ webhook seguro](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Configurar ambiente de parceiro

### <a name="connect-bmc-helix-to-azure-monitor"></a>Conectar o BMC Helix ao Azure Monitor

A seção a seguir fornece detalhes sobre como conectar seu produto BMC Helix e exportar com segurança no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

* O Azure AD está registrado.
* A versão com suporte do gerenciamento de serviços de várias nuvens do BMC Helix: versão 20, 2 ou posterior

Para configurar a conexão do BMC Helix:

1) [Habilitando a integração predefinida com o Azure Monitor para a versão 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Como parte da configuração da conexão no Helix do BMC, acesse sua instância de integração do BMC e siga as instruções:

1. Selecionar **Catálogo**
2. Selecionar **alertas do Azure**
3. Selecionar **conectores**
4. Selecionar **configuração**
5. Selecione **Adicionar nova** configuração de conexão
6. Preencha as informações da seção de configuração.
    1. **Nome** -crie seu próprio
    2. **Tipo de autorização** -nenhum
    3. **Descrição**-crie seu próprio
    4. **Site**-nuvem
    5. **Número de instâncias** -2 – valor padrão
    6. **Marcar** -selecionado por padrão e habilitar o uso
    7. ID de locatário do Azure, ID do aplicativo do Azure são obtidas do aplicativo que foram definidos na etapa "criado Azure Active Directory".
![Configuração do BMC](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
