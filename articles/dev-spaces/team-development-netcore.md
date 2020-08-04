---
title: Desenvolvimento em equipe usando o .NET Core e o Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: Este tutorial mostra como usar o Azure Dev Spaces e o Visual Studio Code para fazer o desenvolvimento em equipe em um aplicativo .NET Core no Serviço de Kubernetes do Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: f83dbea53427d88043537a5039f4071d974a9786
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013602"
---
# <a name="team-development-using-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Desenvolvimento em equipe usando o .NET Core e o Visual Studio Code com o Azure Dev Spaces

Neste tutorial, você aprenderá como uma equipe de desenvolvedores pode colaborar simultaneamente no mesmo cluster Kubernetes usando o Azure Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe
Até agora, você executou o código do aplicativo como se fosse o único desenvolvedor trabalhando no aplicativo. Nesta seção, você aprenderá como o Azure Dev Spaces simplifica o desenvolvimento em equipe:
* Habilite uma equipe de desenvolvedores a trabalhar no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme for necessário.
* Dá suporte a cada desenvolvedor iterando em seu código em isolamento e sem o receio de interromper os outros.
* Testa o código de ponta a ponta, antes da confirmação, sem a necessidade de criar simulações ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios com o desenvolvimento de microsserviços
O aplicativo de exemplo não é muito complexo no momento. Porém, no desenvolvimento do mundo real, os desafios logo surgem à medida que você adiciona mais serviços e a equipe de desenvolvimento cresce. A execução de tudo localmente para o desenvolvimento pode se tornar irrealista.

* Seu computador de desenvolvimento pode não ter recursos suficientes para executar cada serviço de que você precisa de uma vez.
* Alguns serviços podem precisar estar publicamente acessíveis. Por exemplo, um serviço pode precisar ter um ponto de extremidade que responde a um webhook.
* Se quiser executar um subconjunto de serviços, você precisará conhecer a hierarquia de dependências completa entre todos os seus serviços. Pode ser difícil determinar isso, especialmente à medida que o número de serviços aumenta.
* Alguns desenvolvedores recorrem à simulação de muitas de suas dependências de serviço. Essa abordagem pode ajudar, mas o gerenciamento dessas simulações pode afetar rapidamente o custo de desenvolvimento. Além disso, essa abordagem faz com que seu ambiente de desenvolvimento seja muito diferente da produção, o que permite o surgimento de alguns bugs sutis.
* Sendo assim, fazer qualquer tipo de teste de integração se torna difícil. Os testes de integração só podem ocorrer de maneira realista após a confirmação, o que significa que você verá problemas mais tarde no ciclo de desenvolvimento.

    ![Uma imagem que mostra a complexidade do teste de integração ilustrando as relações entre um serviço de aplicativo e as dependências dele.](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar em um espaço de desenvolvimento compartilhado
Com o Azure Dev Spaces, você pode configurar um espaço de desenvolvimento *compartilhado* no Azure. Cada desenvolvedor pode se concentrar exatamente em sua parte do aplicativo e pode desenvolver iterativamente um *código de pré-confirmação* em um espaço que já contém todos os outros serviços e recursos de nuvem dos quais seus cenários dependem. As dependências estão sempre atualizadas, e os desenvolvedores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar em seu próprio espaço
À medida que você desenvolve o código para seu serviço, e antes de estar pronto para fazer check-in nele, em geral, o código não estará em um bom estado. Você ainda o está moldando iterativamente, testando e experimentando soluções. O Azure Dev Spaces apresenta o conceito de um **espaço**, permitindo que você trabalhe em isolamento e sem o receio de interromper os membros de sua equipe.

## <a name="use-dev-spaces-for-team-development"></a>Usar o Azure Dev Spaces para desenvolvimento em equipe
Vamos demonstrar essas ideias com um exemplo concreto usando nosso aplicativo de exemplo *webfrontend* -> *mywebapi*. Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa fazer uma alteração no serviço *mywebapi*, e *somente* desse serviço. O *webfrontend* não precisará ser alterado como parte da atualização de Scott.

_Sem_ usar o Azure Dev Spaces, Scott teria algumas maneiras de desenvolver e testar sua atualização, nenhuma das quais seria ideal:
* Executar TODOS os componentes localmente. Isso requer um computador de desenvolvimento mais avançado com o Docker instalado e, potencialmente, o MiniKube.
* Executar TODOS os componentes em um namespace isolado no cluster Kubernetes. Como *webfrontend* não será alterado, isso é um desperdício de recursos do cluster.
* Executar SOMENTE *mywebapi* e fazer chamadas REST manuais para testar. Isso não testa o fluxo completo de ponta a ponta.
* Adicionar código focado em desenvolvimento a *webfrontend* que permita que o desenvolvedor envie solicitações para uma instância diferente de *mywebapi*. Isso complica o serviço de *webfrontend*.

### <a name="set-up-your-baseline"></a>Configurar a linha de base
Primeiro, precisaremos implantar uma linha de base de nossos serviços. Essa implantação representará a "última boa configuração conhecida", para que você possa comparar facilmente o comportamento do código local e a versão do check-in. Em seguida, criaremos um espaço filho com base nessa linha de base para que possamos testar nossas alterações de *mywebapi* dentro do contexto do aplicativo maior.

1. Clone o [aplicativo de exemplo do Azure Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Faça check-out do branch remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Selecione o espaço _dev_: `azds space select --name dev`. Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<none\>_ .
1. Navegue até o diretório _mywebapi_ e execute: `azds up -d`
1. Navegue até o diretório _webfrontend_ e execute: `azds up -d`
1. Execute `azds list-uris` para ver o ponto de extremidade público de _webfrontend_

> [!TIP]
> As etapas acima configuram manualmente uma linha de base, mas recomendamos que as equipes usem CI/CD para manter a linha de base atualizada automaticamente com o código confirmado.
>
> Confira nosso [guia para configurar CI/CD com o Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama a seguir.
>
> ![Exemplo de diagrama CI/CD](media/common/ci-cd-complex.png)

Neste ponto, sua linha de base deve estar em execução. Execute o comando `azds list-up --all` e você verá uma saída semelhante à seguinte:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A coluna DevSpace mostra que ambos os serviços estão em execução em um espaço chamado _dev_. Qualquer pessoa que abrir a URL pública e navegar para o aplicativo Web invocará o caminho do código do qual você fez check-in e que percorre ambos os serviços. Agora, suponha que você queira continuar desenvolvendo _mywebapi_. Como fazer alterações de código e testá-las e não interromper os outros desenvolvedores que estão usando o ambiente de desenvolvimento? Para fazer isso, você configurará seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de desenvolvimento
Para executar sua própria versão de _mywebapi_ em um espaço que não seja _dev_, crie seu próprio espaço usando o seguinte comando:

```cmd
azds space select --name scott
```

Quando solicitado, selecione _dev_ como o **espaço de desenvolvimento pai**. Isso significa que nosso novo espaço, _dev/scott_, será derivado do espaço _dev_. Em breve, veremos como isso nos ajudará nos testes.

Mantendo nossa hipótese inicial, usamos o nome _scott_ para o novo espaço para que colegas possam identificar quem está trabalhando nele. Mas ele pode ter o nome que você quiser e pode ter flexibilidade com relação ao significado, como _sprint4_ ou _demonstração_. Seja qual for o caso, _dev_ serve como a linha de base para todos os desenvolvedores que trabalham em uma parte do aplicativo:

![Um diagrama que mostra um espaço de desenvolvimento simples.](media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. A coluna _Selecionado_ indica qual espaço está selecionado no momento (verdadeiro/falso). Em seu caso, o espaço chamado _dev/scott_ foi selecionado automaticamente quando foi criado. Você pode selecionar outro espaço a qualquer momento com o comando `azds space select`.

Vamos ver isso tudo em ação.

### <a name="make-a-code-change"></a>Alterar um código
Vá para a janela do VS Code para `mywebapi` e faça uma edição de código no método `string Get(int id)` em `Controllers/ValuesController.cs`. Por exemplo:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```

### <a name="run-the-service"></a>Executar o serviço

Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço _dev/scott_ recém-selecionado. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Observe uma instância do *mywebapi* agora em execução no espaço _dev/scott_. A versão em execução em _dev_ ainda está em execução, mas não está listada.

Listar as URLs para o espaço atual executando `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Observe que a URL de ponto de acesso público para *webfrontend* é prefixado com *scott.s*. Essa URL é exclusiva para o espaço _dev/scott_. Esse prefixo de URL informa o Controlador de entrada para rotear solicitações para a versão _dev/scott_ de um serviço. Quando uma solicitação com essa URL é manipulada por espaços de desenvolvimento, o Controlador de Entrada primeiro tenta encaminhar a solicitação para o serviço *webfrontend* pelo espaço _dev/scott_. Se isso falhar, a solicitação será encaminhada para o serviço *webfrontend* pelo espaço _dev_ como um fallback. Também note que há uma URL do localhost para acessar o servidor sobre o localhost usando a funcionalidade do Kubernetes *port-forward*. Para obter mais informações sobre URLs e o roteamento em Azure Dev Spaces, consulte [Como o Azure Dev Spaces funciona e é configurado](how-dev-spaces-works.md).

![Roteamento do Espaço](media/common/Space-Routing.png)

Esse recurso interno do Azure Dev Spaces permite que você teste o código em um espaço compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de *mywebapi* com *webfrontend*, abra o navegador na URL de ponto de acesso público de *webfrontend* e acesse a página Sobre. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (com a versão *mywebapi* em execução em _dev_).

Quando tiver um espaço _dev_, que sempre contém suas alterações mais recentes, e supondo que seu aplicativo tenha sido projetado para aproveitar o roteamento baseado em espaço do Dev Space, conforme descrito nesta seção do tutorial, deverá ser fácil ver como o Azure Dev Spaces podem ajudar bastante com o teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos_ os serviços em seu espaço privado, você poderá criar um espaço privado derivado de _dev_ e apenas "aumentar" os serviços em que de fato está trabalhando. A infraestrutura de roteamento do Azure Dev Spaces cuidará do restante utilizando tantos serviços de seu espaço privado quantos puder encontrar, e usando como padrão a versão mais recente em execução no espaço _dev_. E ainda melhor, _vários_ desenvolvedores podem desenvolver ativamente serviços diferentes ao mesmo tempo em seu próprio espaço sem interromper uns aos outros.

### <a name="well-done"></a>Muito bem!
Você concluiu o guia de introdução! Você aprendeu a:

> [!div class="checklist"]
> * Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
> * Desenvolva iterativamente o código em contêineres.
> * Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolva e teste o código produtivamente em um ambiente de equipe.
> * Estabelecer uma linha de base de funcionalidade usando o Azure Dev Spaces para testar facilmente alterações isoladas dentro do contexto de um aplicativo de microsserviço maior

Agora que você explorou o Azure Dev Spaces, [compartilhe seu espaço de desenvolvimento com um membro da equipe](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil começar a colaborar.

## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
