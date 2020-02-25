---
title: Como gerar e transferir chaves protegidas por HSM para o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o Azure Key Vault. Também conhecido como traga sua própria chave (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 9b8f1065660ea8331853f8804e709134fe682ba7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566107"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importar chaves protegidas por HSM para o Key Vault (versão prévia)

> [!NOTE]
> Este recurso está em visualização e disponível somente nas regiões do Azure *leste dos EUA 2 EUAP* e *EUA Central EUAP*. 

Para obter garantia adicional ao usar Azure Key Vault, você pode importar ou gerar uma chave em um módulo de segurança de hardware (HSM); a chave nunca deixará o limite do HSM. Esse cenário geralmente é chamado de *traga sua própria chave* (BYOK). Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Use as informações neste artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o Azure Key Vault.

> [!NOTE]
> Essa funcionalidade não está disponível para o Azure China 21Vianet. 
> 
> Esse método de importação está disponível somente para [HSMs com suporte](#supported-hsms). 

Para obter mais informações e para obter um tutorial para começar a usar o Key Vault (incluindo como criar um cofre de chaves para chaves protegidas por HSM), consulte [o que é Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Visão geral

Aqui está uma visão geral do processo. Etapas específicas a serem concluídas são descritas posteriormente neste artigo.

* No Key Vault, gere uma chave (chamada de chave de *troca de chaves* (Kek)). O KEK deve ser uma chave RSA-HSM que tenha apenas a operação `import` chave. Somente Key Vault SKU Premium dá suporte a chaves RSA-HSM.
* Baixe a chave pública KEK como um arquivo. PEM.
* Transfira a chave pública KEK para um computador offline que esteja conectado a um HSM local.
* No computador offline, use a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um arquivo BYOK. 
* A chave de destino é criptografada com um KEK, que permanece criptografado até ser transferido para o HSM de Key Vault. Somente a versão criptografada da sua chave deixa o HSM local.
* Um KEK que é gerado dentro de um Key Vault HSM não é exportável. Os HSMs impõem a regra de que não existe nenhuma versão clara de um KEK fora de um Key Vault HSM.
* O KEK deve estar no mesmo cofre de chaves em que a chave de destino será importada.
* Quando o arquivo BYOK é carregado para Key Vault, um Key Vault HSM usa a chave privada KEK para descriptografar o material de chave de destino e importá-lo como uma chave HSM. Essa operação ocorre inteiramente dentro de um Key Vault HSM. A chave de destino sempre permanece no limite de proteção do HSM.

## <a name="prerequisites"></a>Prerequisites

A tabela a seguir lista os pré-requisitos para usar o BYOK no Azure Key Vault:

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um cofre de chaves no Azure Key Vault, você precisa de uma assinatura do Azure. [Inscreva-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Um Key Vault SKU Premium para importar chaves protegidas por HSM |Para obter mais informações sobre as camadas de serviço e os recursos do Azure Key Vault, consulte [Key Vault preços](https://azure.microsoft.com/pricing/details/key-vault/). |
| Um HSM da lista de HSMs com suporte e uma ferramenta BYOK e instruções fornecidas pelo seu fornecedor de HSM | Você deve ter permissões para um HSM e um conhecimento básico de como usar seu HSM. Consulte os [HSMs com suporte](#supported-hsms). |
| CLI do Azure versão 2.1.0 ou posterior | Consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSMs com suporte

|Nome do fornecedor do HSM|Modelos HSM com suporte|Mais informações|
|---|---|---|
|Thales|Família SafeNet Luna HSM 7 com firmware versão 7,3 ou posterior| [Ferramenta e documentação do SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> Para importar chaves protegidas por HSM da família nCipher nShield de HSMs, use o [procedimento BYOK herdado](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Tipos de chave com suporte

|Nome da chave|Tipo de chave|Tamanho da chave|Origem|DESCRIÇÃO|
|---|---|---|---|---|
|Chave de troca de chaves (KEK)|RSA| 2\.048 bits<br />3\.072 bits<br />4\.096 bits|Azure Key Vault HSM|Um par de chaves RSA com suporte para HSM gerado em Azure Key Vault|
|Chave de destino|RSA|2\.048 bits<br />3\.072 bits<br />4\.096 bits|HSM do fornecedor|A chave a ser transferida para o Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Gerar e transferir sua chave para o Key Vault HSM

Para gerar e transferir sua chave para um Key Vault HSM:

* [Etapa 1: gerar um KEK](#step-1-generate-a-kek)
* [Etapa 2: baixar a chave pública KEK](#step-2-download-the-kek-public-key)
* [Etapa 3: gerar e preparar sua chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Etapa 4: transferir sua chave para Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Etapa 1: gerar um KEK

Um KEK é uma chave RSA que é gerada em um HSM Key Vault. O KEK é usado para criptografar a chave que você deseja importar (a chave de *destino* ).

O KEK deve ser:
- Uma chave RSA-HSM (2.048-bit; 3.072-bit; ou 4.096-bit)
- gerado no mesmo cofre de chaves em que você pretende importar a chave de destino
- Criado com operações de chave permitidas definidas para `import`

Use o comando [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) para criar um Kek que tenha operações de chave definidas como `import`. Registre o identificador de chave (`kid`) que é retornado do comando a seguir. (Você usará o valor `kid` na [etapa 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Etapa 2: baixar a chave pública KEK

Use o [download da chave AZ keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para baixar a chave pública Kek para um arquivo. PEM. A chave de destino que você importa é criptografada usando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o arquivo KEKforBYOK. PublicKey. pem para o computador offline. Você precisará desse arquivo na próxima etapa.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Etapa 3: gerar e preparar sua chave para transferência

Consulte a documentação do fornecedor do HSM para baixar e instalar a ferramenta BYOK. Siga as instruções do seu fornecedor HSM para gerar uma chave de destino e, em seguida, crie um pacote de transferência de chave (um arquivo BYOK). A ferramenta BYOK usará a `kid` da [etapa 1](#step-1-generate-a-kek) e do arquivo KEKforBYOK. PublicKey. pem que você baixou na [etapa 2](#step-2-download-the-kek-public-key) para gerar uma chave de destino criptografada em um arquivo BYOK.

Transfira o arquivo BYOK para o computador conectado.

> [!NOTE] 
> Não há suporte para a importação de chaves RSA de 1.024 bits. Atualmente, não há suporte para a importação de uma chave de curva elíptica (EC).
> 
> **Problema conhecido**: falha ao importar uma chave de destino RSA 4K de HSMs SafeNet Luna. Quando o problema for resolvido, este artigo será atualizado.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Etapa 4: transferir sua chave para Azure Key Vault

Para concluir a importação de chave, transfira o pacote de transferência de chave (um arquivo BYOK) de seu computador desconectado para o computador conectado à Internet. Use o comando [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o arquivo BYOK no Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem-sucedido, CLI do Azure exibirá as propriedades da chave importada.

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para obter mais informações, consulte [esta comparação de preço e recurso](https://azure.microsoft.com/pricing/details/key-vault/).



