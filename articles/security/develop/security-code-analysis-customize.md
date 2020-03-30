---
title: Personalize as tarefas de análise de código de segurança da Microsoft
titleSuffix: Azure
description: Este artigo descreve a personalização das tarefas na extensão Microsoft Security Code Analysis
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500000"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configure e personalize as tarefas de compilação

Este artigo descreve detalhadamente as opções de configuração disponíveis em cada uma das tarefas de compilação. O artigo começa com as tarefas para ferramentas de análise de código de segurança. Termina com as tarefas pós-processamento.

## <a name="anti-malware-scanner-task"></a>Tarefa anti-Malware Scanner

>[!NOTE]
> A tarefa de compilação do Anti-Malware Scanner requer um agente de compilação com o Windows Defender ativado. Hospedado Visual Studio 2017 e mais tarde fornecer tal agente. A tarefa de compilação não será executada no agente hospedado do Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas sobre esses agentes, as assinaturas devem ser sempre menores que três horas.

Os detalhes da configuração da tarefa são mostrados na captura de tela e no texto a seguir.

![Configurando a tarefa de compilação do Anti-Malware Scanner](./media/security-tools/5-add-anti-malware-task600.png)

Na caixa de lista **Tipo** da captura de tela, **Basic** é selecionado. Selecione **Personalizado** para fornecer argumentos de linha de comando que personalizem a varredura.

O Windows Defender usa o cliente Windows Update para baixar e instalar assinaturas. Se a atualização de assinatura falhar no seu agente de compilação, o código de erro **HRESULT** provavelmente virá do Windows Update.

Para obter mais informações sobre os erros do Windows Update e sua mitigação, consulte [códigos de erro do Windows Update por componente](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) e o artigo da TechNet Windows Update Agent - Error [Codes](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de YAML anti-malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Antes de executar a tarefa BinSkim, sua compilação deve atender a uma dessas condições:
>  - Sua construção produz artefatos binários a partir de código gerenciado.
>  - Você tem artefatos binários comprometidos que você deseja analisar com binSkim.

Os detalhes da configuração da tarefa são mostrados na captura de tela e na lista a seguir.

![Configurando a tarefa de compilação BinSkim](./media/security-tools/7-binskim-task-details.png)

- Defina a configuração de compilação como Debug para que os arquivos de depuração .pdb sejam produzidos. O BinSkim usa esses arquivos para mapear problemas nos binários de saída de volta ao código-fonte.
- Para evitar pesquisar e criar sua própria linha de comando:
     - Na lista **Tipo,** selecione **Básico**.
     - Na **lista Função,** **selecione Analisar**.
- Em **'Destino'** digite um ou mais especificadores para um padrão de arquivo, diretório ou filtro. Esses especificadores resolvem que um ou mais binários sejam analisados:
    - Vários alvos especificados devem ser separados por um ponto e vírgula (;).
    - Um especificador pode ser um único arquivo ou conter curingas.
    - As especificações do \\diretório devem sempre terminar com *.
    - Exemplos:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Se você selecionar **Linha de Comando** na lista **Tipo,** você precisa executar binskim.exe:
     - Certifique-se de que os primeiros argumentos para binskim.exe são o verbo **analisar** seguido por uma ou mais especificações de caminho. Cada caminho pode ser um caminho completo ou um caminho relativo ao diretório de origem.
     - Vários caminhos de destino devem ser separados por um espaço.
     - Você pode omiti-lo na opção **/o** ou **/output.** O valor de saída é adicionado para você ou substituído.
     - As configurações padrão da linha de comando são mostradas da seguinte forma.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > A trilha \\* é importante se você especificar diretórios para o alvo.

Para obter mais informações sobre os argumentos da linha de comando BinSkim, regras por ID ou códigos de saída, consulte o [Guia do Usuário do BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções binSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Tarefa de scanner de credencial

Os detalhes da configuração da tarefa são mostrados na captura de tela e na lista a seguir.

![Configurando a tarefa de compilação do Scanner de Credencial](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem:

  - **Formato de saída**: Os valores disponíveis incluem **TSV,** **CSV,** **SARIF**e **PREfast**.
  - **Versão da ferramenta**: Recomendamos que você selecione **As últimas**.
  - **Scan Folder**: A pasta do repositório a ser digitalizada.
  - **Tipo de arquivo dos pesquisadores**: As opções para localizar o arquivo de buscadores que é usado para digitalização.
  - **Arquivo de supressões**: Um arquivo [JSON](https://json.org/) pode suprimir problemas no registro de saída. Para obter mais informações sobre cenários de supressão, consulte a seção FAQ deste artigo.
  - **Saída verbose**: Auto-explicativa.
  - **Tamanho do lote**: O número de roscas simultâneas usadas para executar o Scanner de Credencial. O valor padrão é 20. Os valores possíveis variam de 1 a 2.147.483.647.
  - **Match Timeout**: A quantidade de tempo em segundos para gastar tentando uma partida de busca antes de abandonar o cheque.
  - **Varredura do arquivo Leia tamanho do buffer**: O tamanho em bytes do buffer usado durante a leitura do conteúdo. O valor padrão é 524.288.  
  - **Varredura máxima de arquivos Leia Bytes**: O número máximo de bytes a serem lidos de um arquivo durante a análise de conteúdo. O valor padrão é 104.857.600.
  - **Opções de** > controle**Execute esta tarefa**: Especifica quando a tarefa será executada. Selecione **Condições personalizadas** para especificar condições mais complexas.
  - **Versão**: A versão de tarefa de compilação dentro do Azure DevOps. Esta opção não é usada com freqüência.

Para obter informações sobre a configuração YAML para esta tarefa, consulte nossas [opções de YAML do Scanner credencial](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Tarefa de detecção de risco de segurança da Microsoft

> [!NOTE]
> Você deve criar e configurar uma conta com o serviço MSRD (Microsoft Security Risk Detection, detecção de riscos de risco de segurança) da Microsoft antes de usar a tarefa MSRD. Este serviço requer um processo de onboarding separado. Ao contrário da maioria das outras tarefas nesta extensão, essa tarefa requer uma assinatura separada com o MSRD.
>
> Consulte a [Detecção de Risco de Segurança da Microsoft](https://aka.ms/msrddocs) e a [Detecção de Risco de Segurança da Microsoft: como fazer](https://docs.microsoft.com/security-risk-detection/how-to/) as instruções.

Os detalhes para a configuração desta tarefa são mostrados na lista a seguir. Para qualquer elemento de ia, você pode passar o tempo sobre esse elemento para obter ajuda.

   - **Nome final do serviço do Azure DevOps para MSRD**: Um tipo genérico de ponto final de serviço Do Zure DevOps armazena sua URL de instância MSRD a bordo e seu token de acesso à API REST. Se você criou tal ponto final, você pode especificá-lo aqui. Caso contrário, selecione o link **Gerenciar** para criar e configurar um novo ponto final de serviço para esta tarefa MSRD.
   - **ID da conta**: Um GUID que pode ser recuperado da URL da conta MSRD.
   - **URLs para Binaries**: Uma lista delimitada de ponto e vírgula de URLs disponíveis publicamente. A máquina de fuzzing usa esses URLs para baixar os binários.
   - **URLs dos Arquivos de Sementes**: Uma lista delimitada de ponto e vírgula de URLs disponíveis publicamente. A máquina de fuzzing usa esses URLs para baixar as sementes. Especificar esse valor é opcional se os arquivos de sementes forem baixados juntamente com os binários.
   - **Tipo de plataforma DO SISTEMA OPERACIONAL**: A plataforma do sistema operacional (OS) de máquinas que executam o trabalho de fuzzing. Os valores disponíveis são **Windows** e **Linux**.
   - **Windows Edition / Linux Edition**: A edição do Sistema Operacional de máquinas que executam o trabalho de fuzzing. Você pode substituir o valor padrão se suas máquinas tiverem uma edição de SO diferente.
   - **Script de instalação do pacote**: Seu script a ser executado em uma máquina de teste. Este script instala o programa de destino de teste e suas dependências antes que o trabalho de fuzzing seja submetido.
   - **Parâmetros de submissão de emprego:**
       - **Diretório de sementes**: O caminho para o diretório na máquina de fuzzing que contém as sementes.
       - **Extensão de sementes**: A extensão do nome do arquivo das sementes.
       - **Executar o driver de teste**: O caminho para o arquivo executável de destino na máquina de fuzzing.
       - **Arquitetura executável do driver**de teste : A arquitetura do arquivo executável de destino. Os valores disponíveis são **x86** e **amd64**.
       - **Argumentos do driver**de teste : Os argumentos da linha de comando passaram para o arquivo executável de teste. O argumento "%testfile%", incluindo as aspas, é automaticamente substituído pelo caminho completo para o arquivo de destino. Este arquivo é analisado pelo driver de teste e é necessário.
       - **Saídas do processo do driver de teste após a conclusão do teste**: Selecione esta caixa de seleção se o driver de teste for encerrado após a conclusão. Limpe-o se o condutor de teste precisar ser fechado à força.
       - **Duração máxima (em segundos):** Uma estimativa do tempo mais longo razoavelmente esperado que o programa-alvo requer para analisar um arquivo de entrada. Quanto mais precisa a estimativa, mais eficiente é o aplicativo de fuzzing.
       - **Test Driver pode ser executado repetidamente**: Selecione esta caixa de seleção se o driver de teste pode funcionar repetidamente sem depender de um estado global persistente ou compartilhado.
       - **Driver de teste pode ser renomeado**: Selecione esta caixa de seleção se o arquivo executável do driver de teste pode ser renomeado e ainda funcionar corretamente.
       - **O aplicativo Fuzzing é executado como um processo single os:** Selecione esta caixa de seleção se o driver de teste for executado em um único processo de Sistema Operacional. Limpe-o se o driver de teste gerar processos adicionais.

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções yaml de detecção de risco de segurança da Microsoft](yaml-configuration.md#microsoft-security-risk-detection-task)

## <a name="roslyn-analyzers-task"></a>Tarefa roslyn analyzers

> [!NOTE]
> Antes de executar a tarefa Roslyn Analyzers, sua compilação precisa atender a essas condições:
>
> - Sua definição de compilação inclui a tarefa de compilação MSBuild ou VSBuild incorporada para compilar c# ou código Visual Basic. A tarefa dos analisadores conta com a entrada e a saída da tarefa incorporada para executar a compilação MSBuild com analisadores Roslyn ativados.
> - O agente de compilação executando essa tarefa de compilação tem visual studio 2017 versão 15.5 ou posterior instalado, de modo que ele usa compilação versão 2.6 ou posterior.

Os detalhes da configuração da tarefa são mostrados na lista a seguir e nota.

As opções disponíveis incluem:

- **Regra:** Os valores são **necessários para SDL,** **recomendado sdl**ou seu próprio conjunto de regras personalizadas.
- **Versão dos analisadores**: Recomendamos que você selecione **Mais Recente**.
- **Arquivo de supressão de avisos do compilador**: Um arquivo de texto com uma lista de IDs de avisos que são suprimidos.
- **Opções de** > controle**Execute esta tarefa**: Especifica quando a tarefa será executada. Escolha **condições personalizadas** para especificar condições mais complexas.

> [!NOTE]
>
> - Os Analisadores roslyn são integrados com o compilador e podem ser executados apenas como parte da compilação csc.exe. Portanto, esta tarefa requer que o comando compilador que foi executado anteriormente na compilação seja reproduzido ou executado novamente. Este replay ou execução é feito consultando o Visual Studio Team Services (VSTS) para os registros de tarefas de compilação do MSBuild.
>
>   Não há outra maneira de a tarefa obter de forma confiável a linha de comando de compilação do MSBuild a partir da definição de compilação. Consideramos adicionar uma caixa de texto freeform para permitir que os usuários insiram suas linhas de comando. Mas então seria difícil manter essas linhas de comando atualizadas e em sincronia com a compilação principal.
>
>   Compilações personalizadas requerem reproduzir todo o conjunto de comandos, não apenas comandos compiladores. Nestes casos, habilitar Roslyn Analyzers não é trivial ou confiável.
>
> - Os analisadores Roslyn são integrados com o compilador. Para ser invocado, Roslyn Analyzers requer compilação.
>
>   Esta nova tarefa de construção é implementada recompilando projetos C# que já foram construídos. A nova tarefa usa apenas as tarefas de compilação MSBuild e VSBuild na mesma definição de compilação ou construção da tarefa original. No entanto, neste caso, a nova tarefa os utiliza com os Analisadores Roslyn ativados.
>
>   Se a nova tarefa for executada no mesmo agente da tarefa original, a saída da nova tarefa sobregrava a saída da tarefa original na pasta de fontes *s.* Embora a saída de compilação seja a mesma, aconselhamos que você execute o MSBuild, copie a saída para o diretório de encenação de artefatos e execute roslyn Analyzers.

Para obter recursos adicionais para a tarefa Roslyn Analyzers, confira [os Analisadores baseados em Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) no Microsoft Docs.

Você pode encontrar o pacote analisador instalado e usado por essa tarefa de compilação na página NuGet [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de YAML da Roslyn Analyzers](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Tarefa TSLint

Para obter mais informações sobre o TSLint, acesse o [repo TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Como você deve estar ciente, a página inicial do [repo Do TSLint GitHub](https://github.com/palantir/tslint) diz que o TSLint será preterido em algum momento de 2019. A Microsoft está investigando [o ESLint](https://github.com/eslint/eslint) como uma tarefa alternativa.

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de YAML TSLint](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Publicar tarefa de logs de análise de segurança

Os detalhes da configuração da tarefa são mostrados na captura de tela e na lista a seguir.

![Configurando a tarefa de compilação de registros de análise de segurança de publicação](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefato**: Qualquer identificador de seqüência.
- **Tipo de artefato**: Dependendo da sua seleção, você pode publicar logs no seu Servidor Azure DevOps ou em um arquivo compartilhado acessível ao agente de compilação.
- **Ferramentas**: Você pode optar por preservar logs para ferramentas específicas ou selecionar **Todas as Ferramentas** para preservar todos os logs.

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de YAML de registro de segurança de publicação](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Tarefa relatório de segurança

Os detalhes da configuração do Relatório de Segurança são mostrados na captura de tela e na lista a seguir.

![Configurando a tarefa de compilação do Relatório de Segurança](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Relatórios**: Selecione qualquer um dos formatos **pipeline console,** **arquivo TSV**e **arquivo Html.** Um arquivo de relatório é criado para cada formato selecionado.
- **Ferramentas**: Selecione as ferramentas na definição de compilação para as quais você deseja um resumo dos problemas detectados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você vê apenas erros ou vê erros e avisos no relatório de resumo.
- **Opções Avançadas**: Se não houver registros de uma das ferramentas selecionadas, você pode optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.
- **Pasta de registros base**: Você pode personalizar a pasta de logs base onde os logs devem ser encontrados. Mas essa opção normalmente não é usada.

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de Relatório de Segurança YAML](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Tarefa pós-análise

Os detalhes da configuração da tarefa são mostrados na captura de tela e na lista a seguir.

![Configurando a tarefa de compilação pós-análise](./media/security-tools/a-post-analysis600.png)

- **Ferramentas**: Selecione as ferramentas em sua definição de compilação para as quais você deseja injetar condicionalmente uma quebra de compilação. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você deseja quebrar apenas em erros ou em erros e avisos.
- **Relatório**: Você pode escrever opcionalmente os resultados que estão causando a quebra de compilação. Os resultados são escritos na janela do console Azure DevOps e no arquivo de log.
- **Opções Avançadas**: Se não houver registros de uma das ferramentas selecionadas, você pode optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.

Para obter informações sobre a configuração YAML para esta tarefa, verifique nossas [opções de YAML de análise de postagem](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a configuração baseada em YAML, consulte nosso [guia de configuração YAML](yaml-configuration.md).

Se você tiver mais dúvidas sobre a extensão de Análise de Código de Segurança e as ferramentas oferecidas, confira [nossa página de perguntas frequentes](security-code-analysis-faq.md).
