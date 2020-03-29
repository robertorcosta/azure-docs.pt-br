---
title: Visão geral da documentação do Microsoft Security Code Analysis
description: Este artigo é uma visão geral da extensão microsoft security code analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851461"
---
# <a name="about-microsoft-security-code-analysis"></a>Sobre a Análise de Código de Segurança da Microsoft

Com a extensão microsoft Security Code Analysis, as equipes podem adicionar análise de código de segurança aos seus pipelines de integração e entrega contínua do Azure DevOps (CI/CD). Essa análise é recomendada pelos especialistas em Ciclo de Vida de [Desenvolvimento Seguro (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) da Microsoft.

Um UX consistente simplifica a segurança escondendo a complexidade das ferramentas em execução. Com a entrega das ferramentas baseadas no NuGet, as equipes não precisam mais gerenciar a instalação ou atualização das ferramentas. Com a linha de comando e as interfaces básicas para criar tarefas, todos os usuários podem ter o máximo de controle sobre as ferramentas que quiserem.

As equipes também podem usar poderosos recursos de pós-processamento, tais como:

- Publicação de registros para retenção.
- Gerando relatórios acionáveis e focados em desenvolvedores.
- Configurando quebras de compilação em testes de regressão.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Por que devo usar o Microsoft Security Code Analysis?

### <a name="security-simplified"></a>Segurança simplificada

Adicionar ferramentas de análise de código de segurança da Microsoft ao seu pipeline Azure DevOps é tão simples quanto adicionar novas tarefas. Personalize as tarefas ou use seu comportamento padrão. As tarefas são executadas como parte do pipeline do Azure DevOps e produzem registros que detalham muitos tipos de resultados.

### <a name="clean-builds"></a>Construções limpas

Depois de abordar os problemas iniciais relatados pelas ferramentas, você pode configurar a extensão para quebrar compilações em novos problemas.A configuração de compilações de integração contínua em cada solicitação de tração é fácil.

### <a name="set-it-and-forget-it"></a>Defina e esqueça.

Por padrão, as tarefas de compilação e as ferramentas permanecem atualizadas. Se houver uma versão atualizada de uma ferramenta, você não precisa baixá-la e instalá-la. A extensão cuida da atualização para você.

### <a name="under-the-hood"></a>Nos bastidores

As tarefas de construção da extensão escondem as complexidades de:
  - Executando ferramentas de análise estática de segurança.
  - Processando os resultados de arquivos de log para criar um relatório de resumo ou quebrar a compilação.

## <a name="microsoft-security-code-analysis-tool-set"></a>Conjunto de ferramentas de análise de código de segurança da Microsoft

A extensão Microsoft Security Code Analysis disponibiliza prontamente as versões mais recentes de importantes ferramentas de análise. A extensão inclui ferramentas gerenciadas pela Microsoft e ferramentas de código aberto.

Essas ferramentas são automaticamente baixadas para o agente hospedado na nuvem depois que você usa a tarefa de compilação correspondente para configurar e executar o pipeline.

Esta seção lista o conjunto de ferramentas que estão disponíveis atualmente na extensão. Cuidado com a adição de mais ferramentas. Além disso, envie-nos suas sugestões de ferramentas que você quer que adicionemos.

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

A tarefa de compilação do Anti-Malware Scanner está agora incluída na extensão Microsoft Security Code Analysis. Essa tarefa deve ser executada em um agente de compilação que já tenha o Windows Defender instalado. Para obter mais informações, consulte o site do [Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim é um scanner leve executável portátil (PE) que valida as configurações do compilador, as configurações do linker e outras características relevantes de segurança de arquivos binários. Esta tarefa de compilação fornece um invólucro de linha de comando em torno do aplicativo de console binskim.exe. BinSkim é uma ferramenta de código aberto. Para obter mais informações, consulte [BinSkim no GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Scanner de credenciais

Senhas e outros segredos armazenados no código-fonte são um problema significativo. O Credential Scanner é uma ferramenta proprietária de análise estática que ajuda a resolver esse problema. A ferramenta detecta credenciais, segredos, certificados e outros conteúdos sensíveis em seu código-fonte e na saída de compilação.

### <a name="microsoft-security-risk-detection"></a>Detecção de risco de segurança da Microsoft

O Microsoft Security Risk Detection (MSRD) é um serviço baseado em nuvem para testes de fuzz. Ele identifica bugs de segurança exploráveis no software. Este serviço requer uma assinatura e ativação separadas. Para obter mais informações, consulte o [MSRD Developer Center](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

Roslyn Analyzers é a ferramenta integrada ao compilador da Microsoft para analisar estáticamente o código C# e visual basic. Para obter mais informações, consulte [analisadores baseados em Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint

TSLint é uma ferramenta de análise estática extensível que verifica o código TypeScript para leitura, manutenção e erros de funcionalidade. É amplamente apoiado por editores modernos e sistemas de construção. Você pode personalizá-lo com suas próprias regras de fiapos, configurações e assuntos. TSLint é uma ferramenta de código aberto. Para obter mais informações, consulte [TSLint no GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Análise e pós-processamento de resultados

A extensão Microsoft Security Code Analysis também tem três tarefas de pós-processamento. Essas tarefas ajudam você a analisar os resultados encontrados pelas tarefas da ferramenta de segurança. Quando adicionados a um pipeline, essas tarefas geralmente seguem todas as outras tarefas de ferramentas.

### <a name="publish-security-analysis-logs"></a>Publicar registros de análise de segurança

A tarefa 'Publish Security Analysis's '''''Registros de análise de segurança' preserva os arquivos de log das ferramentas de segurança executadas durante a compilação. Você pode ler esses registros para investigação e acompanhamento.

Você pode publicar os arquivos de log no Azure Artifacts como um arquivo .zip. Você também pode copiá-los a um compartilhamento de arquivos acessível do seu agente de construção privada.

### <a name="security-report"></a>Relatório de Segurança

A tarefa de compilação do Relatório de Segurança analisa os arquivos de log. Esses arquivos são criados pelas ferramentas de segurança que são executadas durante a compilação. A tarefa de compilação cria então um único arquivo de relatório de resumo. Este arquivo mostra todos os problemas encontrados pelas ferramentas de análise.

Você pode configurar esta tarefa para relatar resultados para ferramentas específicas ou para todas as ferramentas. Você também pode escolher qual nível de problema relatar, como apenas erros ou erros e avisos.

### <a name="post-analysis-build-break"></a>Pós-Análise (quebra de compilação)

Com a tarefa de compilação pós-análise, você pode injetar uma quebra de compilação que propositalmente faz com que uma compilação falhe. Você injeta uma quebra de compilação se uma ou mais ferramentas de análise relatarem problemas no código.

Você pode configurar essa tarefa para quebrar a compilação para problemas encontrados por ferramentas específicas ou todas as ferramentas. Você também pode configurá-lo com base na gravidade dos problemas encontrados, como erros ou avisos.

>[!NOTE]
>Pelo projeto, cada tarefa de construção é bem sucedida se a tarefa terminar com sucesso. Isso é verdade se uma ferramenta encontra problemas ou não, para que a compilação possa ser executada, permitindo que todas as ferramentas sejam executadas.

## <a name="next-steps"></a>Próximas etapas

Para obter instruções sobre como embarcar e instalar a Análise de Código de Segurança da Microsoft, consulte nosso [guia de onboarding e instalação](security-code-analysis-onboard.md).

Para obter mais informações sobre a configuração das tarefas de [compilação,](security-code-analysis-customize.md) consulte nosso guia de configuração ou [guia de configuração YAML](yaml-configuration.md).

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
