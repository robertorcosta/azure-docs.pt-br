---
title: Entender os conceitos do repositório de modelos de dispositivo | Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de ti, saiba mais sobre os conceitos básicos do repositório de modelos de dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 95c9b6dee402bc0c2dd2cab8ef3200cfd9213d61
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126817"
---
# <a name="device-model-repository"></a>Repositório de modelos de dispositivo

O DMR (repositório de modelos de dispositivo) permite que os criadores de dispositivos gerenciem e compartilhem modelos de dispositivos IoT Plug and Play. Os modelos de dispositivo são documentos JSON LD definidos usando a [linguagem de modelagem de gêmeos digital (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

O DMR define um padrão para armazenar as interfaces DTDL em uma estrutura de pastas com base no DTMI (identificador de modelo de dispositivo). Você pode localizar uma interface no DMR convertendo o DTMI em um caminho relativo. Por exemplo, o `dtmi:com:example:Thermostat;1` DTMI se traduz em `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repositório de modelo de dispositivo público

A Microsoft hospeda um DMR público com estas características:

- Modelos organizados. A Microsoft revisa e aprova todas as interfaces disponíveis usando um fluxo de trabalho de validação de PR do GitHub aberto.
- Imutabilidade.  Depois de publicado, uma interface não pode ser atualizada.
- Hiperescala. A Microsoft fornece toda a infraestrutura necessária para criar um ponto de extremidade seguro e altamente escalonável.

## <a name="custom-device-model-repository"></a>Repositório de modelos de dispositivo personalizado

Você pode usar o mesmo padrão DMR em qualquer mídia de armazenamento, como sistema de arquivos local ou servidores Web HTTP personalizados, para criar um DMR personalizado. Você pode recuperar modelos do DMR personalizado da mesma maneira que a partir do DMR público, simplesmente alterando a URL base usada para acessar o DMR.

> [!NOTE]
> As ferramentas usadas para validar os modelos no DMR público podem ser reutilizadas em repositórios personalizados.

## <a name="public-models"></a>Modelos públicos

Os modelos de entrelaçamento digital pública armazenados no repositório de modelo estão disponíveis para todos consumirem e integrarem em seus aplicativos. Os modelos públicos permitem que um sistema de eco aberto para criadores de dispositivos e desenvolvedores de soluções compartilhem e reutilizem seus modelos de dispositivos IoT Plug and Play.

Consulte a seção [publicar um modelo](#publish-a-model) para obter instruções sobre como publicar um modelo no repositório de modelos para torná-lo público.

Os usuários podem procurar, Pesquisar e exibir interfaces públicas do [repositório GitHub](https://github.com/Azure/iot-plugandplay-models)oficial.

Todas as interfaces nas `dtmi` pastas também estão disponíveis no ponto de extremidade público [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Resolver modelos

Para acessar essas interfaces programaticamente, você precisa converter um dtmi em um caminho relativo que você pode usar para consultar o ponto de extremidade público. O exemplo de código a seguir mostra como fazer isso:

Para converter um DTMI em um caminho absoluto, usamos a `DtmiToPath` função, com `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Com o caminho resultante e a URL base do repositório, podemos obter a interface:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Publicar um modelo

> [!Important]
> Você deve ter uma conta do GitHub para poder enviar modelos para o DMR público.

1. Bifurcar o repositório GitHub público: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Clone o repositório bifurcado. Opcionalmente, crie uma nova ramificação para manter suas alterações isoladas da `main` ramificação.
1. Adicione as novas interfaces à `dtmi` pasta usando a Convenção pasta/nome de arquivo. Consulte a ferramenta [Adicionar modelo](#add-model) .
1. Valide os modelos localmente usando a seção [scripts para validar alterações](#validate-files) .
1. Confirme as alterações localmente e envie por push para sua bifurcação.
1. Em sua bifurcação, crie uma PR que tenha como alvo a `main` ramificação. Consulte [criando um problema ou documentos de solicitação de pull](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Examine os [requisitos de PR](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

A PR dispara uma série de ações do GitHub que validarão as novas interfaces enviadas e garantirá que sua PR atenda a todas as verificações.

A Microsoft responderá a uma PR com todas as verificações em três dias úteis.

### <a name="add-model"></a>Adicionar modelo

As etapas a seguir mostram como o script de add-model.js ajuda a adicionar uma nova interface. Este script requer Node.js para ser executado:

1. Em um prompt de comando, navegue até o repositório git local
1. Execute `npm install`
1. Execute `npm run add-model <path-to-file-to-add>`

Observe a saída do console para qualquer mensagem de erro.

### <a name="local-validation"></a>Validação local

Você pode executar as mesmas verificações de validação localmente antes de enviar a PR para ajudar a diagnosticar problemas com antecedência.

#### <a name="validate-files"></a>validar-arquivos

`npm run validate-files <file1.json> <file2.json>` verifica se o caminho do arquivo corresponde à pasta e aos nomes de arquivo esperados.

#### <a name="validate-ids"></a>Validate-IDs

`npm run validate-ids <file1.json> <file2.json>` verifica se todas as IDs definidas no documento usam a mesma raiz que a ID principal.

#### <a name="validate-deps"></a>Validate-deps

`npm run validate-deps <file1.json> <file2.json>` verifica se todas as dependências estão disponíveis na `dtmi` pasta.

#### <a name="validate-models"></a>validar-modelos

Você pode executar o [exemplo de validação DTDL](https://github.com/Azure-Samples/DTDL-Validator) para validar seus modelos localmente.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é examinar a [arquitetura de plug and Play de IOT](concepts-architecture.md).
