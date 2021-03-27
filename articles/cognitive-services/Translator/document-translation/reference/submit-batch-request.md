---
title: Solicitação de envio de lote de tradução de documentos
titleSuffix: Azure Cognitive Services
description: Envie uma solicitação de tradução de documento para o serviço de tradução de documentos.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612960"
---
# <a name="document-translation-submit-batch-request"></a>Tradução de documento: Enviar solicitação em lote

Use essa API para enviar uma solicitação de conversão em massa (lote) para o serviço de tradução de documentos. Cada solicitação pode conter vários documentos e deve conter um contêiner de origem e de destino para cada documento.

O filtro de prefixo e sufixo (se fornecido) são usados para filtrar pastas. O prefixo é aplicado ao subcaminho após o nome do contêiner.

Os glossários/memória de tradução podem ser incluídos na solicitação e aplicados pelo serviço quando o documento é traduzido.

Se o Glossário for inválido ou inacessível durante a conversão, um erro será indicado no status do documento. Se um arquivo com o mesmo nome já existir no destino, ele será substituído. O targetUrl para cada idioma de destino deve ser exclusivo.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `POST` para:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="request-body-batch-submission-request"></a>Corpo da solicitação: solicitação de envio em lote

|Nome|Type|Descrição|
|--- |--- |--- |
|entradas|BatchRequest []|BatchRequest listados abaixo. A lista de entrada de documentos ou pastas que contêm documentos. Tipos de mídia: "aplicativo/JSON", "Text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Entradas

Definição para a solicitação de tradução de lote de entrada.

|Nome|Type|Obrigatório|Descrição|
|--- |--- |--- |--- |
|source|SourceInput[]|True|inputs. Source listado abaixo. Fonte dos documentos de entrada.|
|storageType|StorageInputType[]|True|inputs. StorageType listado abaixo. Tipo de armazenamento da cadeia de caracteres de origem dos documentos de entrada.|
|destinos|TargetInput[]|True|inputs. Target listado abaixo. Local do destino para a saída.|

**entrada. origem**

Fonte dos documentos de entrada.

|Nome|Type|Obrigatório|Descrição|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falso|DocumentFilter [] listado abaixo.|
|filtrar. prefixo|string|Falso|Uma cadeia de caracteres de prefixo que diferencia maiúsculas de minúsculas para filtrar documentos no caminho de origem para tradução. Por exemplo, ao usar um URI de blob de armazenamento do Azure, use o prefixo para restringir subpastas para tradução.|
|filtro. sufixo|string|Falso|Uma cadeia de caracteres de sufixo que diferencia maiúsculas de minúsculas para filtrar documentos no caminho de origem para tradução. Isso geralmente é usado para extensões de arquivo.|
|Linguagem|string|Falso|Código de idioma se nenhum for especificado, executaremos a detecção automática no documento.|
|sourceUrl|string|True|Local da pasta/contêiner ou arquivo único com seus documentos.|
|armazenamento|Armazenamento|Falso|Armazenamento listado abaixo.|
|storagename. AzureBlob|string|Falso||

**inputs. StorageType**

Tipo de armazenamento da cadeia de caracteres de origem dos documentos de entrada.

|Nome|Type|
|--- |--- |
|arquivo|string|
|folder|string|

**entradas. Target**

Destino dos documentos traduzidos finalizados.

|Nome|Type|Obrigatório|Descrição|
|--- |--- |--- |--- |
|category|string|Falso|Categoria/sistema personalizado para solicitação de tradução.|
|glossários|Glossário []|Falso|Glossário listado abaixo. Lista de glossário.|
|formato de glossários.|string|Falso|Formato.|
|glossários. glossaryUrl|string|Verdadeiro (se estiver usando glossários)|Local do glossário. Usaremos a extensão de arquivo para extrair a formatação se o parâmetro de formato não for fornecido. Se o par de idiomas de tradução não estiver presente no Glossário, ele não será aplicado.|
|glossários. armazenamento|Armazenamento|Falso|Armazenamento listado acima.|
|targetUrl|string|True|Local da pasta/contêiner com seus documentos.|
|Linguagem|string|True|Código de idioma de destino de duas letras. Consulte a [lista de códigos de idioma](../../language-support.md).|
|armazenamento|Armazenamento do []|Falso|Storagename [] listado acima.|
|version|string|Falso|Versão.|

## <a name="example-request"></a>Solicitação de exemplo

Veja a seguir exemplos de solicitações em lote.

**Traduzindo todos os documentos em um contêiner**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Convertendo todos os documentos em um contêiner que aplica glossários**

Verifique se você criou a URL do glossário & token SAS para o blob/documento específico (não para o contêiner)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Convertendo pasta específica em um contêiner**

Verifique se você especificou o nome da pasta (diferencia maiúsculas de minúsculas) como prefixo no filtro – embora o token SAS ainda seja para o contêiner.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Convertendo documento específico em um contêiner**

* Verifique se você especificou "storagetype": "File"
* Verifique se você criou a URL de origem & token SAS para o blob/documento específico (não para o contêiner)
* Verifique se você especificou o nome de arquivo de destino como parte da URL de destino – embora o token SAS ainda seja para o contêiner.
* Exemplo de solicitação abaixo mostra um único documento sendo traduzido em duas linguagens de destino

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|Descrição|
|--- |--- |
|202|Aceita. Solicitação bem-sucedida e a solicitação de lote são criadas pelo serviço. O Operation-Location de cabeçalho indicará uma URL de status com a ID da operação. HeadersOperation-Location: String|
|400|Solicitação inválida. Solicitação inválida. Verifique os parâmetros de entrada.|
|401|Não autorizado. Verifique suas credenciais.|
|429|A taxa de solicitação é muito alta.|
|500|Erro Interno do Servidor.|
|503|O serviço não está disponível no momento.  Tente novamente mais tarde.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|

## <a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
|code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
|innerError|InnerErrorV2|Novo formato de erro interno, que está em conformidade com as diretrizes de API de serviços cognitivas. Ele contém as propriedades obrigatórias ErrorCode, mensagem e propriedades opcionais destino, detalhes (par chave-valor), erro interno (isso pode ser aninhado).|
|Inner. ErrorCode|string|Obtém a cadeia de caracteres de erro do código.|
|innerError. Message|string|Obtém a mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem-sucedida

As informações a seguir são retornadas em uma resposta bem-sucedida.

Você pode encontrar a ID do trabalho no cabeçalho de resposta do método POST Operation-Location o valor da URL. O último parâmetro da URL é a ID do trabalho da operação (a cadeia de caracteres após "/Operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Exemplo de resposta de erro

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Siga nosso início rápido para saber mais sobre como usar a tradução de documentos e a biblioteca de clientes.

> [!div class="nextstepaction"]
> [Introdução à tradução do documento](../get-started-with-document-translation.md)
