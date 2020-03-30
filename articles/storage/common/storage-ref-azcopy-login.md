---
title: login azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de login azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295398"
---
# <a name="azcopy-login"></a>azcopy login

Faça login no Azure Active Directory para acessar os recursos de armazenamento do Azure.

## <a name="synopsis"></a>Sinopse

Faça login no Azure Active Directory para acessar os recursos de armazenamento do Azure.

Para ser autorizado à sua conta do Azure Storage, você deve atribuir a função **de contribuinte de dados blob de armazenamento** à sua conta de usuário no contexto da conta de armazenamento, grupo de recursos pai ou assinatura pai.

Este comando armazenará informações de login criptografadas para o usuário atual usando os mecanismos incorporados do Sistema Operacional.

Consulte os exemplos para obter mais informações.

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no histórico da linha de comando. Considere limpar variáveis que contenham credenciais do seu histórico de linha de comando. Para evitar que variáveis apareçam em seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Faça login interativamente com o ID de inquilino AAD padrão definido como comum:

```azcopy
azcopy login
```

Faça login interativamente com um ID de inquilino especificado:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Faça login usando a identidade atribuída ao sistema de uma Máquina Virtual (VM):

```azcopy
azcopy login --identity
```

Faça login usando a identidade atribuída pelo usuário de uma VM e um ID do Cliente da identidade do serviço:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Faça login usando a identidade atribuída pelo usuário de uma VM e um ID de objeto da identidade do serviço:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Faça login usando a identidade atribuída pelo usuário de uma VM e um ID de recurso da identidade do serviço:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Faça login como diretor de serviço usando um segredo de cliente. Defina a variável ambiente AZCOPY_SPA_CLIENT_SECRET para o cliente secreto para o principal serviço baseado em segredo auth.

```azcopy
azcopy login --service-principal
```

Faça login como um diretor de serviço usando um certificado e senha. Defina a variável ambiente AZCOPY_SPA_CERT_PASSWORD a senha do certificado para a autorização principal do serviço baseado em CERT.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Certifique-se de tratar /path/to/my/cert como um caminho para um arquivo PEM ou PKCS12. O AzCopy não entra na loja cert do sistema para obter seu certificado.

--caminho de certificado é obrigatório ao fazer o auth principal de serviço baseado em cert.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--aad-endpoint|O ponto final do Diretório Ativo do Azure para usar. O padrão`https://login.microsoftonline.com`( ) é correto para a nuvem pública Azure. Defina este parâmetro ao autenticar em uma nuvem nacional. Consulte [os pontos finais de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Este sinalizador não é necessário para a identidade de serviço gerenciada.|
|--cadeia de id de aplicação|ID do aplicativo de identidade atribuída pelo usuário. Necessário para o serviço principal auth.|
|--seqüência de caminho de certificado|Caminho para certificado de autenticação SPN. Necessário para o auth principal de serviço baseado em certificado.|
|-h, --help|Mostrar conteúdo de ajuda para o comando de login.|
|--identidade|fazer login usando a identidade da máquina virtual, também conhecida como Identidade de serviço Gerenciado (MSI).|
|--identidade-cliente-id string|ID do cliente da identidade atribuída pelo usuário.|
|--identidade-objeto-id string|ID de objeto de identidade atribuída pelo usuário.|
|--identidade-recurso-id string|ID de recursos da identidade atribuída pelo usuário.|
|--serviço-principal|Faça login via SPN (Service Principal Name) usando um certificado ou um segredo. O segredo do cliente ou a senha do certificado devem ser colocados na variável ambiente apropriada. Digite `AzCopy env` para ver nomes e descrições de variáveis de ambiente.|
|--inquilino-id string| o ID de inquilino ativo do diretório azure para usar para o login interativo do dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
