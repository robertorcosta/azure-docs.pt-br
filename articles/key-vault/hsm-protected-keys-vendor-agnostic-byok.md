---
title: Como gerar e transferir chaves protegidas por HSM para o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure. Também conhecido como BYOK ou Traga sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461732"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importar chaves protegidas por HSM para Key Vault (versão prévia)

> [!NOTE]
> Este recurso está em versão prévia e disponível apenas nas regiões **leste dos EUA 2 EUAP** e **EUA Central EUAP** . 

Para obter garantia adicional ao usar Azure Key Vault, você pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Use as informações neste tópico para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre de Chaves do Azure.

> [!NOTE]
> Essa funcionalidade não está disponível para o Azure China 21Vianet. 
> 
> Este método de importação só está disponível para [HSMs com suporte](#supported-hsms). 

Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-overview.md)  Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Visão geral

* Gere uma chave (conhecida como chave de troca de chaves ou KEK) no Key Vault. Deve ser uma chave RSA-HSM com ' importar ' como a única operação de chave. Somente a SKU Premium do Key Vault dá suporte a chaves RSA-HSM.
* Baixar a chave pública de KEK como um arquivo. pem
* Transfira a chave pública KEK para sua estação de trabalho offline conectada ao HSM local.
* Na sua estação de trabalho offline, use a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um arquivo BYOK. 
* A chave de destino é criptografada com um KEK, que permanece criptografado até ser transferido para o Azure Key Vault HSMs. Somente a versão criptografada da sua chave deixa o HSM local.
* O KEK que é gerado dentro do Azure Key Vault HSMs e não é exportável. Os HSMs impõem que não pode haver nenhuma versão clara do KEK fora dos HSMs Key Vaults.
* O KEK deve estar no mesmo cofre de chaves em que a chave de destino será importada.
* Quando o arquivo BYOK é carregado para Key Vault, Key Vault HSMs usam a chave privada KEK para descriptografar o material de chave de destino e importá-lo como uma chave HSM. Essa operação ocorre inteiramente dentro de Key Vault HSMs e a chave de destino sempre permanece no limite de proteção do HSM.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Consulte a tabela a seguir para obter uma lista de pré-requisitos para o Traga a sua própria chave (BYOK) para o Cofre da Chave do Azure.

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um Cofre da Chave do Azure, você precisa de uma assinatura do Azure: [Conecte-se para fazer a avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| Um cofre de chaves (SKU Premium) para importar chaves protegidas por HSM |Para obter mais informações sobre os recursos e as camadas de serviço para o Cofre da Chave do Azure, consulte o site [Preços do Cofre da Chave do Azure](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Um HSM da lista de HSMs com suporte junto com a ferramenta BYOK e as instruções fornecidas pelo seu fornecedor de HSM | Você deve ter acesso a um módulo de segurança de hardware e o conhecimento operacional básico de seus HSMs. Consulte os [HSMs com suporte](#supported-hsms). |
| CLI do Azure versão 2.1.0 ou mais recente | Consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) para obter mais informações.|

## <a name="supported-hsms"></a>HSMs com suporte

|Nome do fornecedor do HSM|Modelos HSM com suporte|Detalhes adicionais|
|---|---|---|
|Thales|Família SafeNet Luna HSM 7 com firmware versão 7,3 ou mais recente| [Ferramenta e documentação do SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Para importar chaves protegidas por HSM da família nCipher nShield de HSMs, [use o procedimento BYOK herdado](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir sua chave para o HSM do Cofre da Chave do Azure

Você usará as seguintes etapas para gerar e transferir sua chave para um Azure Key Vault HSM:

* [Etapa 1: gerar um KEK](#step-1-generate-a-kek)
* [Etapa 2: baixar a chave pública do KEK](#step-2-download-kek-public-key)
* [Etapa 3: gerar e preparar sua chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Etapa 4: transferir sua chave para Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Etapa 1: gerar um KEK

O KEK (chave de troca de chaves) é uma chave RSA gerada no HSM de Key Vault. Essa chave é usada para criptografar a chave a ser importada (chave de destino).

KEK deve ser:
1. uma chave **RSA-HSM** (2048 bits ou 3072 bits ou 4096 bits)
2. gerado no mesmo cofre de chaves em que você pretende importar a chave de destino
3. criado com operações de chave permitidas definidas para **importar**

Use o comando [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) para criar Kek com operações de chave definidas para importar. Anote o identificador de chave ' Kid ' retornado do comando abaixo. Você precisará dela na [etapa 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Etapa 2: baixar a chave pública do KEK

Use o [download da chave AZ keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para baixar a chave pública Kek em um arquivo. PEM. A chave de destino que você importa é criptografada usando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o arquivo KEKforBYOK. PublicKey. pem para sua estação de trabalho offline. Você precisará desse arquivo durante a próxima etapa.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Etapa 3: gerar e preparar sua chave para transferência

Consulte a documentação do fornecedor do HSM para baixar e instalar a ferramenta BYOK. Siga a instrução do seu fornecedor HSM para gerar uma chave de destino e, em seguida, crie um pacote de transferência de chave (um arquivo BYOK). A ferramenta BYOK usará o identificador de chave do arquivo [Step 1](#step-1-generate-a-kek) e KEKforBYOK. PublicKey. pem que você baixou na [etapa 2](#step-2-download-kek-public-key) para gerar uma chave de destino criptografada em um arquivo BYOK.

Transfira o arquivo BYOK para a estação de trabalho conectada.

> [!NOTE] 
> A chave de destino deve ser uma chave RSA de tamanho de 2048 bits ou 3072 bits ou 4096 bits. Não há suporte para a importação de chaves de curva elíptica no momento.
> <br/><strong>Problema conhecido:</strong> Falha ao importar chave de destino RSA 4K de HSMs SafeNet Luna. Quando o problema for resolvido, este documento será atualizado.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Etapa 4: transferir sua chave para Azure Key Vault

Para essa etapa final, transfira o pacote de transferência de chave (um arquivo BYOK) de sua estação de trabalho desconectada para a estação de trabalho conectada à Internet e, em seguida, use o comando [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o arquivo BYOK do Azure Key Vault HSM, para concluir a importação de chave.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem-sucedido, você verá exibindo as propriedades da chave que você acabou de importar.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para obter mais informações, consulte esta [comparação](https://azure.microsoft.com/pricing/details/key-vault/)de preço e recurso.
