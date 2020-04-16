---
title: Controle do código-fonte
description: Saiba como configurar o controle de origem na Fábrica de Dados do Azure
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 2108f2f9098fe6da8ee4666b30605bed14164484
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414714"
---
# <a name="source-control-in-azure-data-factory"></a>Controle de origem na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A experiência de interface de usuário do Azure Data Factory (UX) tem duas experiências disponíveis para autoria visual:

- Criar diretamente com o serviço de Data Factory
- Autor com integração Azure Repos Git ou GitHub

> [!NOTE]
> Somente a autoria diretamente com o serviço Data Factory é suportada na Nuvem do Governo Do Azure.

## <a name="author-directly-with-the-data-factory-service"></a>Criar diretamente com o serviço de Data Factory

Ao criar diretamente com o serviço Data Factory, a única maneira de salvar alterações é através do botão **Publicar tudo.** Uma vez clicado, todas as alterações feitas são publicadas diretamente no serviço Data Factory. 

![Modo Publicar](media/author-visually/data-factory-publish.png)

A autoria diretamente com o serviço Data Factory tem as seguintes limitações:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para as alterações.
- O serviço de Data Factory não está otimizado para colaboração ou controle de versão.

> [!NOTE]
> A autoria diretamente com o serviço Data Factory é desativada no UX da Fábrica de Dados do Azure quando um repositório Do Git é configurado. As alterações podem ser feitas diretamente no serviço via PowerShell ou um SDK.

## <a name="author-with-azure-repos-git-integration"></a>Criar com a integração do Git ao Azure Repos

A criação visual com a integração do Git ao Azure Repos dá suporte ao controle do código-fonte e à colaboração para trabalhar nos pipelines de data factory. Você pode associar um data factory a um repositório da organização do Git do Azure Repos para obter controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única organização do Git do Azure Repos pode ter vários repositórios, mas um repositório Git do Azure Repos pode ser associado a apenas um data factory. Se você não tiver uma organização ou um repositório do Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar arquivos de script e de dados em um repositório Git do Azure Repos. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não faz upload automaticamente dos arquivos de dados ou de script armazenados em um repositório Git do Azure Repos para o Armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório Git do Azure Repos com o Azure Data Factory

Você pode configurar um repositório Git do Azure Repos com um data factory usando dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Página inicial da Fábrica de Dados do Azure

Na página inicial da Fábrica de Dados do Azure, **selecione Configurar repositório de código**.

![Configurar um repositório de código do Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX
Na tela de autoria do Azure Data Factory UX, selecione o menu suspenso da Fábrica de **Dados** e selecione **'Configurar repositório de código ''Configuração'.**

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração de configurações do repositório.

![Configurar as definições do repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes configurações do repositório do Azure Repos:

| Configuração | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos.<br/> | Azure DevOps Git ou GitHub |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | `<your tenant name>` |
| **Organização do Azure Repos** | O nome da organização do Azure Repos. Localize o nome de organização do Azure Repos em `https://{organization name}.visualstudio.com`. Você pode [entrar na sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **Projectname** | O nome do projeto do Azure Repos. Localize o nome do projeto do Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome do repositório** | O nome do seu repositório de código do Azure Repos. Os projetos do Azure Repos contêm repositórios Git para gerenciar seu código-fonte à medida que o projeto aumenta. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | `<your Azure Repos code repository name>` |
| **Ramificação de colaboração** | Seu branch de colaboração do Azure Repos que é usado para publicação. Por padrão, é. `master` Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | A pasta raiz em seu branch de colaboração do Azure Repos. | `<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da **Tela de criação** da UX em um repositório Git do Azure Repos. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

> [!NOTE]
> Se você estiver usando o Microsoft Edge e não ver nenhum valor na lista de itens do Azure DevOps Account, adicione https://*.visualstudio.com à lista de sites confiáveis.

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

A criação visual com a integração do GitHub oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do GitHub para controle do código-fonte, colaboração e controle de versão. Uma única conta do GitHub pode ter vários repositórios, mas um repositório do GitHub pode ser associado somente a um data factory. Se você não tiver uma conta ou repositório do GitHub, siga [estas instruções](https://github.com/join) para criar seus recursos.

A integração do GitHub com a Data Factory [https://github.com](https://github.com)suporta tanto o GitHub (ou seja, ) quanto o GitHub Enterprise. Você pode usar os repositórios GitHub públicos e privados com o Data Factory, desde que você tenha permissão de leitura e de escrita para o repositório no GitHub.

Para configurar um repo do GitHub, você deve ter permissões de administrador para a assinatura do Azure que você está usando.

Para ver uma introdução de nove minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configure um repositório GitHub com a fábrica de dados do Azure

Você pode configurar um repositório do GitHub com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Página inicial da Fábrica de Dados do Azure

Na página inicial da Fábrica de Dados do Azure, **selecione Configurar repositório de código**.

![Configurar um repositório de código do Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação da UX

Na tela de autoria do Azure Data Factory UX, selecione o menu suspenso da Fábrica de **Dados** e selecione **'Configurar repositório de código ''Configuração'.**

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração de configurações do repositório.

![Configurações do repositório do GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Configuração** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos. | GitHub |
| **Usar GitHub Enterprise** | Caixa de seleção para selecionar o GitHub Enterprise | não selecionado (padrão) |
| **URL do GitHub Enterprise** | O URL raiz do GitHub Enterprise (deve ser HTTPS para o servidor GitHub Enterprise local). Por exemplo: https://github.mydomain.com. Exigido apenas se **o Uso do GitHub Enterprise** for selecionado | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | Seu nome de conta do GitHub. Este nome pode ser\/encontrado a partir de https: /github.com/{account name}/{repositório name}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta. | `<your repository name>` |
| **Ramificação de colaboração** | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, é mestre. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz em sua ramificação de colaboração GitHub. |`<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se importa os recursos existentes da fábrica de dados da tela de autoria do UX para um repositório do GitHub. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Você pode armazenar arquivos de dados e scripts em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não carrega automaticamente os arquivos de dados ou scripts armazenados em um repositório GitHub para o Armazenamento do Azure.

- O GitHub Enterprise com uma versão mais antiga que 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com as ferramentas de autoria visual da Fábrica de Dados só funciona na versão geralmente disponível da Fábrica de Dados.

- Um máximo de 1.000 entidades por tipo de recurso (como pipelines e conjuntos de dados) pode ser obtido a partir de uma única filial do GitHub. Se esse limite for atingido, sugere-se dividir seus recursos em fábricas separadas. Azure DevOps Git não tem essa limitação.

## <a name="switch-to-a-different-git-repo"></a>Alternar para um repositório Git diferente

Para mudar para um repo Git diferente, clique no ícone **Configurações do Git Repo** no canto superior direito da página de visão geral da Fábrica de Dados. Se você não puder ver o ícone, limpe o cache do navegador local. Selecione o ícone para remover a associação com o repositório atual.

![Ícone git](media/author-visually/remove-repo.png)

Uma vez que o painel Configurações do repositório seja exibido, **selecione Remover Git**. Digite seu nome de fábrica de dados e clique **em confirmar** para remover o repositório Git associado à sua fábrica de dados.

![Remover a associação com o repositório Git atual](media/author-visually/remove-repo2.png)

Depois de remover a associação com o repo atual, você pode configurar suas configurações do Git para usar um repo diferente e, em seguida, importar os recursos existentes da Fábrica de Dados para o novo repo. 

## <a name="version-control"></a>Controle de versão

Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando ramos de recursos

Cada repositório Git do Azure Repos que está associado a um data factory tem um branch de colaboração. (`master` é a ramificação de colaboração padrão). Os usuários também podem criar ramificações de recursos clicando em **+ Nova filial** na parte de entrada da filial. Uma vez que o novo painel de ramificação apareça, digite o nome do seu ramo de recursos.

![Criar uma nova filial](media/author-visually/new-branch.png)

Quando estiver pronto para mesclar as alterações do seu ramo de recursos para o seu ramo de colaboração, clique na entrada de ramificação e selecione **Criar solicitação de puxar**. Essa ação o levará para o Git do Azure Repos, em que será possível gerar solicitações de pull, realizar revisões de código e mesclar alterações com o branch de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço do Data Factory de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Para configurar o branch de publicação, ou seja, o branch em que os modelos do Resource Manager são salvos, adicione um arquivo `publish_config.json` à pasta raiz no branch de colaboração. O Data Factory lê esse arquivo, procura o campo `publishBranch` e cria um branch (caso ele ainda não exista) com o valor fornecido. Em seguida, ele salva todos os modelos do Resource Manager no local especificado. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando você especifica um novo branch de publicação, o Data Factory não exclui o branch de publicação anterior. Se você quiser remover a filial de publicação anterior, exclua-a manualmente.

> [!NOTE]
> O Data Factory apenas lê o arquivo `publish_config.json` quando ele carrega o factory. Se o factory já estiver carregado no portal, atualize o navegador para que as alterações entrem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de mesclar alterações no`master` ramo de colaboração (é o padrão), clique **em Publicar** manualmente suas alterações de código no ramo mestre para o serviço Data Factory.

![Publicar as alterações no serviço do Data Factory](media/author-visually/publish-changes.png)

Um painel lateral será aberto onde você confirma que o ramo de publicação e as alterações pendentes estão corretas. Depois de verificar suas alterações, clique em **OK** para confirmar a publicação.

![Confirmar o branch de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O branch mestre não é representativo do que é implantado no serviço de Data Factory. O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.

## <a name="advantages-of-git-integration"></a>Vantagens da integração do Git

-   **Controle de Origem**. Conforme as cargas de trabalho do data factory se tornam cruciais, você desejaria integrar seu factory ao Git para aproveitar os vários benefícios de controle do código-fonte com o seguinte:
    -   Capacidade de controlar/auditar as alterações.
    -   Capacidade de reverter as alterações que introduziram bugs.
-   **Salvamento parcial**. Como você faz um monte de mudanças em sua fábrica, você vai perceber que no modo LIVE regular, você não pode salvar suas alterações como rascunho, porque você não está pronto, ou você não quer perder suas alterações no caso de seu computador falhar. Com a integração do Git, você pode continuar salvando as alterações incrementalmente e publicar o factory somente quando você tiver terminado. O Git atua como um local de preparo para seu trabalho, até que você teste suas alterações de acordo com sua satisfação.
-   **Colaboração e controle**. Se você tiver vários membros da equipe participando do mesmo factory, talvez queira permitir que seus colegas de equipe colaborem entre si por meio de um processo de revisão de código. Você também pode configurar seu factory de tal forma que nem todo colaborador dela tenha permissão para implantar nela. Os membros da equipe apenas podem fazer alterações por meio do Git, mas somente determinadas pessoas da equipe têm permissão para "Publicar" as alterações para seu factory.
-   **Mostrando comparações**. No modo Git, você pode ver um bom diferencial da carga que está prestes a ser publicada na fábrica. Essa comparação mostra todos os recursos/entidades que foram modificados/adicionados/excluídos desde a última vez em que você publicou no seu factory. Com base nessa comparação, você pode prosseguir com a publicação ou voltar e verificar suas alterações e retornar posteriormente.
-   **Melhor CI/CD**. Se você estiver usando o modo Git, poderá configurar seu pipeline de lançamento para disparar automaticamente assim que houver qualquer alteração feita no factory de desenvolvimento. Você também poderá personalizar as propriedades no factory que estão disponíveis como parâmetros no modelo do Resource Manager. Isso pode ser útil manter somente o conjunto necessário de propriedades como parâmetros e ter todos os demais elementos embutidos no código.
-   **Melhor desempenho**. Uma fábrica média carrega dez vezes mais rápido no modo Git do que no modo LIVE normal, porque os recursos são baixados via Git.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração do Git

### <a name="permissions"></a>Permissões

Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar a fábrica. Recomenda-se as seguintes configurações de permissões:

*   Todos os membros da equipe devem ter permissões de leitura para o data factory.
*   Apenas um conjunto seleto de pessoas deve ser autorizado a publicar na fábrica. Para isso, eles devem ter o papel **de contribuinte da Fábrica de Dados** no grupo de recursos em que a fábrica está. Para obter mais informações sobre permissões, consulte [Funções e permissões para a Fábrica de Dados Do Azure](concepts-roles-permissions.md).
   
Recomenda-se não permitir check-ins diretos para o ramo de colaboração. Essa restrição pode ajudar a prevenir bugs, pois cada check-in passará por um processo de revisão de solicitação de atração descrito no [Criando ramificações de recursos](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas do Azure Key Vault

Recomenda-se usar o Azure Key Vault para armazenar quaisquer seqüências de conexão ou senhas para serviços vinculados à fábrica de dados. Por razões de segurança, não armazenamos nenhuma informação secreta no Git, então quaisquer alterações nos Serviços Vinculados são publicadas imediatamente para o serviço Azure Data Factory.

O uso do Key Vault também facilita a integração e a implantação contínuas, pois você não precisará fornecer esses segredos durante a implantação do modelo do Gerenciador de recursos.

## <a name="troubleshooting-git-integration"></a>Solução de problemas integração git

### <a name="stale-publish-branch"></a>Ramo de publicação obsoleta

Se o ramo de publicação estiver fora de sincronia com o ramo principal e contiver recursos desatualizados, apesar de uma publicação recente, tente seguir estas etapas:

1. Remova seu repositório Git atual
1. Reconfigure o Git com as mesmas configurações, mas certifique-se de que **os recursos existentes da Fábrica de Dados para o repositório** sejam selecionados e escolha **Nova filial**
1. Crie uma solicitação de puxar para mesclar as alterações no ramo de colaboração 

Abaixo estão alguns exemplos de situações que podem causar um ramo de publicação obsoleto:
- Um usuário tem várias filiais. Em uma filial de recursos, eles excluíram um serviço vinculado que não é associado à AKV (serviços não vinculados à AKV são publicados imediatamente, independentemente de estarem no Git ou não) e nunca fundiram o ramo de recursos no brnach de colaboração.
- Um usuário modificou a fábrica de dados usando o SDK ou PowerShell
- Um usuário transferiu todos os recursos para uma nova filial e tentou publicar pela primeira vez. Os serviços vinculados devem ser criados manualmente ao importar recursos.
- Um usuário carrega manualmente um serviço não vinculado a AKV ou um JSON de Execução de Integração. Eles fazem referência a esse recurso a partir de outro recurso, como um conjunto de dados, serviço vinculado ou pipeline. Um serviço não vinculado à AKV criado através do UX é publicado imediatamente porque as credenciais precisam ser criptografadas. Se você carregar um conjunto de dados fazendo referência a esse serviço vinculado e tentar publicar, o UX permitirá isso porque ele existe no ambiente git. Ele será rejeitado no momento da publicação, uma vez que não existe no serviço de fábrica de dados.

## <a name="provide-feedback"></a>Fornecer comentários
Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
* Para implementar a integração e a implantação contínuas, consulte [Integração contínua e entrega (CI/CD) na Fábrica de Dados Do Azure](continuous-integration-deployment.md).
