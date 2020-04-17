---
title: Perguntas frequentes sobre documentação de análise de código de segurança da Microsoft
description: Este artigo contém uma FAQ sobre a extensão microsoft security code analysis
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
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460215"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes
Tem perguntas? Confira a seguinte FAQ para obter mais informações.

## <a name="general-faq"></a>Faq geral

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Posso instalar a extensão na minha instância visual studio team foundation server em vez de em uma instância Dozure DevOps?

Não. A extensão não está disponível para download e instalação para o Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Tenho que executar a Análise de Código de Segurança do Microsoft com a minha compilação? 

Talvez. Depende do tipo de ferramenta de análise. O código-fonte pode ser a única coisa necessária, ou a saída de compilação pode ser necessária.

Por exemplo, o Credential Scanner (CredScan) analisa arquivos dentro da estrutura da pasta do repositório de código. Por causa dessa análise, você pode executar os Registros de análise de segurança do CredScan e publicar tarefas em uma compilação autônoma para obter resultados.

Para outras ferramentas como o BinSkim que analisam artefatos pós-construção, a compilação é necessária primeiro.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso quebrar minha compilação quando os resultados forem encontrados?

Sim. Você pode introduzir uma quebra de compilação quando qualquer ferramenta relatar um problema ou problema em seu arquivo de log. Basta adicionar a tarefa de compilação pós-análise e selecionar a caixa de seleção para qualquer ferramenta para a qual você deseja quebrar a compilação.

Na ui da tarefa Pós-Análise, você pode optar por quebrar a compilação quando qualquer ferramenta reporta apenas erros ou erros e avisos.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Como os argumentos de linha de comando no Azure DevOps diferem desses argumentos nas ferramentas de desktop autônomas? 

Na maioria das vezes, as tarefas de compilação do Azure DevOps são invólucros diretos em torno dos argumentos de linha de comando das ferramentas de segurança. Você pode passar como argumentos para uma tarefa de compilação qualquer coisa que você normalmente passar para uma ferramenta de linha de comando.

Diferenças perceptíveis:

- As ferramentas são executadas a partir da pasta de origem do agente $(Build.SourcesDirectory) ou de %BUILD_SOURCESDIRECTORY%. Um exemplo é C:\agent\_work\1\s.
- Os caminhos nos argumentos podem ser relativos à raiz do diretório de origem previamente listado. Caminhos também podem ser absolutos. Você obter caminhos absolutos usando variáveis de compilação do Azure DevOps ou executando um agente local com locais de implantação conhecidos de recursos locais.
- As ferramentas fornecem automaticamente um caminho ou pasta de arquivo de saída. Se você fornecer um local de saída para uma tarefa de compilação, esse local será substituído por um caminho para nossa localização conhecida de logs no agente de compilação
- Alguns argumentos adicionais de linha de comando são alterados para algumas ferramentas. Um exemplo é a adição ou remoção de opções que garantem que nenhuma GUI seja lançada.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Posso executar uma tarefa de compilação como o Credential Scanner em vários repositórios em um Azure DevOps Build?

Não. A execução das ferramentas de desenvolvimento seguras em vários repositórios em um único pipeline não é suportada.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>O arquivo de saída que especifiquei não está sendo criado, ou não consigo encontrar o arquivo de saída que especifiquei

As tarefas de compilação filtram alguma entrada do usuário. Para esta pergunta especificamente, eles atualizam a localização do arquivo de saída gerado para ser um local comum no agente de compilação. Para obter mais informações sobre este local, consulte as seguintes perguntas.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Onde estão os arquivos de saída gerados pelas ferramentas salvas? 

As tarefas de compilação adicionam automaticamente caminhos de saída a esse local\_conhecido no agente de compilação: sdt\logs de $(Agent.BuildDirectory). Como padronizamos neste local, todas as equipes que produzem ou consomem registros de análise de código têm acesso à saída.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Posso enfileirar uma compilação para executar essas tarefas em um agente de compilação hospedado? 

Sim. Todas as tarefas e ferramentas na extensão podem ser executadas em um agente de compilação hospedado.

>[!NOTE]
> A tarefa de compilação do Anti-Malware Scanner requer um agente de compilação com o Windows Defender ativado. Hospedado Visual Studio 2017 e mais tarde fornecer tal agente. A tarefa de compilação não será executada no agente hospedado do Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas sobre esses agentes, as assinaturas devem ser sempre menores que três horas.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Posso executar essas tarefas de construção como parte de um pipeline de liberação em vez de um pipeline de construção?

Na maioria dos casos, sim.

No entanto, o Azure DevOps não suporta tarefas em execução em pipelines de liberação quando essas tarefas publicam artefatos. Essa falta de suporte impede que a tarefa Publish Security Analysis Logs seja executado com sucesso em um pipeline de liberação. Em vez disso, a tarefa falha com uma mensagem de erro descritiva.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>De onde as tarefas de compilação baixam as ferramentas?

As tarefas de compilação podem baixar os pacotes NuGet das ferramentas no feed de gerenciamento de [pacotes Do Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). As tarefas de compilação também podem usar o Node Package Manager, que deve ser pré-instalado no agente de compilação. Um exemplo dessa instalação é o comando **npm instalar tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Qual o efeito da instalação da extensão na minha organização Azure DevOps? 

Após sua instalação, as tarefas de construção de segurança fornecidas pela extensão ficam disponíveis para todos os usuários da sua organização. Quando você cria ou edita um Pipeline Azure, essas tarefas estão disponíveis na lista de coleta de tarefas de compilação. Caso contrário, a instalação da extensão na organização Azure DevOps não tem efeito. A instalação não modifica as configurações da conta, as configurações do projeto ou os pipelines.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Instalar a extensão modifica meus azure pipelines existentes? 

Não. A instalação da extensão torna as tarefas de construção de segurança disponíveis para adição aos seus pipelines. Você ainda é obrigado a adicionar ou atualizar definições de compilação, para que as ferramentas possam trabalhar com o seu processo de compilação.

## <a name="task-specific-faq"></a>Perguntas frequentes específicas para tarefas

Perguntas específicas para construir tarefas estão listadas nesta seção.

### <a name="credential-scanner"></a>Scanner de credenciais

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Quais são cenários e exemplos comuns de supressão?

Aqui estão detalhes de dois dos cenários de supressão mais comuns.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Para suprimir todas as ocorrências de um determinado segredo dentro do caminho especificado

A chave hash do segredo do arquivo de saída CredScan é necessária conforme mostrado na amostra a seguir.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> A chave hash é gerada por uma parte do valor correspondente ou conteúdo do arquivo. Qualquer revisão do código-fonte pode alterar a tecla hash e desativar a regra de supressão.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Para suprimir todos os segredos em um arquivo especificado ou para suprimir o arquivo de segredos em si

A expressão do arquivo pode ser um nome de arquivo. Ele também pode ser a parte de nome base de um caminho de arquivo completo ou um nome de arquivo. Não há suporte para caracteres curinga.

Os exemplos a seguir mostram \<como suprimir o arquivo InputPath>\src\JS\lib\angular.js

Exemplos de regras de supressão válidas:

- \<InputPath>\src\JS\lib\angular.js - suprime o arquivo no caminho especificado
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - suprime qualquer arquivo com o mesmo nome

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Todos os segredos futuros adicionados ao arquivo também serão suprimidos automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quais são as diretrizes recomendadas para gerenciar segredos?

Os seguintes recursos ajudam você a gerenciar segredos com segurança e acessar informações confidenciais de dentro de seus aplicativos:

 - [Cofre de Chave do Azure](../../key-vault/index.yml)
 - [Diretório Ativo do Azure (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Identidade de serviço gerenciado do AZure AD (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identidades gerenciadas dos recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identidades gerenciadas no Serviço de Aplicativos Azure e Funções Azure](../../app-service/overview-managed-identity.md)
 - [Biblioteca de autenticação de aplicativos](../../key-vault/general/service-to-service-authentication.md)


Para obter mais informações, consulte o post do blog [Gerenciando Segredos Com Segurança na Nuvem](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso escrever meus próprios pesquisadores personalizados?

O Credential Scanner conta com um conjunto de pesquisadores de conteúdo que são comumente definidos no arquivo buildsearchers.xml. O arquivo contém uma matriz de objetos serializados XML que representam um objeto **ContentSearcher.** O programa é distribuído com um conjunto de pesquisadores bem testados. Mas você pode implementar seus próprios buscadores personalizados também.

Um pesquisador de conteúdo é definido da seguinte forma:

- **Nome**: O nome do pesquisador descritivo a ser usado em arquivos de saída do Credential Scanner. Recomendamos que use a convenção de nomeação de camelos para nomes de pesquisadores.
- **RuleId**: O ID estável opaco do pesquisador:
    - Um pesquisador padrão do Credential Scanner é atribuído a um valor **RuleId** como CSCAN0010, CSCAN0020 ou CSCAN0030. O último dígito é reservado para potencialmente fundir ou dividir grupos de pesquisadores através de expressões regulares (regex).
    - O valor **RuleId** para um pesquisador personalizado deve ter seu próprio namespace. Exemplos\<incluem CSCAN-\>Namespace 0010,\<CSCAN- Namespace\>0020\<e\>CSCAN- Namespace 0030.
    - Um nome de pesquisador totalmente qualificado é a combinação de um valor **RuleId** e um nome de pesquisador. Exemplos incluem CSCAN0010. KeyStoreFiles e CSCAN0020. Certificado base64Encoded.
- **ResourceMatchPattern**: Regex de extensões de arquivo para verificar contra o pesquisador.
- **ContentSearchPatterns**: Uma matriz de strings contendo instruções reex compatíveis. Se nenhum padrão de pesquisa for definido, todos os arquivos correspondentes ao valor **ResourceMatchPattern** serão devolvidos.
- **ContentSearchFilters**: Uma matriz de strings contendo instruções regex para filtrar falsos positivos específicos do pesquisador.
- **Detalhes da**correspondência : Uma mensagem descritiva, instruções de mitigação ou ambas a serem adicionadas para cada correspondência do pesquisador.
- **Recomendação**: O conteúdo de campo de sugestões para uma partida usando o formato de relatório PREfast.
- **Gravidade**: Um inteiro que reflete o nível de gravidade de um problema. O nível de gravidade mais alto tem o valor 1.

  ![XML mostrando a configuração do Scanner credencial](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quais são os erros comuns ao usar a tarefa Roslyn Analyzers?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>O projeto foi restaurado usando uma versão errada do Microsoft.NETCore.App

A mensagem de erro completa:

"Erro: o projeto foi restaurado usando a versão *x.x.x*do Microsoft.NETCore.App, mas com as configurações atuais, a versão *y.y.y.y* seria usada em seu lugar. Para resolver esse problema, verifique se as mesmas configurações são usadas para restauração e para as operações seguintes, como build ou publicação. Normalmente, esse problema pode ocorrer se a propriedade RuntimeIdentifier é definida durante o build ou a publicação, mas não durante a restauração."

Como as tarefas de Roslyn Analyzers são executadas como parte da compilação, a árvore de origem na máquina de construção precisa estar em um estado construível.

Um passo entre a sua construção principal e as etapas da Roslyn Analyzers pode ter colocado a árvore de origem em um estado que impede a construção. Este passo extra é provavelmente **dotnet.exe publicar**. Tente duplicar a etapa que faz uma restauração NuGet pouco antes da etapa Roslyn Analyzers. Este passo duplicado pode colocar a árvore de origem de volta em um estado construível.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe não pode criar uma instância analisador

A mensagem de erro completa:

"'csc.exe' saiu com o código de erro 1 -- Uma instância do\\analisador *AAAA* não pode ser criada a partir de C:*BBBB*.dll : Não foi possível carregar arquivo ou montagem 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou uma de suas dependências. O sistema não pode localizar o arquivo especificado."

Certifique-se de que seu compilador suporta Analisadores Roslyn. Executando o comando **csc.exe /version** deve relatar um valor de versão de 2.6 ou posterior.

Às vezes, um arquivo .csproj pode substituir a instalação do Visual Studio da máquina de construção fazendo referência a um pacote do Microsoft.Net.Compilers. Se você não pretende usar uma versão específica do compilador, remova as referências ao Microsoft.Net.Compilers. Caso contrário, certifique-se de que a versão do pacote referenciado também seja 2.6 ou posterior.

Tente obter o caminho de registro de erro, que está especificado na opção **csc.exe/errorlog.** A opção e o caminho aparecem no registro da tarefa build Roslyn Analyzers. Eles podem parecer algo como **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A versão do compilador C# não é recente o suficiente

Para obter as versões mais recentes do compilador C#, vá para [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Para obter sua versão instalada, execute **csc.exe /version** em um prompt de comando. Certifique-se de fazer referência a um pacote Microsoft.Net.Compilers NuGet que é a versão 2.6 ou posterior.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Os logs MSBuild e VSBuild não são encontrados

A tarefa de compilação Roslyn Analyzers precisa consultar devOps do Azure para o log mSBuild a partir da tarefa de compilação do MSBuild. Se a tarefa do analisador for executada imediatamente após a tarefa MSBuild, o log ainda não estará disponível. Coloque outras tarefas entre a tarefa MSBuild e a tarefa Roslyn Analyzers. Exemplos de outras tarefas incluem binSkim e Anti-Malware Scanner.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de assistência adicional, o Suporte de Análise de Código de Segurança da Microsoft está disponível de segunda a sexta-feira das 9:00 às 17:00 hora padrão do Pacífico.

- Onboarding: Consulte nossa [documentação de Onboarding](security-code-analysis-onboard.md)
  
- Suporte: Envie um e-mail para nossa equipe no [Microsoft Security Code Analysis Support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)