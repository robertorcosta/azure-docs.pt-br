---
title: Como criar e assinar uma política do Atestado do Azure
description: Uma explicação de como criar e assinar uma política de atestado.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006812"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Como criar e assinar uma política de atestado

A política de atestado é um arquivo carregado no Atestado do Microsoft Azure. O Atestado do Azure oferece a flexibilidade para carregar uma política em um formato de política específico de atestado. Como alternativa, uma versão codificada da política, na Assinatura da Web JSON, também pode ser carregada. O administrador da política é responsável por escrever a política de atestado. Na maioria dos cenários de atestado, a terceira parte confiável funciona como o administrador da política. O cliente que faz a chamada de atestado envias a evidência de atestado, que o serviço analisa e converte em declarações de entrada (conjunto de propriedades, valor). Em seguida, o serviço processa as declarações, com base no que está definido na política, e retorna o resultado calculado.

A política contém regras que determinam os critérios de autorização, as propriedades e o conteúdo do token de atestado. Um exemplo de arquivo de política tem a seguinte aparência:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Um arquivo de política tem três segmentos, conforme visto acima:

- **version**:  a versão é o número de versão da gramática seguida. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Atualmente, a única versão compatível é 1.0.

- **authorizationrules**: uma coleção de regras de declaração que será verificada primeiro para determinar se o Atestado do Azure deve prosseguir para **issuancerules**. As regras de declaração se aplicam na ordem em que são definidas.

- **issuancerules**: uma coleção de regras de declaração que será avaliada para adicionar outras informações ao resultado do atestado, conforme definido na política. As regras de declaração se aplicam na ordem em que são definidas e também são opcionais.

Confira [Declaração e regras de declaração](claim-rule-grammar.md) para obter mais informações.
   
## <a name="drafting-the-policy-file"></a>Como criar a versão preliminar do arquivo de política

1. Crie um arquivo.
1. Adicione a versão ao arquivo.
1. Adicione seções a **authorizationrules** e **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  As regras de autorização contêm a ação deny() sem nenhuma condição, a fim de garantir que nenhuma regra de emissão seja processada. Como alternativa, a regra de autorização também pode conter a ação permit(), a fim de permitir o processamento das regras de emissão.
  
4. Adicionar regras de declaração às regras de autorização

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Se o conjunto de declarações de entrada contiver uma declaração correspondente ao tipo, ao valor e ao emissor, a ação permit() instruirá o mecanismo de política a processar as **issuancerules**.
  
5. Adicione regras de declaração a **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  O conjunto de declarações de saída conterá uma declaração com:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Políticas complexas podem ser criadas de maneira semelhante. Para obter mais informações, confira [Exemplos de política de atestado](policy-examples.md).
  
6. Salve o arquivo.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Como criar o arquivo de política no formato de Assinatura da Web JSON

Depois de criar um arquivo de política, para carregar uma política no formato JWS, siga as etapas abaixo.

1. Gere o JWS, RFC 7515 com a política (codificada em UTF-8) como o conteúdo
     - O identificador de conteúdo da política codificada em Base64Url deve ser "AttestationPolicy".
     
     JWT de exemplo:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (opcional) assine a política. O Atestado do Azure dá suporte aos seguintes algoritmos:
     - **Nenhum**: não assine o conteúdo da política.
     - **RS256**: algoritmo com suporte para assinar o conteúdo da política

3. Carregue o JWS e valide a política.
     - Se o arquivo de política estiver livre de erros de sintaxe, o arquivo de política será aceito pelo serviço.
     - Se o arquivo de política contiver erros de sintaxe, o arquivo de política será rejeitado pelo serviço.

## <a name="signing-the-policy"></a>Como assinar a política

Veja abaixo um exemplo de script Python de como executar a operação de assinatura de política

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Próximas etapas
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
- [Atestar um enclave do SGX usando exemplos de código](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
