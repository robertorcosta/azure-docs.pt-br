---
title: Como gerar e transferir chaves protegidas por HSM para o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM para usar com o Azure Key Vault. Também conhecido como trazer sua própria chave (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 77568928e50fb4398aa786dbbd4a44b9277a8d5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429701"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importar chaves protegidas por HSM para o Key Vault (versão prévia)

> [!NOTE]
> Este recurso está em pré-visualização e está disponível apenas nas regiões azure *Leste dos EUA 2 EUAP* e Central US *EUAP*. 

Para obter maior garantia quando você usa o Azure Key Vault, você pode importar ou gerar uma chave em um módulo de segurança de hardware (HSM); a chave nunca sairá do limite HSM. Este cenário muitas vezes é referido como *trazer sua própria chave* (BYOK). O Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 Nível 2 validado) para proteger suas chaves.

Use as informações deste artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM para usar com o Azure Key Vault.

> [!NOTE]
> Esta funcionalidade não está disponível para o Azure China 21Vianet. 
> 
> Este método de importação está disponível apenas para [HSMs suportados.](#supported-hsms) 

Para obter mais informações e para que um tutorial comece a usar o Key Vault (incluindo como criar um cofre de chaves para chaves protegidas pelo HSM), consulte [O que é o Azure Key Vault?](../general/overview.md)

## <a name="overview"></a>Visão geral

Eis uma visão geral do processo. Etapas específicas a serem concluídas são descritas posteriormente no artigo.

* No Key Vault, gere uma chave (chamada de *Chave de Troca de Chaves* (KEK)). O KEK deve ser uma chave RSA-HSM que tem apenas a `import` operação chave. Apenas o Key Vault Premium SKU suporta chaves RSA-HSM.
* Baixe a chave pública KEK como um arquivo .pem.
* Transfira a chave pública KEK para um computador offline conectado a um HSM no local.
* No computador offline, use a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um arquivo BYOK. 
* A chave de destino é criptografada com um KEK, que permanece criptografado até ser transferido para o Key Vault HSM. Apenas a versão criptografada da sua chave deixa o HSM no local.
* Um KEK gerado dentro de um Key Vault HSM não pode ser exportado. Os HSMs aplicam a regra de que não existe uma versão clara de um KEK fora de um Key Vault HSM.
* O KEK deve estar no mesmo cofre de chaves onde a chave de destino será importada.
* Quando o arquivo BYOK é carregado no Key Vault, um Key Vault HSM usa a chave privada KEK para descriptografar o material-chave de destino e importá-lo como uma chave HSM. Esta operação acontece inteiramente dentro de um Key Vault HSM. A chave de destino permanece sempre no limite de proteção HSM.

## <a name="prerequisites"></a>Pré-requisitos

A tabela a seguir lista pré-requisitos para usar BYOK no Azure Key Vault:

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um cofre chave no Azure Key Vault, você precisa de uma assinatura do Azure. [Inscreva-se para um teste gratuito](https://azure.microsoft.com/pricing/free-trial/). |
| Um SKU Premium do Cofre chave para importar chaves protegidas pelo HSM |Para obter mais informações sobre os níveis e recursos de serviço no Azure Key Vault, consulte [Key Vault Pricing](https://azure.microsoft.com/pricing/details/key-vault/). |
| Um HSM da lista de HSMs suportado e uma ferramenta BYOK e instruções fornecidas pelo seu fornecedor HSM | Você deve ter permissões para um HSM e conhecimento básico de como usar seu HSM. Consulte [HSMs suportados](#supported-hsms). |
| Azure CLI versão 2.1.0 ou posterior | Consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSMs suportados

|Nome do fornecedor|Tipo de fornecedor|Modelos HSM suportados|Mais informações|
|---|---|---|---|
|Thales|Fabricante|Família SafeNet Luna HSM 7 com firmware versão 7.3 ou posterior| [Ferramenta e documentação SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortita|HSM como serviço|Serviço de gerenciamento de chaves auto-defendssem (SDKMS)|[Exportando chaves SDKMS para provedores de nuvem para BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Para importar chaves protegidas pelo HSM da família nCipher nShield dos HSMs, use o [procedimento BYOK legado](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Tipos-chave suportados

|Nome da chave|Tipo de chave|Tamanho da chave|Origem|Descrição|
|---|---|---|---|---|
|Chave de troca de chaves (KEK)|RSA| 2.048 bits<br />3.072 bits<br />4.096 bits|Azure Key Vault HSM|Um par de chaves RSA apoiado pelo HSM gerado no Azure Key Vault|
|Chave alvo|RSA|2.048 bits<br />3.072 bits<br />4.096 bits|Fornecedor HSM|A chave a ser transferida para o Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Gerar e transferir sua chave para o Key Vault HSM

Para gerar e transferir sua chave para um Key Vault HSM:

* [Passo 1: Gerar um KEK](#step-1-generate-a-kek)
* [Passo 2: Baixe a chave pública KEK](#step-2-download-the-kek-public-key)
* [Passo 3: Gere e prepare sua chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passo 4: Transfira sua chave para o Cofre chave do Azure](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passo 1: Gerar um KEK

Um KEK é uma chave RSA que é gerada em um Key Vault HSM. O KEK é usado para criptografar a chave que você deseja importar (a chave *de destino).*

O KEK deve ser:
- Uma chave RSA-HSM (2.048 bits; 3.072 bits; ou 4.096 bits)
- Gerado no mesmo cofre de chaves onde você pretende importar a chave de destino
- Criado com operações-chave permitidas definidas para`import`

> [!NOTE]
> O KEK deve ter "importação" como a única operação-chave permitida. A 'importação' é mutuamente exclusiva com todas as outras operações-chave.

Use o comando [az keyvault create para](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) criar um `import`KEK que tenha as operações-chave definidas para . Grave o identificador`kid`de chave () que é retornado do comando seguinte. (Você usará `kid` o valor na [Etapa 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passo 2: Baixe a chave pública KEK

Use [o download da chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para baixar a chave pública KEK para um arquivo .pem. A chave de destino que você importa é criptografada usando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o arquivo KEKforBYOK.publickey.pem para o seu computador offline. Você vai precisar deste arquivo na próxima etapa.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passo 3: Gere e prepare sua chave para transferência

Consulte a documentação do seu fornecedor HSM para baixar e instalar a ferramenta BYOK. Siga as instruções do fornecedor HSM para gerar uma chave de destino e, em seguida, crie um pacote de transferência de chave (um arquivo BYOK). A ferramenta BYOK `kid` usará o arquivo PASSO [1](#step-1-generate-a-kek) e KEKforBYOK.publickey.pem que você baixou na [Etapa 2](#step-2-download-the-kek-public-key) para gerar uma chave de destino criptografada em um arquivo BYOK.

Transfira o arquivo BYOK para o computador conectado.

> [!NOTE] 
> A importação de chaves RSA de 1.024 bits não é suportada. Atualmente, não é suportado importar uma chave de Curva Elíptica (CE).
> 
> **Problema conhecido**: Importar uma chave de destino RSA 4K do SafeNet Luna HSMs só é suportado com firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passo 4: Transfira sua chave para o Cofre chave do Azure

Para concluir a importação da chave, transfira o pacote de transferência de chaves (um arquivo BYOK) do seu computador desconectado para o computador conectado à Internet. Use o comando [de importação de chave do cofre de chave az](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o arquivo BYOK para o Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem-sucedido, o Azure CLI exibirá as propriedades da chave importada.

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para obter mais informações, consulte [este preço e comparação de recursos](https://azure.microsoft.com/pricing/details/key-vault/).



