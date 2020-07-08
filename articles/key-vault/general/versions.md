---
title: Versões do Azure Key Vault
description: As diversas versões do Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 4959d530e1f7a3c777aa3db21fe577c853fbca13
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856006"
---
# <a name="key-vault-versions"></a>Versões do Key Vault

Aqui está o que há de novo com Azure Key Vault. Novos recursos e aprimoramentos também são anunciados no [canal de atualizações Key Vault do Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Junho de 2020

Azure Monitor para Key Vault agora está em visualização.  O Azure Monitor fornece monitoramento abrangente de seus cofres de chaves, fornecendo uma exibição unificada de suas solicitações de Key Vault, desempenho, falhas e latência. Para obter mais informações, consulte [Azure monitor para Key Vault (versão prévia).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maio de 2020

Key Vault "Traga sua própria chave" (BYOK) agora está disponível para o público geral. Consulte a [especificação do Azure Key Vault BYOK](../keys/byok-specification.md)e saiba como [importar chaves protegidas por HSM para Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Março de 2020

Pontos de extremidade privados agora disponíveis na visualização. O serviço de vínculo privado do Azure permite que você acesse Azure Key Vault e serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual.  Saiba como [integrar o Key Vault com o link privado do Azure](private-link-service.md).

## <a name="2019"></a>2019

- Versão dos SDKs de Azure Key Vault da próxima geração. Para obter exemplos de uso, consulte os guias de início rápido do segredo Azure Key Vault para [Python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)e [Node.js](../secrets/quick-create-node.md)
- Novas políticas do Azure para gerenciar certificados do cofre de chaves. Consulte as [definições internas de Azure Policy para Key Vault](../policy-samples.md).
- A extensão de máquina virtual Azure Key Vault agora está disponível para o público geral.  Consulte [Key Vault extensão de máquina virtual para Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Key Vault extensão de máquina virtual para Windows](../../virtual-machines/extensions/key-vault-windows.md).
- O gerenciamento de segredos controlados por eventos para Azure Key Vault agora disponível na grade de eventos do Azure. Para obter mais informações, consulte [o esquema da grade de eventos para eventos no Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD] e saiba como [receber e responder a notificações do Key Vault com a grade de eventos do Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Novos recursos e integrações lançados neste ano:

- Integração com o Azure Functions. Para um cenário de exemplo que aproveita [Azure Functions](../../azure-functions/index.yml) para operações do Key Vault, consulte [automatizar a rotação de um segredo](../secrets/tutorial-rotation.md). 
- [Integração com o Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Com isso, Azure Databricks agora dá suporte a dois tipos de escopos secretos: com backup Azure Key Vault e databricks-com suporte. Para obter mais informações, consulte [criar um escopo de segredo com backup Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Novos recursos lançados neste ano:

- Chaves da conta de armazenamento gerenciada. O recurso chaves da conta de armazenamento adicionou a integração mais fácil com o armazenamento do Azure. Consulte o tópico de visão geral para saber mais, [Visão geral das Chaves de Conta de Armazenamento Gerenciado](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Exclusão reversível. O recurso de exclusão reversível melhora a proteção de dados de seus cofres de chaves e objetos do cofre de chaves. Para saber mais, confira o tópico de visão geral, [Visão geral da exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Novos recursos lançados neste ano:
- Gerenciamento de certificados. Adicionado como um recurso à versão de GA 2015-06-01 em 26 de setembro de 2016.

A disponibilidade geral (versão 2015-06-01) foi anunciada em 24 de junho de 2015. As seguintes alterações foram feitas nessa versão: 
- Excluir uma chave - campo "usar" removido.
- Obtenha informações sobre uma chave - campo "usar" removido.
- Importar uma chave em um cofre - campo "usar" removido.
- Restaurar uma chave - campo "usar" removido.     
- "RSA_OAEP" alterado para "RSA-OAEP" para Algoritmos RSA. Consulte [sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).    
 
A segunda versão de visualização (versão 2015-02-01-Preview) foi anunciada em 20 de abril de 2015. Para saber mais, consulte a postagem no blog [Atualização da API REST](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx). As seguintes tarefas foram atualizadas:
 
- Listar as chaves em um cofre - adição de suporte a paginação para a operação.
- Listar as versões de uma chave - adição de operação para listar as versões de uma chave.  
- Listar os segredos em um cofre - adição de suporte a paginação.
- Listar as versões de um segredo - adição de operação para listar as versões de um segredo.  
- Todas as operações - adição de carimbos de data/hora de criação/atualização dos atributos.  
- Criar um segredo - adição de Content-Type aos segredos.
- Criar uma chave - adição de marcas como informações opcionais.
- Criar um segredo - adição de marcas como informações opcionais.
- Atualizar uma chave - adição de marcas como informações opcionais.
- Atualizar um segredo - adição de marcas como informações opcionais.
- Alteração do tamanho máximo para segredos de 10 K para 25 K Bytes. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
A primeira versão de visualização (versão 2014-12-08-Preview) foi anunciada em 8 de janeiro de 2015.  
 
## <a name="next-steps"></a>Próximas etapas

- [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md)
- [Chaves](../keys/index.yml)
- [Segredos](../secrets/index.yml)
- [Certificados](../certificates/index.yml)
