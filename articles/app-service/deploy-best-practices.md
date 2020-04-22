---
title: Melhores práticas de implantação
description: Conheça os principais mecanismos de implantação no Azure App Service. Encontre recomendações específicas do idioma e outras ressalvas.
keywords: serviço de aplicativo azure, aplicativo web, implantação, implantação, pipelines, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770678"
---
# <a name="deployment-best-practices"></a>Práticas recomendadas de implantação

Toda equipe de desenvolvimento tem requisitos únicos que podem dificultar a implementação de um pipeline de implantação eficiente em qualquer serviço de nuvem. Este artigo introduz os três principais componentes da implantação no App Service: fontes de implantação, construção de pipelines e mecanismos de implantação. Este artigo também abrange algumas práticas recomendadas e dicas para pilhas de idiomas específicas.

## <a name="deployment-components"></a>Componentes de implantação

### <a name="deployment-source"></a>Fonte de implantação

Uma fonte de implantação é a localização do código do aplicativo. Para aplicativos de produção, a fonte de implantação geralmente é um repositório hospedado por software de controle de versão, como [GitHub, BitBucket ou Azure Repos](deploy-continuous-deployment.md). Para cenários de desenvolvimento e teste, a fonte de implantação pode ser [um projeto em sua máquina local.](deploy-local-git.md) O App Service também suporta [pastas OneDrive e Dropbox](deploy-content-sync.md) como fontes de implantação. Embora as pastas em nuvem possam facilitar o início do App Service, não é normalmente recomendado usar essa fonte para aplicativos de produção de nível corporativo. 

### <a name="build-pipeline"></a>Pipeline de build

Uma vez que você decida sobre uma fonte de implantação, seu próximo passo é escolher um pipeline de compilação. Um pipeline de compilação lê seu código-fonte a partir da fonte de implantação e executa uma série de etapas (como compilar código, minificar HTML e JavaScript, executar testes e componentes de embalagem) para obter o aplicativo em um estado executável. Os comandos específicos executados pelo pipeline de compilação dependem da sua pilha de idiomas. Essas operações podem ser executadas em um servidor de compilação, como o Azure Pipelines, ou executadas localmente.

### <a name="deployment-mechanism"></a>Mecanismo de implantação

O mecanismo de implantação é a ação usada para colocar seu aplicativo incorporado no diretório */home/site/wwwroot* do seu aplicativo web. O *diretório /wwwroot* é um local de armazenamento montado compartilhado por todas as instâncias do seu aplicativo web. Quando o mecanismo de implantação coloca seu aplicativo neste diretório, suas instâncias recebem uma notificação para sincronizar os novos arquivos. O App Service suporta os seguintes mecanismos de implantação:

- Pontos finais de Kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) é a ferramenta de produtividade de desenvolvedor de código aberto que funciona como um processo separado no Windows App Service, e como um segundo contêiner no Linux App Service. O Kudu lida com implantações contínuas e fornece pontos finais HTTP para implantação, como zipdeploy.
- FTP e WebDeploy: Usando suas credenciais de [usuário ou site,](deploy-configure-credentials.md)você pode carregar arquivos [via FTP](deploy-ftp.md) ou WebDeploy. Esses mecanismos não passam por Kudu.  

Ferramentas de implantação como Azure Pipelines, Jenkins e plugins de editores usam um desses mecanismos de implantação.

## <a name="use-deployment-slots"></a>Use slots de implantação

Sempre que possível, use [slots de implantação](deploy-staging-slots.md) ao implantar uma nova compilação de produção. Ao usar um nível padrão de plano de serviço de aplicativo ou melhor, você pode implantar seu aplicativo em um ambiente de preparação, validar suas alterações e fazer testes de fumaça. Quando estiver pronto, poderá trocar seus slots de encenação e produção. A operação de swap aquece as instâncias de trabalho necessárias para corresponder à sua escala de produção, eliminando assim o tempo de inatividade.

### <a name="continuously-deploy-code"></a>Implantar código continuamente

Se o seu projeto designou ramos para testes, QA e encenação, então cada um desses ramos deve ser continuamente implantado em um slot de preparação. (Isso é conhecido como o [projeto Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Isso permite que seus stakeholders avaliem e testem facilmente o ramo implantado. 

A implantação contínua nunca deve ser habilitada para o seu slot de produção. Em vez disso, seu ramo de produção (muitas vezes mestre) deve ser implantado em um slot de não-produção. Quando estiver pronto para liberar o ramo base, troque-o pelo slot de produção. A troca na produção — em vez de ser implantada na produção — impede o tempo de inatividade e permite que você reverta as alterações trocando novamente. 

![Visual de uso de slot](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Implantar continuamente contêineres

Para contêineres personalizados do Docker ou outros registros de contêineres, implante a imagem em um slot de preparação e troque-o em produção para evitar o tempo de inatividade. A automação é mais complexa do que a implantação de código, porque você deve empurrar a imagem para um registro de contêiner e atualizar a tag de imagem no webapp.

Para cada ramo que você deseja implantar em um slot, configure a automação para fazer o seguinte em cada compromisso com a filial.

1. **Construa e marque a imagem**. Como parte do pipeline de compilação, marque a imagem com o ID de confirmação do git, carimbo de data e hora ou outras informações identificáveis. É melhor não usar a tag padrão "mais recente". Caso contrário, é difícil rastrear qual código está atualmente implantado, o que torna a depuração muito mais difícil.
1. **Empurre a imagem marcada**. Uma vez que a imagem é construída e marcada, o pipeline empurra a imagem para o nosso registro de contêiner. Na próxima etapa, o slot de implantação puxará a imagem marcada do registro do contêiner.
1. **Atualize o slot de implantação com a nova tag de imagem**. Quando esta propriedade for atualizada, o site reiniciará automaticamente e puxará a nova imagem do contêiner.

![Visual de uso de slot](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Há exemplos abaixo para estruturas de automação comuns.

### <a name="use-azure-devops"></a>Use Azure DevOps

O App Service tem [entrega contínua incorporada](deploy-continuous-deployment.md) para contêineres através do Centro de Implantação. Navegue até o seu aplicativo no [portal Azure](https://portal.azure.com/) e selecione **Centro de Implantação** em **Implantação**. Siga as instruções para selecionar seu repositório e ramo. Isso configurará um pipeline de compilação e liberação de DevOps para construir, marcar e implantar automaticamente seu contêiner quando novos compromissos forem empurrados para o ramo selecionado.

### <a name="use-github-actions"></a>Use as ações do GitHub

Você também pode automatizar sua implantação de contêineres [com o GitHub Actions](containers/deploy-container-github-action.md).  O arquivo de fluxo de trabalho abaixo irá construir e marcar o contêiner com o ID de confirmação, empurrá-lo para um registro de contêiner e atualizar o slot do site especificado com a nova tag de imagem.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Use outros provedores de automação

As etapas listadas anteriormente se aplicam a outros utilitários de automação, como CircleCI ou Travis CI. No entanto, você precisa usar o Cli do Azure para atualizar os slots de implantação com novas tags de imagem na etapa final. Para usar o Azure CLI em seu script de automação, gere um Diretor de Serviço usando o seguinte comando.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Em seu script, `az login --service-principal`faça login usando, fornecendo as informações do diretor. Em seguida, `az webapp config container set` você pode usar para definir o nome do contêiner, tag, URL de registro e senha de registro. Abaixo estão alguns links úteis para você construir seu processo de CI de contêiner.

- [Como fazer login no Azure CLI no Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Considerações específicas do idioma

### <a name="java"></a>Java

Use o [Zipdeploy/API](deploy-zip.md) do Kudu para implantar aplicativos JAR e para aplicativos [WARdeploy/for](deploy-zip.md#deploy-war-file) WAR. Se você estiver usando Jenkins, você pode usar essas APIs diretamente em sua fase de implantação. Para obter mais informações, consulte [este artigo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nó

Por padrão, kudu executa as etapas`npm install`de compilação para o aplicativo Nó ( ). Se você estiver usando um serviço de compilação como o Azure DevOps, então a compilação Kudu é desnecessária. Para desativar a compilação Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`crie uma configuração de aplicativo, com um valor de `false`.

### <a name="net"></a>.NET 

Por padrão, kudu executa as etapas`dotnet build`de compilação para o seu aplicativo .NET ( ). Se você estiver usando um serviço de compilação como o Azure DevOps, então a compilação Kudu é desnecessária. Para desativar a compilação Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`crie uma configuração de aplicativo, com um valor de `false`.

## <a name="other-deployment-considerations"></a>Outras considerações de implantação

### <a name="local-cache"></a>Cache Local

O conteúdo do Serviço de Aplicativo do Azure é armazenado no Armazenamento do Microsoft Azure e exibido de forma duradoura como um compartilhamento de conteúdo. No entanto, alguns aplicativos só precisam de uma loja de conteúdo de alto desempenho e leitura que eles podem executar com alta disponibilidade. Esses aplicativos podem se beneficiar do uso do [cache local](overview-local-cache.md). O cache local não é recomendado para sites de gerenciamento de conteúdo, como o WordPress.

Use sempre o cache local em conjunto com [os slots de implantação](deploy-staging-slots.md) para evitar o tempo de inatividade. Consulte [esta seção](overview-local-cache.md#best-practices-for-using-app-service-local-cache) para obter informações sobre como usar esses recursos em conjunto.

### <a name="high-cpu-or-memory"></a>Alta CPU ou memória

Se o seu Plano de Serviço de Aplicativo estiver usando mais de 90% da CPU ou memória disponíveis, a máquina virtual subjacente pode ter problemas para processar sua implantação. Quando isso acontecer, dimensione temporariamente sua contagem de instâncias para executar a implantação. Uma vez concluída a implantação, você pode retornar a contagem de instâncias ao seu valor anterior.

Para obter mais informações sobre as melhores práticas, visite [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para descobrir práticas recomendadas acionáveis específicas do seu recurso.

- Navegue até o seu Web App no [portal Azure](https://portal.azure.com).
- Clique em **Diagnosticar e resolver problemas** na navegação à esquerda, que abre o App Service Diagnostics.
- Escolha o azulejo da página inicial **das Práticas Recomendadas.**
- Clique **em Práticas recomendadas para disponibilidade & desempenho** ou práticas **recomendadas para configuração ideal para** visualizar o estado atual do seu aplicativo em relação a essas práticas recomendadas.

Você também pode usar este link para abrir diretamente `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`o App Service Diagnostics para o seu recurso: .
