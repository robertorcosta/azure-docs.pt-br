---
title: Controle do código-fonte no Azure Data Factory | Microsoft Docs
description: Saiba como configurar o controle do código-fonte no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: f3d443eed43cc1e131cd3dc47407ce2dfb023ce8
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326392"
---
# <a name="source-control-in-azure-data-factory"></a>Controle do código-fonte no Azure Data Factory

A experiência de interface do usuário do Azure Data Factory (UX) tem duas experiências disponíveis para a criação visual:

- Criar diretamente com o serviço de Data Factory
- Crie com Azure Repos a integração do git ou do GitHub

> [!NOTE]
> Somente a criação direta com o serviço de Data Factory tem suporte na nuvem do Azure governamental.

## <a name="author-directly-with-the-data-factory-service"></a>Criar diretamente com o serviço de Data Factory

Ao criar diretamente com o serviço de Data Factory, a única maneira de salvar as alterações é por meio do botão **publicar tudo** . Depois de clicar, todas as alterações feitas são publicadas diretamente no serviço de Data Factory. 

![Modo Publicar](media/author-visually/data-factory-publish.png)

A criação direta com o serviço de Data Factory tem as seguintes limitações:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para as alterações.
- O serviço de Data Factory não está otimizado para colaboração ou controle de versão.

> [!NOTE]
> A criação direta com o serviço de Data Factory está desabilitada no Azure Data Factory UX quando um repositório Git é configurado. As alterações podem ser feitas diretamente no serviço por meio do PowerShell ou de um SDK.

## <a name="author-with-azure-repos-git-integration"></a>Criar com a integração do Git ao Azure Repos

A criação visual com a integração do Git ao Azure Repos dá suporte ao controle do código-fonte e à colaboração para trabalhar nos pipelines de data factory. Você pode associar um data factory a um repositório da organização do Git do Azure Repos para obter controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única organização do Git do Azure Repos pode ter vários repositórios, mas um repositório Git do Azure Repos pode ser associado a apenas um data factory. Se você não tiver uma organização ou um repositório do Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar arquivos de script e de dados em um repositório Git do Azure Repos. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não faz upload automaticamente dos arquivos de dados ou de script armazenados em um repositório Git do Azure Repos para o Armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório Git do Azure Repos com o Azure Data Factory

Você pode configurar um repositório Git do Azure Repos com um data factory usando dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na home page Azure Data Factory, selecione **configurar repositório de código**.

![Configurar um repositório de código do Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX
Na tela de criação do Azure Data Factory UX, selecione o menu suspenso **Data Factory** e, em seguida, selecione **configurar repositório de código**.

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Configurar as definições do repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes configurações de Azure Repos repositório de código:

| Configuração | Descrição | Value |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos.<br/> | Azure DevOps git ou GitHub |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | `<your tenant name>` |
| **Organização do Azure Repos** | O nome da organização do Azure Repos. Localize o nome de organização do Azure Repos em `https://{organization name}.visualstudio.com`. Você pode [entrar na sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **ProjectName** | O nome do projeto do Azure Repos. Localize o nome do projeto do Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | O nome do seu repositório de código do Azure Repos. Os projetos do Azure Repos contêm repositórios Git para gerenciar seu código-fonte à medida que o projeto aumenta. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | `<your Azure Repos code repository name>` |
| **Ramificação de colaboração** | Seu branch de colaboração do Azure Repos que é usado para publicação. Por padrão, seu `master`. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | A pasta raiz em seu branch de colaboração do Azure Repos. | `<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da **Tela de criação** da UX em um repositório Git do Azure Repos. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

> [!NOTE]
> Se você estiver usando o Microsoft Edge e não vir nenhum valor em seu menu suspenso da conta do Azure DevOps, adicione https://*. VisualStudio. com à lista de sites confiáveis.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário do Azure Active Directory diferente

Você pode criar um repositório Git do Azure Repos em um locatário diferente do Azure Active Directory. Para especificar outro locatário do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que está usando.

### <a name="use-your-personal-microsoft-account"></a>Usar sua conta Microsoft pessoal

Para usar uma conta Microsoft pessoal para integração com o Git, você poderá vincular seu Repositório do Azure pessoal ao Active Directory da sua organização.

1. Adicione sua conta Microsoft pessoal ao Active Directory da sua organização como convidado. Para saber mais, veja [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](../active-directory/b2b/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção Azure DevOps, onde você agora vê seu repositório pessoal. Selecione o repositório e conecte-se com o Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração de Git na IU do Data Factory.

Para saber mais sobre como se conectar ao Azure Repos para o Active Directory da sua organização, veja [Conectar sua organização do Azure DevOps ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Criar com a integração do GitHub

A criação visual com a integração do GitHub oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do GitHub para controle do código-fonte, colaboração e controle de versão. Uma única conta do GitHub pode ter vários repositórios, mas um repositório do GitHub pode ser associado somente a um data factory. Se não tiver uma conta ou repositório do GitHub, siga  [estas instruções](https://github.com/join)  para criar seus recursos.

A integração do GitHub com o Data Factory dá suporte ao GitHub público (ou seja, [https://github.com](https://github.com)) e ao GitHub Enterprise. Você pode usar os repositórios GitHub públicos e privados com o Data Factory, desde que você tenha permissão de leitura e de escrita para o repositório no GitHub.

Para configurar um repositório GitHub, você deve ter permissões de administrador para a assinatura do Azure que você está usando.

Para ver uma introdução de nove minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurar um repositório GitHub com Azure Data Factory

Você pode configurar um repositório do GitHub com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na home page Azure Data Factory, selecione **configurar repositório de código**.

![Configurar um repositório de código do Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX

Na tela de criação do Azure Data Factory UX, selecione o menu suspenso **Data Factory** e, em seguida, selecione **configurar repositório de código**.

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Configurações do repositório do GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Configuração** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos. | GitHub |
| **Usar GitHub Enterprise** | Caixa de seleção para selecionar o GitHub Enterprise | não selecionado (padrão) |
| **URL do GitHub Enterprise** | A URL raiz do GitHub Enterprise. Por exemplo: https://github.mydomain.com. Necessário somente se **usar o GitHub Enterprise** estiver selecionado | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | Seu nome de conta do GitHub. Esse nome pode ser encontrado em https: \//github. com/{nome da conta}/{Repository Name}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta. | `<your repository name>` |
| **Ramificação de colaboração** | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, seu mestre. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz em sua ramificação de colaboração GitHub. |`<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se os recursos existentes do data factory devem ser importados da tela de criação do UX em um repositório do GitHub. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Você pode armazenar arquivos de dados e scripts em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não carrega automaticamente os arquivos de dados ou scripts armazenados em um repositório GitHub para o Armazenamento do Azure.

- O GitHub Enterprise com uma versão mais antiga que 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com o Data Factory ferramentas de criação visual funciona apenas na versão disponível do Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Alternar para um repositório Git diferente

Para alternar para um repositório git diferente, clique no ícone de **configurações do repositório git** no canto superior direito da página Visão geral do data Factory. Se você não vir o ícone, limpe o cache do navegador local. Selecione o ícone para remover a associação com o repositório atual.

![Ícone do git](media/author-visually/remove-repo.png)

Depois que o painel configurações do repositório for exibido, selecione **remover git**. Insira seu nome de data factory e clique em **confirmar** para remover o repositório git associado ao seu data Factory.

![Remover a associação com o repositório Git atual](media/author-visually/remove-repo2.png)

Depois de remover a associação com o repositório atual, você pode definir as configurações do git para usar um repositório diferente e, em seguida, importar recursos existentes do Data Factory para o novo repositório. 

## <a name="version-control"></a>Controle de versão

Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando ramificações de recurso

Cada repositório Git do Azure Repos que está associado a um data factory tem um branch de colaboração. (`master` é a ramificação de colaboração padrão). Os usuários também podem criar ramificações de recursos clicando em **+ nova ramificação** na lista suspensa Branch. Depois que o painel novo Branch for exibido, insira o nome do Branch de recursos.

![Criar uma nova ramificação](media/author-visually/new-branch.png)

Quando você estiver pronto para mesclar as alterações do Branch de recursos para sua ramificação de colaboração, clique na lista suspensa Branch e selecione **criar solicitação pull**. Essa ação o levará para o Git do Azure Repos, em que será possível gerar solicitações de pull, realizar revisões de código e mesclar alterações com o branch de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço do Data Factory de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Para configurar o branch de publicação, ou seja, o branch em que os modelos do Resource Manager são salvos, adicione um arquivo `publish_config.json` à pasta raiz no branch de colaboração. O Data Factory lê esse arquivo, procura o campo `publishBranch` e cria um branch (caso ele ainda não exista) com o valor fornecido. Em seguida, ele salva todos os modelos do Resource Manager no local especificado. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando você especifica um novo branch de publicação, o Data Factory não exclui o branch de publicação anterior. Se você quiser remover o branch de publicação anterior, exclua-o manualmente.

> [!NOTE]
> O Data Factory apenas lê o arquivo `publish_config.json` quando ele carrega o factory. Se o factory já estiver carregado no portal, atualize o navegador para que as alterações entrem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de mesclar as alterações para a ramificação de colaboração (`master` é o padrão), clique em **publicar** para publicar manualmente as alterações de código no Branch mestre para o serviço de data Factory.

![Publicar as alterações no serviço do Data Factory](media/author-visually/publish-changes.png)

Um painel lateral será aberto onde você confirmar que a ramificação de publicação e as alterações pendentes estão corretas. Depois de verificar as alterações, clique em **OK** para confirmar a publicação.

![Confirmar o branch de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O branch mestre não é representativo do que é implantado no serviço de Data Factory. O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.

## <a name="advantages-of-git-integration"></a>Vantagens da integração do Git

-   **Controle do código-fonte**. Conforme as cargas de trabalho do data factory se tornam cruciais, você desejaria integrar seu factory ao Git para aproveitar os vários benefícios de controle do código-fonte com o seguinte:
    -   Capacidade de controlar/auditar as alterações.
    -   Capacidade de reverter as alterações que introduziram bugs.
-   **Salvamento parcial**. Conforme você faz muitas alterações em seu factory, perceberá que no modo normal dinâmico, você não pode salvar suas alterações como rascunho, porque não terminou ou não deseja perder as alterações no caso de falha do seu computador. Com a integração do Git, você pode continuar salvando as alterações incrementalmente e publicar o factory somente quando você tiver terminado. O Git atua como um local de preparo para seu trabalho, até que você teste suas alterações de acordo com sua satisfação.
-   **Colaboração e controle**. Se você tiver vários membros da equipe participando do mesmo factory, talvez queira permitir que seus colegas de equipe colaborem entre si por meio de um processo de revisão de código. Você também pode configurar seu factory de tal forma que nem todo colaborador dela tenha permissão para implantar nela. Os membros da equipe apenas podem fazer alterações por meio do Git, mas somente determinadas pessoas da equipe têm permissão para "Publicar" as alterações para seu factory.
-   **Mostrando comparações**. No modo de Git, você consegue ver uma comparação interessante do conteúdo que está prestes a ser publicado no factory. Essa comparação mostra todos os recursos/entidades que foram modificados/adicionados/excluídos desde a última vez em que você publicou no seu factory. Com base nessa comparação, você pode prosseguir com a publicação ou voltar e verificar suas alterações e retornar posteriormente.
-   **Melhor CI/CD**. Se você estiver usando o modo Git, poderá configurar seu pipeline de lançamento para disparar automaticamente assim que houver qualquer alteração feita no factory de desenvolvimento. Você também poderá personalizar as propriedades no factory que estão disponíveis como parâmetros no modelo do Resource Manager. Isso pode ser útil manter somente o conjunto necessário de propriedades como parâmetros e ter todos os demais elementos embutidos no código.
-   **Melhor desempenho**. Uma fábrica média carrega dez vezes mais rápido no modo git do que no modo dinâmico normal, pois os recursos são baixados por meio do git.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração do Git

### <a name="permissions"></a>Permissões

Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar a fábrica. As seguintes configurações de permissões são recomendadas:

*   Todos os membros da equipe devem ter permissões de leitura para o data factory.
*   Somente um conjunto selecionado de pessoas deve ter permissão para publicar na fábrica. Para fazer isso, eles devem ter a função de **colaborador de data Factory** na fábrica. Para obter mais informações sobre permissões, consulte [funções e permissões para Azure data Factory](concepts-roles-permissions.md).
   
é recomendável não permitir check-ins diretos na ramificação de colaboração. Essa restrição pode ajudar a evitar bugs, uma vez que cada check-in passará por um processo de solicitação de pull.

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas de Azure Key Vault

é recomendável usar Azure Key Vault para armazenar quaisquer cadeias de conexão ou senhas para Data Factory serviços vinculados. Por motivos de segurança, não armazenamos nenhuma informação secreta no git, portanto, quaisquer alterações nos serviços vinculados são publicadas imediatamente no serviço de Azure Data Factory.

Usar Key Vault também facilita a integração e a implantação contínua, pois você não precisará fornecer esses segredos durante a implantação do modelo do Resource Manager.

## <a name="troubleshooting-git-integration"></a>Solução de problemas de integração do git

### <a name="stale-publish-branch"></a>Branch de publicação obsoleto

Se a ramificação de publicação estiver fora de sincronia com a ramificação mestre e contiver recursos desatualizados, independentemente de uma publicação recente, tente seguir estas etapas:

1. Remover seu repositório git atual
1. Reconfigure o Git com as mesmas configurações, mas certifique-se de que **importar recursos existentes do data Factory para o repositório** esteja selecionado e escolha **novo Branch**
1. Excluir todos os recursos de sua ramificação de colaboração
1. Criar uma solicitação de pull para mesclar as alterações para a ramificação de colaboração 

## <a name="provide-feedback"></a>Fornecer comentários
Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
* Para implementar a integração e a implantação contínuas, consulte [integração e entrega contínuas (CI/CD) em Azure data Factory](continuous-integration-deployment.md).
