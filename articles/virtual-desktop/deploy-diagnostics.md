---
title: Implantar a ferramenta de diagnóstico para Windows Virtual Desktop - Azure
description: Como implantar a ferramenta UX de diagnósticos para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123401"
---
# <a name="deploy-the-diagnostics-tool"></a>Implantar a ferramenta de diagnóstico

>[!IMPORTANT]
>A partir de 16 de março de 2020, desativamos temporariamente consultas de diagnóstico que afetaram a experiência do usuário devido ao aumento da demanda no serviço. Isso fará com que a ferramenta pare de funcionar porque depende dessas consultas para funcionar. Atualizaremos este artigo quando as consultas de diagnóstico estiverem disponíveis novamente.
>
>Até lá, recomendamos que você [use o Log Analytics](diagnostics-log-analytics.md) para monitoramento contínuo.

Veja o que a ferramenta de diagnóstico para o Windows Virtual Desktop pode fazer por você:

- Procure atividades de diagnóstico (gerenciamento, conexão ou feed) para um único usuário durante um período de uma semana.
- Reúna as informações do host de sessão para atividades de conexão do espaço de trabalho do Log Analytics.
- Revise os detalhes de desempenho da máquina virtual (VM) para um determinado host.
- Veja quais usuários estão logados no host da sessão.
- Envie mensagem para usuários ativos em um host de sessão específico.
- Faça login de usuários de um host de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa criar um registro de aplicativo do diretório ativo do Azure e um espaço de trabalho do Log Analytics antes de implantar o modelo do Azure Resource Manager para a ferramenta. Você ou o administrador precisam dessas permissões para fazer isso:

- Dono da assinatura do Azure
- Permissão para criar recursos em sua assinatura do Azure
- Permissão para criar um aplicativo AD do Azure
- RdS Proprietário ou Direitos do Contribuinte

Você também precisa instalar esses dois módulos PowerShell antes de começar:

- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Módulo Azure AD](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Certifique-se de ter seu ID de assinatura pronto para quando fizer login.

Depois de ter tudo em ordem, você pode criar o registro do aplicativo Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Crie um registro de aplicativo do Azure Active Directory

Esta seção mostrará como usar o PowerShell para criar o aplicativo Azure Active Directory com um diretor de serviço e obter permissões de API para ele.

>[!NOTE]
>As permissões de API são o Windows Virtual Desktop, o Log Analytics e as permissões da Microsoft Graph API são adicionadas ao Azure Active Directory Application.

1. Abra o PowerShell como Administrador.
2. Faça login no Azure com uma conta que tenha permissões de Proprietário ou Contribuinte na assinatura do Azure que você gostaria de usar para a ferramenta de diagnóstico:
   ```powershell
   Login-AzAccount
   ```
3. Faça login no Azure AD com a mesma conta:
   ```powershell
   Connect-AzureAD
   ```
4. Acesse o [repo RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateADAppRegistrationforDiagnostics.ps1** no PowerShell.
5.  Quando o script pedir para você nomear seu aplicativo, digite um nome de aplicativo exclusivo.


Depois que o script for executado com sucesso, ele deve mostrar as seguintes coisas em sua saída:

-  Uma mensagem que confirma que seu aplicativo agora tem uma atribuição principal de função de serviço.
-  Seu ID do cliente e a chave secreta do cliente que você precisará para quando implantar a ferramenta de diagnóstico.

Agora que você registrou seu aplicativo, é hora de configurar seu espaço de trabalho do Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configure seu espaço de trabalho do Log Analytics

Para a melhor experiência possível, recomendamos que você configure seu espaço de trabalho do Log Analytics com os seguintes contadores de desempenho que permitem derivar declarações da experiência do usuário em uma sessão remota. Para obter uma lista de contadores recomendados com limites sugeridos, consulte [os limites do contador de desempenho do Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Crie um espaço de trabalho do Azure Log Analytics usando o PowerShell

Você pode executar um script PowerShell para criar um espaço de trabalho do Log Analytics e configurar os contadores de desempenho recomendados do Windows para monitorar a experiência do usuário e o desempenho do aplicativo.

>[!NOTE]
>Se você já tiver um espaço de trabalho do Log Analytics existente que você fez sem o script PowerShell que deseja usar, pule adiante para [validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal).

Para executar o script PowerShell:

1.  Abrir o PowerShell como administrador.
2.  Acesse o [repo RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateLogAnalyticsWorkforDiagnostics.ps1** no PowerShell.
3. Insira os seguintes valores para os parâmetros:

    - Para **ResourceGroupName,** digite o nome do grupo de recursos.
    - Para **LogAnalyticsWorkspaceName,** digite um nome exclusivo para o espaço de trabalho do Log Analytics.
    - Para **Localização,** digite a região Azure que você está usando.
    - Insira o ID de assinatura do **Azure,** que você pode encontrar no portal Azure em **Assinaturas**.

4. Digite as credenciais de um usuário com acesso de admin delegado.
5. Faça login no portal Azure com as credenciais do mesmo usuário.
6. Anote ou memorize o ID loganalyticsWorkspace para depois.
7. Se você configurar o espaço de trabalho do Log Analytics com o script PowerShell, então seus contadores de desempenho já devem estar configurados e você pode pular adiante para [validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal). Caso contrário, continue até a próxima seção.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configure contadores de desempenho do Windows no espaço de trabalho do Log Analytics existente

Esta seção é para usuários que desejam usar um espaço de trabalho existente do Azure Log Analytics criado sem o script PowerShell na seção anterior. Se você não tiver usado o script, então você deve configurar os contadores de desempenho recomendados do Windows manualmente.

Veja como configurar manualmente os contadores de desempenho recomendados:

1. Abra seu navegador de internet e faça login no [portal Azure](https://portal.azure.com/) com sua conta administrativa.
2. Em seguida, vá aos **espaços de trabalho do Log Analytics** para revisar os contadores de desempenho configurados do Windows.
3. Na seção **Configurações,** selecione **Configurações avançadas**.
4. Depois disso, navegue até**os contadores de desempenho do** **Data** > Windows e adicione os seguintes contadores:

    -   LogicalDisk\*(\\) %Espaço Livre
    -   LogicalDisk(C:)\\Comprimento da fila do disco
    -   Memória\*(\\) Mbytes disponíveis
    -   Informações do\*\\processador ( ) Tempo do processador
    -   Atraso de entrada\*do\\usuário por sessão ( ) Atraso máximo de entrada

Saiba mais sobre os contadores de desempenho nas [fontes de dados de desempenho do Windows e Linux no Azure Monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Quaisquer contadores adicionais configurados não aparecerão na própria ferramenta de diagnóstico. Para fazê-lo aparecer na ferramenta de diagnóstico, você precisa configurar o arquivo de configuração da ferramenta. Instruções de como fazer isso com administração avançada estarão disponíveis no GitHub em uma data posterior.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Validar os resultados do script no portal Azure

Antes de continuar implantando a ferramenta de diagnóstico, recomendamos que você verifique se o aplicativo Azure Active Directory tem permissões de API e seu espaço de trabalho Log Analytics tem os contadores de desempenho do Windows pré-configurados.

### <a name="review-your-app-registration"></a>Revise o registro do seu aplicativo

Para garantir que o registro do aplicativo tenha permissões de API:

1. Abra um navegador e conecte-se [ao portal Azure](https://portal.azure.com/) com sua conta administrativa.
2. Acesse o **Azure Active Directory**.
3. Acesse **as inscrições do App** e selecione **Todas as aplicações**.
4. Procure o registro do aplicativo Azure AD com o mesmo nome do aplicativo que você inseriu na etapa 5 do Criar um registro do [aplicativo Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Revise seu espaço de trabalho do Log Analytics

Para garantir que o espaço de trabalho do Log Analytics tenha os contadores de desempenho pré-configurados do Windows:

1. No [portal Azure,](https://portal.azure.com/)vá aos **espaços de trabalho do Log Analytics** para revisar os contadores de desempenho configurados do Windows.
2. Em **Configurações,** selecione **Configurações avançadas**.
3. Depois disso, vá para Os**contadores de desempenho do** **Data** > Windows .
4. Certifique-se de que os seguintes contadores estão pré-configurados:

   - LogicalDisk(\*\\) %Espaço Livre: Exibe a quantidade de espaço livre do espaço utilizável total no disco em percentagem.
   - LogicalDisk(C:)\\Comprimento da fila do disco: O comprimento da solicitação de transferência de disco para sua unidade C. O valor não deve exceder 2 por mais de um curto período de tempo.
   - Memória(\*\\) Mbytes disponíveis: A memória disponível para o sistema em megabytes.
   - Informações do\*\\processador( ) Tempo do processador: a porcentagem de tempo decorrido que o processador gasta para executar um segmento não ocioso.
   - Atraso de entrada\*do\\usuário por sessão ( ) Atraso máximo de entrada

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Conecte-se a VMs em seu espaço de trabalho log analytics

Para poder visualizar a saúde das VMs, você precisará ativar a conexão Log Analytics. Siga estas etapas para conectar suas VMs:

1. Abra um navegador e faça login no [portal Azure](https://portal.azure.com/) com sua conta administrativa.
2. Vá para o seu Espaço de Trabalho do Log Analytics.
3. No painel esquerdo, em Fontes de dados do espaço de trabalho, selecione **máquinas virtuais**.
4. Selecione o nome da VM a que deseja se conectar.
5. Selecione **Conectar**.

## <a name="deploy-the-diagnostics-tool"></a>Implantar a ferramenta de diagnóstico

Para implantar o modelo de gerenciamento de recursos do Azure para a ferramenta de diagnóstico:

1.  Vá para a [página GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Implante o modelo no Azure e siga as instruções no modelo. Certifique-se de ter as seguintes informações disponíveis:

    -   ID do cliente
    -   Client-Secret
    -   ID do espaço de trabalho do Log Analytics

3.  Uma vez que os parâmetros de entrada sejam fornecidos, aceite os termos e condições e selecione **Comprar**.

A implantação levará de 2 a 3 minutos. Após a implantação bem-sucedida, vá para o grupo de recursos e certifique-se de que os recursos do plano de serviço do aplicativo web e do aplicativo estejam lá.

Depois disso, você precisa definir o URI redirecionamento.

### <a name="set-the-redirect-uri"></a>Defina o URI de redirecionamento

Para definir o URI de redirecionamento:

1.  No [portal Azure,](https://portal.azure.com/)acesse **App Services** e localize o aplicativo que você criou.
2.  Vá para a página de visão geral e copie a URL que você encontrar lá.
3.  Navegue até **os registros do aplicativo** e selecione o aplicativo que deseja implantar.
4.  No painel esquerdo, em Gerenciar seção, selecione **Autenticação**.
5.  Digite o URI de redirecionamento desejado na caixa de texto **REdirecionar URI** e, em seguida, **selecione Salvar** no canto superior esquerdo do menu.
6. Selecione **Web** no menu suspenso em Tipo.
7. Digite a URL na página de visão geral do aplicativo e adicione **/security/signin-callback** ao final dela. Por exemplo: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![A página uri redirecionada](media/redirect-uri-page.png)

8. Agora, vá para os recursos do Azure, selecione o recurso Azure App Services com o nome fornecido no modelo e navegue até a URL associada a ele. (Por exemplo, se o nome do `contosoapp45`aplicativo que você <https://contosoapp45.azurewebsites.net>usou no modelo foi, então sua URL associada é ).
9. Entre usando a conta de usuário do Azure Active Directory apropriada.
10.   Selecione **Aceitar**.

## <a name="distribute-the-diagnostics-tool"></a>Distribua a ferramenta de diagnóstico

Antes de disponibilizar a ferramenta de diagnóstico aos seus usuários, certifique-se de que eles tenham as seguintes permissões:

- Os usuários precisam de acesso à leitura para análise de log. Para obter mais informações, consulte [Comece com funções, permissões e segurança com o Monitor Azure](/azure/azure-monitor/platform/roles-permissions-security).
-  Os usuários também precisam de acesso de leitura para o inquilino do Windows Virtual Desktop (função RDS Reader). Para obter mais informações, consulte [acesso delegado no Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Você também precisa dar aos seus usuários as seguintes informações:

- URL do aplicativo
- Os nomes do inquilino individual do grupo podem acessar.

## <a name="use-the-diagnostics-tool"></a>Use a ferramenta de diagnóstico

Depois de fazer o fizer o fizer em sua conta usando as informações recebidas da sua organização, prepare a UPN para o usuário para o que deseja consultar as atividades. Uma pesquisa lhe dará todas as atividades o tipo de atividade especificada que aconteceu na última semana.

### <a name="how-to-read-activity-search-results"></a>Como ler os resultados da pesquisa de atividades

As atividades são classificadas por carimbo de data e hora, com a última atividade primeiro. Se os resultados retornarem um erro, primeiro verifique se é um erro de serviço. Para erros de serviço, crie um ticket de suporte com as informações de atividade para nos ajudar a depurar o problema. Todos os outros tipos de erros geralmente podem ser resolvidos pelo usuário ou administrador. Para obter uma lista dos cenários de erro mais comuns e como resolvê-los, consulte [Identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Os erros de serviço são chamados de "erros externos" na documentação vinculada. Isso será alterado quando atualizarmos a referência PowerShell.

As atividades de conexão podem ter mais de um erro. Você pode expandir o tipo de atividade para ver quaisquer outros erros que o usuário tenha visto. Selecione o nome do código de erro para abrir uma caixa de diálogo para ver mais informações sobre ele.

### <a name="investigate-the-session-host"></a>Investigue o anfitrião da sessão 

Nos resultados da pesquisa, encontre e selecione o host de sessão sobre o qual você deseja obter informações.

Você pode analisar a saúde do anfitrião da sessão:

- Com base em um limite predefinido, você pode recuperar as informações de saúde do host de sessão que o Log Analytics consulta.
- Quando não houver atividade ou o host de sessão não estiver conectado ao Log Analytics, as informações não estarão disponíveis.

Você também pode interagir com os usuários no host de sessão:

- Você pode sair ou enviar uma mensagem para usuários com login.
- O usuário que você pesquisou originalmente é selecionado por padrão, mas você também pode selecionar usuários adicionais para enviar mensagens ou assinar vários usuários ao mesmo tempo.

### <a name="windows-performance-counter-thresholds"></a>Limites do contador de desempenho do Windows

- LogicalDisk\*(\\) %Espaço Livre:

    - Exibe a porcentagem do espaço utilizável total no disco lógico que é livre.
    - Limiar: Menos de 20% é marcado como insalubre.

- LogicDisk(C:)\\Comprimento da fila do disco:

    - Representa as condições do sistema de armazenamento.
    - Limiar: Maior que 5 é marcado como insalubre.

- Memória(\*\\) Mbytes disponíveis:

    - A memória disponível para o sistema.
    - Limite: Menos de 500 megabytes marcados como insalubres.

- Informações do\*\\processador ( ) Tempo do processador:

    - Limiar: Maior que 80% é marcado como insalubre.

- [Atraso de entrada\*do\\usuário por sessão( ) Atraso máximo de entrada:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Limiar: Maior que 2000 ms é marcado como insalubre.

## <a name="next-steps"></a>Próximas etapas

- Saiba como monitorar registros de atividades em [Usar diagnósticos com o Log Analytics](diagnostics-log-analytics.md).
- Leia sobre cenários de erro comuns e como corrigi-los em [Identificar e diagnosticar problemas.](diagnostics-role-service.md)
