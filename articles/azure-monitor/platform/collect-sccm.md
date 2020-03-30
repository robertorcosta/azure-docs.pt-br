---
title: Conecte o Gerenciador de Configuração ao Monitor Azure | Microsoft Docs
description: Este artigo mostra as etapas para conectar o Gerenciador de Configuração ao espaço de trabalho no Azure Monitor e começar a analisar os dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655249"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conecte o Gerenciador de Configuração ao Monitor Azure
Você pode conectar o ambiente do Microsoft Endpoint Configuration Manager ao Azure Monitor para sincronizar dados de coleta de dispositivos e referenciar essas coleções no Monitor Azure e automação do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor suporta a filial atual do Configuration Manager, versão 1606 ou superior.

>[!NOTE]
>O recurso para conectar o Configuration Manager com um espaço de trabalho do Log Analytics é opcional e não está ativado por padrão. É necessário habilitar esse recurso antes de usá-lo. Para obter mais informações, consulte [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Visão geral de configuração

As etapas a seguir resumem as etapas para configurar a integração do Gerenciador de Configuração com o Monitor Do Azure.  

1. No Azure Active Directory, registre o Gerenciador de Configuração como um aplicativo web e/ou um aplicativo de API da Web e garanta que você tenha o ID do cliente e a chave secreta do cliente a partir do registro do Azure Active Directory. Consulte [Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md) para obter informações detalhadas sobre como executar essa etapa.

2. No Azure Active Directory, [conceda o Configuration Manager (o aplicativo web registrado) com permissão para acessar o Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. No Gerenciador de configuração, adicione uma conexão usando o assistente **Azure Services.**

4. [Baixe e instale o agente Log Analytics para Windows](#download-and-install-the-agent) no computador executando a função de site de ponto de conexão do gerenciador de configuração. O agente envia dados do Gerenciador de Configuração para o espaço de trabalho do Log Analytics no Azure Monitor.

5. No Monitor Azure, [importe coleções do Gerenciador de Configuração](#import-collections) como grupos de computador.

6. No Azure Monitor, visualize dados do Gerenciador de Configuração como [grupos de computador](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Conceder o Configuration Manager com permissões para o Log Analytics

No procedimento a seguir, você concede a função *Colaborador* no espaço de trabalho do Log Analytics ao aplicativo do AD e à entidade de serviço criada anteriormente para o Configuration Manager. Se você ainda não tiver um espaço de trabalho, consulte [Criar um espaço de trabalho no Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) antes de prosseguir. Isso permite que o Configuration Manager autentique e conecte-se ao espaço de trabalho do Log Analytics.  

> [!NOTE]
> Você deve especificar permissões no espaço de trabalho Log Analytics para Gerenciador de configuração. Caso contrário, você receberá uma mensagem de erro ao usar o assistente de configuração do Configuration Manager.
>

1. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, **digite Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Análise de log**.

2. Na lista de workspaces do Log Analytics, selecione o workspace para modificar.

3. No painel esquerdo, selecione **Controle de acesso (IAM)**.

4. Na página Controle de acesso (IAM), clique em **Adicionar atribuição de função** e o painel **Adicionar atribuição de função** será exibido.

5. No painel **Adicionar atribuição de função** na lista suspensa **Função**, selecione a função **Colaborador**.  

6. Na lista suspensa **Atribuir acesso a** selecione o aplicativo Configuration Manager criado no AD anteriormente e clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Baixe e instale o agente

Revise o artigo [Conecte computadores Windows ao Azure Monitor no Azure](agent-windows.md) para entender os métodos disponíveis para instalar o agente Log Analytics para Windows no computador que hospeda a função de sistema de ponto de conexão do Gerenciador de Configuração.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Conecte o Gerenciador de Configuração ao espaço de trabalho do Log Analytics

>[!NOTE]
> Para adicionar uma conexão Log Analytics, o ambiente do Gerenciador de Configuração deve ter um [ponto de conexão de serviço](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) configurado para o modo on-line.

> [!NOTE]
> Você deve conectar o site de primeira linha em sua hierarquia ao Monitor Do Azure. Se você conectar um site principal autônomo ao Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, você terá que excluir e recriar a conexão dentro da nova hierarquia.

1. No espaço de trabalho de **Administração** do Gerenciador de configuração, selecione **Serviços de nuvens** e selecione **Serviços Azure**. 

2. Clique com o botão direito do mouse **nos serviços do Azure** e, em seguida, **selecione Configurar serviços do Azure**. A **página Configurar Serviços Azure** é exibida. 
   
3. Na tela **Geral**, confirme se você realizou as seguintes ações e se tem os detalhes de cada item, em seguida, selecione **Avançar**.

4. Na página Serviços do Azure do Assistente de Serviços do Azure:

    1. Especifique um **Nome** para o objeto no Configuration Manager.
    2. Especifique uma **Descrição** opcional para ajudá-lo a identificar o serviço.
    3. Selecione o **conector OMS**do serviço Azure .

    >[!NOTE]
    >O OMS agora é referido como Log Analytics, que é uma característica do Azure Monitor.

5. Selecione **Avançar** para continuar para a página Propriedades do aplicativo do Azure do Assistente de Serviços do Azure.

6. Na página **do App** do Azure Services Wizard, primeiro selecione o ambiente Azure na lista e clique em **Importar**.

7. Na página **Aplicativos de importação,** especifique as seguintes informações:

    1. Especifique o nome do **inquilino azure AD** para o aplicativo.

    2. Especificar para **Azure AD Tenant ID** o inquilino Azure AD. Essas informações podem encontrar essas informações na página **Propriedades** do Diretório Ativo do Azure. 

    3. Especifique para **nome do aplicativo** o nome do aplicativo.

    4. Especificar para **ID do cliente**, o ID de aplicativo do aplicativo Azure AD criado anteriormente.

    5. Especificar para **a chave Secreta**, a chave secreta do Cliente do aplicativo Azure AD criado.

    6. Especifique para **Expiração da chave secreta,** a data de validade da sua chave.

    7. Especifique para **App ID URI**, o App ID URI do aplicativo Azure AD criado anteriormente.

    8. Selecione **Verificar** e à direita os resultados devem mostrar **verificado com sucesso!**.

8. Na página **Configuração,** revise as informações para verificar as assinaturas do **Azure,** **o grupo de recursos Do Azure**e os campos de espaço de trabalho do Operations Management **Suite** estão pré-preenchidos, indicando que o aplicativo Azure AD tem permissões suficientes no grupo de recursos. Se os campos estiverem vazios, indica que sua aplicação não tem os direitos necessários. Selecione as coleções de dispositivos para coletar e encaminhar para o espaço de trabalho e, em seguida, **selecione Adicionar**.

9. Revise as opções na **página Confirmar as configurações** e selecione **'Seguinte'** para começar a criar e configurar a conexão.

10. Quando a configuração estiver concluída, a **página Concluir** será exibida. Selecione **Fechar**. 

Depois de vincular o Gerenciador de Configuração ao Monitor Do Azure, você pode adicionar ou remover coleções e visualizar as propriedades da conexão.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar as propriedades de conexão do espaço de trabalho do Log Analytics

Se uma senha ou chave secreta do cliente expirar ou for perdida, você precisará atualizar manualmente as propriedades de conexão do Log Analytics.

1. No espaço de trabalho de **Administração** do Gerenciador de Configuração, selecione **Serviços de nuvem** e selecione o **Conector OMS** para abrir a página Propriedades de **conexão do OMS.**
2. Nessa página, clique na guia **Azure Active Directory** para exibir seu **Locatário**, **ID do Cliente**, **Client secret key expiration (Expiração da chave secreta do cliente)**. **Verifique se** sua **Chave secreta do cliente** caso ela tenha expirado.

## <a name="import-collections"></a>Importe coleções

Depois de adicionar uma conexão Log Analytics ao Gerenciador de configuração e instalar o agente no computador executando a função de sistema de ponto de conexão do gerenciador de configuração, o próximo passo é importar coleções do Gerenciador de Configuração no Azure Monitore como grupos de computador.

Depois de concluir a configuração inicial para importar coleções de dispositivos de sua hierarquia, as informações de coleta são recuperadas a cada 3 horas para manter a adesão atual. É possível escolher desabilitar isso a qualquer momento.

1. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, **digite Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **espaços de trabalho do Log Analytics**.
2. Na sua lista de workspaces do Log Analytics, selecione o workspace no qual o Configuration Manager está registrado.  
3. Selecione **Configurações avançadas**.
4. Selecione **Grupos de Computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **Importar associações de coleta do Configuration Manager** e clique em **Salvar**.  
   
    ![Grupos de computadores – guia SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Exibir dados do Configuration Manager

Depois de adicionar uma conexão Log Analytics ao Gerenciador de configuração e instalar o agente no computador executando a função de sistema de ponto de conexão do Gerenciador de configuração, os dados do agente são enviados para o espaço de trabalho do Log Analytics no Azure Monitor. No Monitor Azure, suas coleções do Gerenciador de Configuração aparecem como [grupos de computador](../../azure-monitor/platform/computer-groups.md). Você pode exibir os grupos da página **Configuration Manager** em **Configurações\Grupos de Computadores**.

Depois que as coleções forem importadas, você poderá ver quantos computadores com associações de coleção foram detectados. Você também pode ver o número de coleções que foram importadas.

![Grupos de computadores – guia SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, o editor de consulta de log abre exibindo todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando [o Log Search,](../../azure-monitor/log-query/log-query-overview.md)você pode realizar uma análise mais aprofundada dos dados de membros da coleta.

## <a name="next-steps"></a>Próximas etapas

Use a [Pesquisa de Log](../../azure-monitor/log-query/log-query-overview.md) para exibir informações detalhadas sobre os dados do Configuration Manager.
