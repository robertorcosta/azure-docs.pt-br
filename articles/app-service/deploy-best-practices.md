---
title: Melhores práticas de implantação
description: Conheça os principais mecanismos de implantação no Azure App Service. Encontre recomendações específicas do idioma e outras ressalvas.
keywords: serviço de aplicativo azure, aplicativo web, implantação, implantação, pipelines, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750475"
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

## <a name="language-specific-considerations"></a>Considerações específicas do idioma

### <a name="java"></a>Java

Use o [Zipdeploy/API](deploy-zip.md) do Kudu para implantar aplicativos JAR e para aplicativos [WARdeploy/for](deploy-zip.md#deploy-war-file) WAR. Se você estiver usando Jenkins, você pode usar essas APIs diretamente em sua fase de implantação. Para obter mais informações, consulte [este artigo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nó

Por padrão, kudu executa as etapas`npm install`de compilação para o aplicativo Nó ( ). Se você estiver usando um serviço de compilação como o Azure DevOps, então a compilação Kudu é desnecessária. Para desativar a compilação Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`crie uma configuração de aplicativo, com um valor de `false`.

### <a name="net"></a>.NET 

Por padrão, kudu executa as etapas`dotnet build`de compilação para o seu aplicativo .Net ( ). Se você estiver usando um serviço de compilação como o Azure DevOps, então a compilação Kudu é desnecessária. Para desativar a compilação Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`crie uma configuração de aplicativo, com um valor de `false`.

## <a name="other-deployment-considerations"></a>Outras considerações de implantação

### <a name="use-deployment-slots"></a>Use slots de implantação

Sempre que possível, use [slots de implantação](deploy-staging-slots.md) ao implantar uma nova compilação de produção. Ao usar um nível padrão de plano de serviço de aplicativo ou melhor, você pode implantar seu aplicativo em um ambiente de preparação, validar suas alterações e fazer testes de fumaça. Quando estiver pronto, poderá trocar seus slots de encenação e produção. A operação de swap aquece as instâncias de trabalho necessárias para corresponder à sua escala de produção, eliminando assim o tempo de inatividade. 

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
