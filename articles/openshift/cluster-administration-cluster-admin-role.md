---
title: Função de administrador de cluster sazure Red Hat OpenShift | Microsoft Docs
description: Atribuição e uso da função de administrador de cluster Szure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139089"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Função de administrador de clientes Azure Red Hat OpenShift

Você é o administrador de cluster de um cluster Azure Red Hat OpenShift. Sua conta aumentou as permissões e o acesso a todos os projetos criados pelo usuário.

Quando sua conta tem a função de autorização de cluster de administrador do cliente vinculada a ela, ela pode gerenciar automaticamente um projeto.

> [!Note] 
> A função de cluster de administrador de clientes não é a mesma que a função cluster de administrador escoado.

Por exemplo, você pode executar ações associadas`create`a um conjunto de verbos ( ) para operar em um conjunto de nomes de recursos (`templates`). Para visualizar os detalhes dessas funções e seus conjuntos de verbos e recursos, execute o seguinte comando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Os nomes do verbo não necessariamente `oc` todos mapeiam diretamente para comandos. Eles equivalem mais geralmente aos tipos de operações de CLI que você pode realizar. 

Por exemplo, `list` ter o verbo significa que você pode exibir`oc get`uma lista de todos os objetos de um nome de recurso ( ). O `get` verbo significa que você pode exibir os detalhes`oc describe`de um objeto específico se você souber seu nome ( ).

## <a name="configure-the-customer-administrator-role"></a>Configure a função de administrador do cliente

Você pode configurar a função de cluster de administrador do cliente somente `--customer-admin-group-id`durante a criação do cluster, fornecendo o sinalizador . Este campo não é atualmente configurável no portal Azure. Para saber como configurar o Azure Active Directory e o grupo Administradores, consulte a [integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confirme a adesão na função de administrador do cliente

Para confirmar sua adesão ao grupo de admin `oc get nodes` `oc projects`do cliente, tente os comandos OpenShift CLI ou . `oc get nodes`mostrará uma lista de nós se você tiver a função de cluster de administrador esporáxo do cliente e um erro de permissão se você tiver apenas a função de projeto de administrador do cliente. `oc projects`mostrará todos os projetos no cluster em oposição apenas aos projetos em que você está trabalhando.

Para explorar ainda mais funções e permissões [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) em seu cluster, você pode usar o comando.

## <a name="next-steps"></a>Próximas etapas

Configure a função de cluster de administradores do cliente:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para O Azure Red Hat OpenShift](howto-aad-app-configuration.md)
