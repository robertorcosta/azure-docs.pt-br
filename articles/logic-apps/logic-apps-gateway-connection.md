---
title: Acessar fontes de dados no local
description: Conecte-se a fontes de dados locais do Azure Logic Apps criando um recurso de gateway de dados no local do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657130"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Conectar-se a fontes de dados locais dos Aplicativos Lógicos do Azure

Antes de acessar fontes de dados em locais a partir de seus aplicativos lógicos, você precisa criar um recurso do Azure depois [de instalar o gateway de dados *local* em um computador local](../logic-apps/logic-apps-gateway-install.md). Seus aplicativos de lógica usam esse recurso gateway do Azure nos gatilhos e ações fornecidos pelos [conectores locais](../connectors/apis-list.md#on-premises-connectors) disponíveis para aplicativos azure logic.

Este artigo mostra como criar seu recurso gateway do Azure para um [gateway previamente instalado em seu computador local](../logic-apps/logic-apps-gateway-install.md). Para obter mais informações sobre o gateway, consulte [Como funciona o gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Para se conectar a redes virtuais do Azure, considere criar um [*ambiente de serviço de integração*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados do Microsoft Power Automate on-premises](/power-automate/gateway-reference)
* [Gateway de dados local do Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway de dados do Microsoft Power Apps no local](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

No Azure Logic Apps, o gateway de dados local suporta os [conectores locais](../connectors/apis-list.md#on-premises-connectors) para essas fontes de dados:

* BizTalk Server 2016
* Sistema de Arquivos
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Banco de dados Oracle
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

O Azure Logic Apps suporta operações de leitura e gravação através do gateway de dados. No entanto, essas operações têm [limites em seu tamanho de carga.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations) Embora o gateway em si não incorra em custos adicionais, o [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md) se aplica a esses conectores e outras operações no Azure Logic Apps.

## <a name="prerequisites"></a>Pré-requisitos

* Você já [instalou o gateway de dados no local em um computador local](../logic-apps/logic-apps-gateway-install.md).

* Você está usando a [mesma conta e assinatura do Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que foi usada ao instalar esse gateway de dados. Esta conta Azure deve pertencer a um único [inquilino ou diretório do Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* Sua instalação de gateway ainda não está registrada e reivindicada por outro recurso do Gateway Do Azure.

  Quando você cria um recurso de gateway no portal Azure, você seleciona uma instalação de gateway, que se conecta ao recurso do gateway e apenas ao recurso gateway. Nos Aplicativos de Lógica do Azure, os gatilhos e ações no local usam o recurso gateway para se conectar a fontes de dados locais. Nesses gatilhos e ações, você seleciona sua assinatura do Azure e o recurso de gateway associado que deseja usar. Cada recurso de gateway é vinculado a apenas uma instalação de gateway, que se conecta a apenas uma conta do Azure.

  > [!NOTE]
  > Apenas o administrador de gateway pode criar o recurso gateway no portal Azure. Atualmente, os diretores de serviço não são suportados. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso gateway do Azure

Depois de instalar o gateway em um computador local, crie o recurso Azure para o seu gateway.

1. Faça login no [portal Azure](https://portal.azure.com) com a mesma conta do Azure que foi usada para instalar o gateway.

1. Na caixa de pesquisa do portal Azure, digite o "gateway de dados no local" e selecione **Gateways de dados on-premises**.

   ![Localize “Gateway de dados local”](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Em **Gateways de dados on-premises,** selecione **Adicionar**.

   ![Adicionar novo recurso do Azure para gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Em **Criar gateway de conexão,** forneça essas informações para o recurso gateway. Quando terminar, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do recurso** | Forneça um nome para o recurso de gateway que`-`contenha apenas`_`letras, números,`(` `)`hífens`.`( ), sublinha (), parênteses ( ), ou períodos ( ). |
   | **Assinatura** | Selecione a assinatura do Azure para a conta do Azure que foi usada para a instalação do gateway. A assinatura padrão baseia-se na conta do Azure utilizada para entrar. |
   | **Grupo de recursos** | O [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) que você deseja usar |
   | **Local** | A mesma região ou local selecionado para o serviço de nuvem de gateway durante a [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). Caso contrário, sua instalação do gateway não aparecerá na lista **Nome de instalação.** Sua localização lógica do aplicativo pode diferir da localização do recurso do gateway. |
   | **Nome de Instalação** | Selecione uma instalação de gateway, que aparece na lista somente quando essas condições forem atendidas: <p><p>- A instalação do gateway usa a mesma região do recurso gateway que você deseja criar. <br>- A instalação do gateway não está vinculada a outro recurso de gateway do Azure. <br>- A instalação do gateway está vinculada à mesma conta do Azure que você está usando para criar o recurso gateway. <br>- Sua conta do Azure pertence a um único [inquilino ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que foi usada para a instalação do gateway. <p><p>Para obter mais informações, consulte a seção [de perguntas frequentes.](#faq) |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que seu recurso gateway e está vinculado à mesma conta do Azure:

   ![Fornecer detalhes para criar recurso de gateway de dados](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Conectar-se a dados locais

Após criar o recurso de gateway e associar a assinatura do Azure a esse recurso, será possível criar uma conexão entre o aplicativo lógico e a fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. Adicione um conector que dê suporte a conexões locais, por exemplo **SQL Server**.

1. Selecione **Conectar por meio do gateway de dados local**.

1. Em **Gateways,** da lista **De assinaturas,** selecione sua assinatura do Azure que tenha o recurso gateway desejado.

1. Na lista **Do Gateway de conexão,** que mostra os recursos de gateway disponíveis na assinatura selecionada, selecione o recurso gateway que você deseja. Cada recurso de gateway está vinculado a uma única instalação de gateway.

   > [!NOTE]
   > A lista de gateways inclui recursos de gateway em outras regiões porque a localização do seu aplicativo lógico pode diferir da localização do seu recurso gateway. 

1. Forneça um nome de conexão único e outras informações necessárias, que dependem da conexão que você deseja criar.

   Um nome de conexão único ajuda você a encontrar facilmente essa conexão mais tarde, especialmente se você criar várias conexões. Se aplicável, também inclua o domínio qualificado para seu nome de usuário.

   Veja um exemplo:

   ![Criar a conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Quando terminar, selecione **Criar**.

A conexão de gateway agora está pronta para o aplicativo lógico usar.

## <a name="edit-connection"></a>Editar conexão

Para atualizar as configurações de uma conexão de gateway, você pode editar sua conexão.

1. Para encontrar todas as conexões de API apenas para o seu aplicativo lógico, no menu do seu aplicativo lógico, em **Ferramentas de Desenvolvimento,** selecione **conexões de API**.

   ![No menu do aplicativo lógico, selecione "Conexões de API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecione a conexão gateway desejada e selecione **Editar conexão API**.

   > [!TIP]
   > Se suas atualizações não fizerem efeito, tente [parar e reiniciar a conta de serviço do Gateway Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) para sua instalação de gateway.

Para localizar todas as conexões de API associadas à assinatura do Azure:

* No menu do portal Azure, selecione **Todos os serviços** > **de Conexões de API da****Web** > .
* Ou, a partir do menu do portal Azure, selecione **Todos os recursos**. Defina o filtro **Tipo** como **Conexão API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, vincule sua instalação de gateway a um recurso de gateway diferente ou remova o recurso gateway, você pode excluir o recurso do gateway sem afetar a instalação do gateway.

1. No menu do portal Azure, selecione **Todos os recursos**ou procure e selecione Todos os **recursos** de qualquer página. Localize e selecione o recurso de gateway.

1. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de Dados Local**. Na barra de ferramentas de recurso do gateway, **selecione Excluir**.

   Por exemplo:

   ![Excluir recurso de gateway no Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P:** Por que minha instalação de gateway não aparece quando crio meu recurso de gateway no Azure? <br/>
**R**: Esse problema pode ocorrer pelos seguintes motivos:

* Sua conta do Azure deve ser a mesma que está vinculada à instalação do gateway no computador local. Verifique se você está conectado ao portal Azure com a mesma identidade que está vinculada à instalação do gateway. Além disso, certifique-se de que sua conta Azure pertence a um único [inquilino ou diretório Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology) e está configurado para o mesmo inquilino ou diretório Azure AD que foi usado durante a instalação do gateway.

* Seu recurso de gateway e instalação de gateway têm que usar a mesma região. No entanto, sua localização lógica do aplicativo pode diferir da localização do recurso do gateway.

* Sua instalação de gateway já está registrada e reivindicada por outro recurso de gateway. Essas instalações não aparecerão na lista **Nome de instalação.** Para revisar seus registros de gateway no portal Azure, encontre todos os seus recursos do Azure que tenham o tipo **De Gateways de Dados on-premises** em *todas as* suas assinaturas do Azure. Para desvincular a instalação do gateway do outro recurso de gateway, consulte [Excluir recurso de gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Próximas etapas

* [Proteja seus aplicativos lógicos](./logic-apps-securing-a-logic-app.md)
* [Exemplos comuns e cenários de aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
