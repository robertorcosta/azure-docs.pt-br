---
title: Use chaves mantadas pelo cliente para criptografar dados no Cache Azure HPC
description: Como usar o Azure Key Vault com o Azure HPC Cache para controlar o acesso à chave de criptografia em vez de usar as chaves de criptografia gerenciadas pela Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538863"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Use chaves de criptografia gerenciadas pelo cliente para cache Azure HPC

Você pode usar o Azure Key Vault para controlar a propriedade das chaves usadas para criptografar seus dados no Cache Azure HPC. Este artigo explica como usar chaves gerenciadas pelo cliente para criptografia de dados de cache.

> [!NOTE]
> Todos os dados armazenados no Azure, inclusive nos discos de cache, são criptografados em repouso usando chaves gerenciadas pela Microsoft por padrão. Você só precisa seguir os passos deste artigo se quiser gerenciar as chaves usadas para criptografar seus dados.

Este recurso está disponível apenas nestas regiões do Azure:

* Leste dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2

Existem três etapas para habilitar a criptografia de chave gerenciada pelo cliente para o Cache Azure HPC:

1. Configure um Cofre azure key para armazenar as chaves.
1. Ao criar o Cache Azure HPC, escolha a criptografia de chave gerenciada pelo cliente e especifique o cofre de chaves e a chave a ser usada.
1. Depois que o cache for criado, autorize-o a acessar o cofre de chaves.

A criptografia não é completamente configurada até depois de autorizar a partir do cache recém-criado (etapa 3). Isso porque você deve passar a identidade do cache para o cofre de chaves para torná-lo um usuário autorizado. Você não pode fazer isso antes de criar o cache, porque a identidade não existe até que o cache seja criado.

Depois de criar o cache, não é possível alterar entre chaves gerenciadas pelo cliente e chaves gerenciadas pela Microsoft. No entanto, se o cache usar chaves gerenciadas pelo cliente, você pode [alterar](#update-key-settings) a chave de criptografia, a versão chave e o cofre de chaves conforme necessário.

## <a name="understand-key-vault-and-key-requirements"></a>Entenda os principais requisitos do cofre e dos principais

O cofre-chave e a chave devem atender a esses requisitos para trabalhar com o Cache Azure HPC.

Propriedades do cofre chave:

* **Assinatura** - Use a mesma assinatura que é usada para o cache.
* **Região** - O cofre-chave deve estar na mesma região que o Cache Azure HPC.
* **Nível de preços** - O nível padrão é suficiente para uso com cache Azure HPC.
* **Exclusão suave** - O Cache Azure HPC permitirá a exclusão suave se ele ainda não estiver configurado no cofre de chaves.
* **Proteção contra expurgo** - A proteção contra expurgo deve ser ativada.
* **Política de acesso** - As configurações padrão são suficientes.
* **Conectividade de rede** - O Cache Azure HPC deve ser capaz de acessar o cofre de chaves, independentemente das configurações de ponto final escolhidas.

Propriedades-chave:

* **Tipo de chave** - RSA
* **Tamanho da chave RSA** - 2048
* **Ativado** - Sim

Permissões de acesso ao cofre chave:

* O usuário que cria o Cache Azure HPC deve ter permissões equivalentes à [função de contribuinte](../role-based-access-control/built-in-roles.md#key-vault-contributor)do Key Vault . As mesmas permissões são necessárias para configurar e gerenciar o Azure Key Vault.

  Leia [acesso seguro a um cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md) para obter mais informações.

## <a name="1-set-up-azure-key-vault"></a>1. Configurar o Cofre de Chaves Azure

Você pode configurar um cofre de chaves e uma chave antes de criar o cache ou fazê-lo como parte da criação do cache. Certifique-se de que esses recursos atendam aos requisitos descritos [acima.](#understand-key-vault-and-key-requirements)

No momento da criação do cache, você deve especificar uma versão de cofre, chave e chave para usar para a criptografia do cache.

Leia a documentação do [Azure Key Vault](../key-vault/key-vault-overview.md) para obter detalhes.

> [!NOTE]
> O Azure Key Vault deve usar a mesma assinatura e estar na mesma região que o Cache Azure HPC. Use uma das regiões suportadas listadas no início deste artigo.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Crie o cache com chaves gerenciadas pelo cliente habilitadas

Você deve especificar a fonte da chave de criptografia ao criar o cache Do Azure HPC. Siga as instruções em [Criar um cache Azure HPC](hpc-cache-create.md)e especifique o cofre de chaves e a chave na página **Chaves de criptografia do Disco.** Você pode criar um novo cofre de chaves e uma chave durante a criação do cache.

> [!TIP]
> Se a página **chaves de criptografia de disco** não aparecer, certifique-se de que seu cache esteja em uma das regiões suportadas.

O usuário que cria o cache deve ter privilégios iguais à [função de contribuinte](../role-based-access-control/built-in-roles.md#key-vault-contributor) do Key Vault ou superior.

1. Clique no botão para habilitar chaves gerenciadas privadamente. Depois de alterar essa configuração, as configurações do cofre da chave aparecerão.

1. Clique **em Selecionar um cofre de chaves** para abrir a página de seleção de chaves. Escolha ou crie o cofre de chaves e a chave para criptografar dados nos discos deste cache.

   Se o cofre de chaves do Azure não aparecer na lista, verifique esses requisitos:

   * O cache está na mesma assinatura do cofre de chaves?
   * O cache é na mesma região do cofre de chaves?
   * Existe conectividade de rede entre o portal Azure e o cofre chave?

1. Depois de selecionar um cofre, selecione a chave individual das opções disponíveis ou crie uma nova chave. A chave deve ser uma chave RSA de 2048 bits.

1. Especifique a versão para a tecla selecionada. Saiba mais sobre a versão na documentação do [Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning).

Continue com o resto das especificações e crie o cache conforme descrito em [Criar um cache Azure HPC](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorize a criptografia do Azure Key Vault a partir do cache
<!-- header is linked from create article, update if changed -->

Após alguns minutos, o novo Cache Azure HPC aparece no seu portal Azure. Vá até a página **Visão Geral** para autorizar a cessação do Cofre de Chaves do Azure e habilite a criptografia de chave gerenciada pelo cliente. (O cache pode aparecer na lista de recursos antes que as mensagens de "implantação em andamento" sejam claras.)

Esse processo de duas etapas é necessário porque a instância de cache Do Azure HPC precisa de uma identidade para passar ao Azure Key Vault para autorização. A identidade de cache não existe até que suas etapas iniciais de criação sejam concluídas.

> [!NOTE]
> Você deve autorizar a criptografia dentro de 90 minutos após a criação do cache. Se você não completar esta etapa, o cache será cronometrado e falhará. Um cache com falha tem que ser recriado, não pode ser corrigido.

O cache mostra o status **À espera da chave**. Clique no botão **Ativar criptografia** na parte superior da página para autorizar o cache a acessar o cofre de chaves especificado.

![captura de tela da página de visão geral do cache no portal, com destaque no botão Ativar criptografia (linha superior) e Status: Esperando a chave](media/waiting-for-key.png)

Clique **em Ativar criptografia** e clique no botão **Sim** para autorizar o cache para usar a chave de criptografia. Esta ação também permite a proteção de exclusão e purga suave (se ainda não estiver habilitada) no cofre de chaves.

![captura de tela da página de visão geral de cache no portal, com uma mensagem de banner na parte superior que pede ao usuário para ativar a criptografia clicando em sim](media/enable-keyvault.png)

Depois que o cache solicita acesso ao cofre de chaves, ele pode criar e criptografar os discos que armazenam dados armazenados em cache.

Depois de autorizar a criptografia, o Cache Azure HPC passa por vários minutos de configuração para criar os discos criptografados e a infra-estrutura relacionada.

## <a name="update-key-settings"></a>Atualizar as configurações das chaves

Você pode alterar o cofre de chaves, a chave ou a versão-chave do seu cache no portal Azure. Clique no link **Configurações** de criptografia do cache para abrir a página **de configurações de tecla cliente.** (Não é possível alterar um cache entre chaves gerenciadas pelo cliente e chaves gerenciadas pelo sistema.)

![captura de tela da página "Configurações das chaves do cliente", alcançada clicando em Configurações > Criptografia da página de cache no portal Azure](media/change-key-click.png)

Clique no link **Alterar chave** e clique em Alterar o cofre **de chaves, a chave ou** a versão para abrir o seletor de chaves.

![captura de tela da página "selecione a chave do Azure Key Vault" com três seletores para baixo para escolher o cofre de chaves, a chave e a versão](media/select-new-key.png)

Os cofres-chave na mesma assinatura e na mesma região que este cache são mostrados na lista.

Depois de escolher os novos valores da chave de criptografia, clique **em Selecionar**. Uma página de confirmação aparece com os novos valores. Clique **em Salvar** para finalizar a seleção.

![captura de tela da página de confirmação com botão Salvar no canto superior esquerdo](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Leia mais sobre chaves gerenciadas pelo cliente no Azure

Esses artigos explicam mais sobre o uso do Azure Key Vault e chaves gerenciadas pelo cliente para criptografar dados no Azure:

* [Visão geral da criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md)
* [Criptografia de disco com chaves gerenciadas pelo cliente](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) - Documentação para usar o Azure Key Vault e discos gerenciados, que é semelhante ao processo usado com o Cache Azure HPC

## <a name="next-steps"></a>Próximas etapas

Depois de criar o Cache Azure HPC e autorizar a criptografia baseada no Key Vault, continue a configurar seu cache dando-lhe acesso às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
