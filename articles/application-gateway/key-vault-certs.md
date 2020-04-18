---
title: Término do TLS com certificados do Azure Key Vault
description: Saiba como integrar o Gateway de aplicativos Do Azure com o Key Vault para certificados de servidor que são anexados a ouvintes habilitados para HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617513"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Término do TLS com certificados key vault

[O Azure Key Vault](../key-vault/general/overview.md) é uma loja secreta gerenciada por plataformas que você pode usar para proteger segredos, chaves e certificados TLS/SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que são anexados a ouvintes habilitados para HTTPS. Este suporte é limitado ao v2 SKU do Application Gateway.

A integração do Key Vault oferece dois modelos para término do TLS:

- Você pode fornecer explicitamente certificados TLS/SSL anexados ao ouvinte. Este modelo é a maneira tradicional de passar certificados TLS/SSL para o Application Gateway para término TLS.
- Você pode fornecer opcionalmente uma referência a um certificado ou segredo do Key Vault existente quando criar um ouvinte habilitado para HTTPS.

A integração do Application Gateway com o Key Vault oferece muitos benefícios, incluindo:

- Segurança mais forte, porque os certificados TLS/SSL não são diretamente tratados pela equipe de desenvolvimento de aplicativos. A integração permite que uma equipe de segurança separada:
  * Configure gateways de aplicativos.
  * Controle ciclos de vida do gateway do aplicativo.
  * Conceda permissões a gateways de aplicativos selecionados para acessar certificados armazenados no cofre principal.
- Suporte para importar certificados existentes em seu cofre principal. Ou use APIs do Key Vault para criar e gerenciar novos certificados com qualquer um dos parceiros confiáveis do Key Vault.
- Suporte para renovação automática de certificados armazenados no cofre principal.

O Application Gateway atualmente suporta apenas certificados validados por software. Os certificados validados pelo Módulo de Segurança de Hardware (HSM) não são suportados. Depois que o Gateway de aplicativo é configurado para usar certificados do Key Vault, suas instâncias recuperam o certificado do Key Vault e os instalam localmente para término do TLS. As instâncias também pesquisam Key Vault em intervalos de 24 horas para recuperar uma versão renovada do certificado, se ele existir. Se um certificado atualizado for encontrado, o certificado TLS/SSL atualmente associado ao ouvinte HTTPS será automaticamente rotacionado.

> [!NOTE]
> O portal Azure só suporta certificados KeyVault, não segredos. O Application Gateway ainda suporta segredos de referência do KeyVault, mas apenas através de recursos não-Portal como PowerShell, CLI, API, modelos ARM, etc. 

## <a name="how-integration-works"></a>Como funciona a integração

A integração do Application Gateway com o Key Vault requer um processo de configuração em três etapas:

1. **Criar uma identidade gerenciada atribuída pelo usuário**

   Você cria ou reutiliza uma identidade gerenciada atribuída pelo usuário existente, que o Application Gateway usa para recuperar certificados do Key Vault em seu nome. Para saber mais, confira [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md). Esta etapa cria uma nova identidade no inquilino do Azure Active Directory. A identidade é confiável pela assinatura usada para criar a identidade.

1. **Configure seu cofre de chaves**

   Em seguida, você importa um certificado existente ou cria um novo no seu cofre principal. O certificado será usado por aplicativos que passam pelo gateway do aplicativo. Nesta etapa, você também pode usar um segredo do cofre de chaves que é armazenado como um arquivo PFX codificado sem senha, sem senha. Recomendamos o uso de um tipo de certificado devido ao recurso de renovação automática que está disponível com objetos do tipo certificado no cofre principal. Depois de criar um certificado ou um segredo, você define políticas de acesso no cofre principal para permitir que a identidade seja concedida *tenha* acesso ao segredo.
   
   > [!NOTE]
   > Se você implantar o gateway de aplicativo através de um modelo ARM, usando o Azure CLI ou PowerShell, ou através de um aplicativo Azure implantado no portal Azure, o certificado SSL armazenado no cofre de chaves como um arquivo PFX codificado com base 64 **deve ser sem senha**. Além disso, você deve completar as etapas em [Use Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md). É particularmente importante definir `enabledForTemplateDeployment` `true`para .

1. **Configure o gateway do aplicativo**

   Depois de concluir as duas etapas anteriores, você pode configurar ou modificar um gateway de aplicativo existente para usar a identidade gerenciada atribuída pelo usuário. Você também pode configurar o certificado TLS/SSL do ouvinte HTTP para apontar para o URI completo do certificado Key Vault ou iD secreto.

   ![Certificados de cofre chave](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Próximas etapas

[Configure o término do TLS com os certificados key vault usando o Azure PowerShell](configure-keyvault-ps.md)
