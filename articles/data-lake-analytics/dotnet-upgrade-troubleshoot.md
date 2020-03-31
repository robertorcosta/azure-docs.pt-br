---
title: Como solucionar problemas com as falhas de trabalho do Azure Data Lake Analytics U-SQL por causa da atualização do .NET Framework 4.7.2
description: Solucionar problemas de falhas de trabalho do U-SQL devido à atualização para .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213578"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>O Azure Data Lake Analytics está atualizando para o .NET Framework v4.7.2

O tempo de execução padrão do Azure Data Lake Analytics está atualizando de .NET Framework v4.5.2 para .NET Framework v4.7.2. Essa alteração introduz um pequeno risco de quebrar alterações se o código U-SQL usar conjuntos personalizados e esses conjuntos personalizados usarem bibliotecas .NET.

Esta atualização do .NET Framework 4.5.2 para a versão 4.7.2 significa que o .NET Framework implantado em um tempo de execução U-SQL (o tempo de execução padrão) agora será sempre 4.7.2. Não há uma opção lado a lado para versões .NET Framework.

Depois que essa atualização para o .NET Framework 4.7.2 estiver concluída, o código gerenciado do sistema será executado como a versão 4.7.2, bibliotecas fornecidas pelo usuário, como os conjuntos personalizados U-SQL, serão executadas no modo retrocompatível apropriado para a versão em que o conjunto foi gerado para.

- Se os DLLs de montagem forem gerados para a versão 4.5.2, a estrutura implantada as tratará como bibliotecas 4.5.2, fornecendo (com algumas exceções) semântica 4.5.2.
- Agora você pode usar conjuntos personalizados U-SQL que fazem uso dos recursos da versão 4.7.2, se você direcionar o .NET Framework 4.7.2.

Devido a essa atualização para o .NET Framework 4.7.2, há um potencial para introduzir alterações de quebra em seus trabalhos U-SQL que usam conjuntos personalizados .NET. Sugerimos que você verifique se há problemas de retrocompatibilidade usando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar problemas de retrocompatibilidade

Verifique o potencial de problemas de quebra de compatibilidade com retrocesso executando as verificações de compatibilidade .NET em seu código .NET em seus conjuntos personalizados U-SQL.

> [!Note]
> A ferramenta não detecta alterações reais. ele só identifica as APIs .NET que podem (para determinadas entradas) causar problemas. Se você for notificado de um problema, seu código ainda pode estar bem, no entanto, você deve verificar mais detalhes.

1. Execute o verificador de retrocompatibilidade em suas DLLs .NET ou por
   1. Usando a extensão visual studio em [.NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Baixando e usando a ferramenta autônoma do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). As instruções para executar a ferramenta autônoma estão no [GitHub dotnetapiport quebrando alterações](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Para 4.7.2. compatibilidade, `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar se seu código pode ser impactado por qualquer uma das possíveis incompatibilidades invertidas (alguns exemplos comuns de incompatibilidades estão listados abaixo), você pode verificar ainda mais por
   1. Analisando seu código e identificando se seu código está passando valores para as APIs impactadas
   1. Faça uma verificação de tempo de execução. A implantação em tempo de execução não é feita lado a lado no ADLA. Você pode executar uma verificação de tempo de execução antes da atualização, usando a execução local do VisualStudio com um .NET Framework 4.7.2 local contra um conjunto de dados representativo.
3. Se você realmente for impactado por uma incompatibilidade incompatível, tome as medidas necessárias para corrigi-lo (como corrigir seus dados ou lógica de código).

Na maioria dos casos, você não deve ser impactado pela incompatibilidade invertida.

## <a name="timeline"></a>Linha do tempo

Você pode verificar a implantação do novo runtime aqui [Runtime troubles ,](runtime-troubleshoot.md)e olhando para qualquer trabalho de sucesso anterior.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não conseguir revisar meu código a tempo?

Você pode enviar seu trabalho contra a versão de tempo de execução antigo (que é construída visando 4.5.2), no entanto, devido à falta de recursos lado a lado do .NET Framework, ele ainda será executado apenas no modo de compatibilidade 4.5.2. Você ainda pode encontrar alguns dos problemas de retrocompatibilidade por causa desse comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são os problemas de retrocompatibilidade mais comuns que você pode encontrar

As incompatibilidades mais comuns que o verificador provavelmente identificará são (geramos essa lista executando o verificador em nossos próprios trabalhos internos de ADLA), quais bibliotecas são impactadas (nota: que você pode chamar as bibliotecas apenas indiretamente, portanto, é importante tomar as medidas necessárias #1 verificar se seus trabalhos são impactados), e possíveis ações para remediar. Nota: Em quase todos os casos para nossos próprios trabalhos, os avisos se revelaram falsos positivos devido às naturezas estreitas da maioria das mudanças quebrando.

- A propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída
  - Ao chamar TaskFactory.FromAsync, a implementação da propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída. Isto é, a propriedade deverá retornar true se, e apenas se, a implementação for concluída de modo síncrono. Anteriormente, a propriedade não era verificada.
  - Bibliotecas impactadas: mscorlib, System.Threading.Tasks
  - Ação sugerida: Garantir taskFactory.FromAsync retorna verdadeira corretamente

- DataObject.GetData agora recupera dados como UTF-8
  - Para aplicativos direcionados ao .NET Framework 4 ou que são executados no .NET Framework 4.5.1 ou versões anteriores, o DataObject.GetData recupera dados formatados em HTML como uma cadeia de caracteres ASCII. Como resultado, caracteres não ASCII (caracteres cujos códigos ASCII são maiores que 0x7F) são representados por dois caracteres aleatórios.#N##N#Para aplicativos `DataObject.GetData` que visam o .NET Framework 4.5 ou posterior e executados no .NET Framework 4.5.2, recupera dados formatados por HTML como UTF-8, que representa caracteres maiores que 0x7F corretamente.
  - Bibliotecas Impactadas: Glo
  - Ação sugerida: garantir que os dados recuperados sejam o formato que você deseja

- XmlWriter é gerado com pares alternativos inválidos
  - Em aplicativos direcionados ao NET Framework 4.5.2 ou versões anteriores, escrever um par alternativo inválido usando o tratamento de fallback de exceção nem sempre gera uma exceção. Em aplicativos destinados ao .NET Framework 4.6, tentar escrever um par alternativo inválido gera `ArgumentException`.
  - Bibliotecas impactadas: System.Xml, System.Xml.ReaderWriter
  - Ação sugerida: Certifique-se de que você não está escrevendo um par de substitutos inválido que causará exceção de argumento

- HtmlTextWriter não renderiza o elemento `<br/>` corretamente
  - A partir do .NET Framework 4.6, chamar `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` com um elemento `<BR />` vai inserir corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas impactadas: System.Web
  - Ação Sugerida: Certifique-se de `<BR />` que você está inserindo a quantidade que você espera ver para que nenhum comportamento aleatório seja visto no trabalho de produção

- Chamada para CreateDefaultAuthorizationContext com um argumento nulo foi alterada
  - A implementação de AuthorizationContext retornado por uma chamada a `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com um argumento authorizationPolicies nulo mudou sua implementação no .NET Framework 4.6.
  - Bibliotecas impactadas: System.IdentityModel
  - Ação Sugerida: Certifique-se de que você está lidando com o novo comportamento esperado quando houver política de autorização nula
  
- Agora, RSACng carrega corretamente as chaves RSA de tamanho não padrão
  - Nas versões do .NET Framework anteriores a 4.6.2, os clientes com tamanhos de chave não padrão para certificados RSA não conseguiam acessá-las por meio dos métodos de extensão `GetRSAPublicKey()` e `GetRSAPrivateKey()`. A `CryptographicException` com a mensagem "O tamanho da chave solicitada não é suportado" é lançada. Com o Quadro .NET 4.6.2 este problema foi corrigido. Da mesma `RSA.ImportParameters()` `RSACng.ImportParameters()` forma, e agora trabalhar com `CryptographicException`tamanhos de chave não-padrão sem lançar's.
  - Bibliotecas Impactadas: mscorlib, System.Core
  - Ação sugerida: Certifique-se de que as chaves RSA estão funcionando como esperado

- Verificações de dois-pontos em caminhos estão mais rigorosas
  - No .NET Framework 4.6.2, uma série de alterações foram feitas para dar suporte aos caminhos incompatíveis anteriormente (em termos de comprimento e formato). As verificações de sintaxe do separador de unidades (dois-pontos) correto foram aperfeiçoadas, o que teve como efeito colateral o bloqueio de alguns caminhos de URI em algumas APIs de Caminho selecionadas em que eles costumavam ser aceitos.
  - Bibliotecas impactadas: mscorlib, System.Runtime.Extensions
  - Ação sugerida:

- Chamadas para construtores ClaimsIdentity
  - A partir do .NET Framework 4.6.2, haverá uma alteração na maneira como os construtores `T:System.Security.Claims.ClaimsIdentity` com um parâmetro `T:System.Security.Principal.IIdentity` configuram a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor`. Se o argumento `T:System.Security.Principal.IIdentity` for um objeto `T:System.Security.Claims.ClaimsIdentity` e a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` desse objeto `T:System.Security.Claims.ClaimsIdentity` não for `null`, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` será anexada usando o método `M:System.Security.Claims.ClaimsIdentity.Clone`. No Framework 4.6.1 e nas `P:System.Security.Claims.ClaimsIdentify.Actor` versões anteriores, a propriedade é anexada como uma referência existente. Por `P:System.Security.Claims.ClaimsIdentify.Actor` causa dessa mudança, a partir do Quadro .NET 4.6.2, a propriedade do `T:System.Security.Claims.ClaimsIdentity` novo objeto não é igual à `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade do argumento do `T:System.Security.Principal.IIdentity` construtor. No .NET Framework 4.6.1 e nas versões anteriores, ele é igual.
  - Bibliotecas Impactadas: mscorlib
  - Ação sugerida: ensure que o ClaimsIdentity esteja funcionando como esperado em novo tempo de execução

- Serialização de caracteres de controle com DataContractJsonSerializer agora é compatível com ECMAScript V6 e V8
  - No quadro .NET 4.6.2 e nas versões anteriores, o DataContractJsonSerializer não serializou alguns caracteres de controle especiais, como \b, \f e \t, de uma forma compatível com os padrões ECMAScript V6 e V8. A partir do .NET Framework 4.7, a serialização desses caracteres de controle é compatível com o ECMAScript V6 e V8.
  - Bibliotecas impactadas: System.Runtime.Serialization.Json
  - Ação Sugerida: Garanta o mesmo comportamento com DataContractJsonSerializer
