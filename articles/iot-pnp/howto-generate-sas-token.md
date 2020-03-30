---
title: Gerar token de segurança para acessar o repositório IoT Plug and Play Preview | Microsoft Docs
description: Gere um token de assinatura de acesso compartilhado para usar quando acessar um repositório de repositório de plug e reprodução de IoT plug e play de forma programática.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159193"
---
# <a name="generate-sas-token"></a>Gerar token SAS

Este guia de como fazer mostra como gerar programáticamente um token de assinatura de acesso compartilhado (SAS) para usar com as APIs do modelo De plug and Play Preview da IoT.

## <a name="python"></a>Python

O trecho a seguir mostra como gerar um token SAS usando Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

O trecho a seguir mostra como gerar um token SAS usando C\#:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Use o token SAS

Depois de gerar um token SAS, você pode usá-lo para fazer uma solicitação HTTP POST. Por exemplo: 

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Se você der a um cliente um token SAS, o cliente não tem a chave principal do recurso e não pode reverter o hash para obtê-lo. Um token SAS lhe dá controle sobre o que o cliente pode acessar e por quanto tempo. Quando você altera a chave principal da diretiva, todos os tokens SAS criados a partir dela são invalidados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a geração de tokens de segurança para usar para acessar os repositórios do modelo IoT Plug e Play Preview, um próximo passo sugerido é aprender mais no guia de [desenvolvedores de modelagem IoT Plug and Play Preview](concepts-developer-guide.md).
