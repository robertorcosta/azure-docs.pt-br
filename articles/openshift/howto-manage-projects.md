---
title: Gerenciar recursos no Azure Red Hat OpenShift | Microsoft Docs
description: Gerenciar projetos, modelos, fluxos de imagem em um cluster Azure Red Hat OpenShift
services: openshift
keywords: red hat openshift projetos solicita auto-provisionador
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139106"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gerenciar projetos, modelos, fluxos de imagem em um cluster Azure Red Hat OpenShift 

Em uma plataforma de contêineres OpenShift, os projetos são usados para agrupar e isolar objetos relacionados. Como administrador, você pode dar aos desenvolvedores acesso a projetos específicos, permitir que eles criem seus próprios projetos e conceder-lhes direitos administrativos a projetos individuais.

## <a name="self-provisioning-projects"></a>Projetos de autoprovisionamento

Você pode permitir que os desenvolvedores criem seus próprios projetos. Um ponto final da API é responsável pelo provisionamento de um projeto de acordo com um modelo chamado project-request. O console web `oc new-project` e o comando usam este ponto final quando um desenvolvedor cria um novo projeto.

Quando uma solicitação de projeto é submetida, a API substitui os seguintes parâmetros no modelo:

| Parâmetro               | Descrição                                    |
| ----------------------- | ---------------------------------------------- |
| Project_name            | O nome do projeto. Obrigatórios.             |
| PROJECT_DISPLAYNAME     | O nome de exibição do projeto. Pode ficar em branco. |
| PROJECT_DESCRIPTION     | A descrição do projeto. Pode ficar em branco.  |
| PROJECT_ADMIN_USER      | O nome de usuário do usuário administrador.       |
| PROJECT_REQUESTING_USER | O nome de usuário do usuário solicitante.           |

O acesso à API é concedido aos desenvolvedores com a vinculação de função de cluster de autoprovisionadores. Este recurso está disponível para todos os desenvolvedores autenticados por padrão.

## <a name="modify-the-template-for-a-new-project"></a>Modifique o modelo para um novo projeto 

1. Faça login como `customer-admin` usuário com privilégios.

2. Editar o modelo padrão de solicitação de projeto.

   ```
   oc edit template project-request -n openshift
   ```

3. Remova o modelo padrão do projeto do processo de atualização ARO (Red Hat OpenShift) do Azure Red Hat adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   O modelo de solicitação de projeto não será atualizado pelo processo de atualização ARO. Isso permite que os clientes personalizem o modelo e preservem essas personalizações quando o cluster for atualizado.

## <a name="disable-the-self-provisioning-role"></a>Desativar a função de autoprovisionamento

Você pode impedir que um grupo de usuários autenticado faça o autoprovisionamento de novos projetos.

1. Faça login como `customer-admin` usuário com privilégios.

2. Edite a vinculação de função de cluster de autoprovisionadores.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Remova a função do processo de atualização ARO `openshift.io/reconcile-protect: "true"`adicionando a seguinte anotação: .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Alterar a vinculação `system:authenticated:oauth` da função de cluster para evitar a criação de projetos:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Gerenciar modelos padrão e imageStreams

No Azure Red Hat OpenShift, você pode desativar atualizações para `openshift` quaisquer modelos padrão e fluxos de imagem dentro do namespace.
Para desativar atualizações `Templates` para `ImageStreams` `openshift` ALL e no namespace:

1. Faça login como `customer-admin` usuário com privilégios.

2. Editar `openshift` namespace:

   ```
   oc edit namespace openshift
   ```

3. Remova `openshift` o namespace do processo de atualização ARO adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Qualquer objeto individual `openshift` no namespace pode ser removido do `openshift.io/reconcile-protect: "true"` processo de atualização adicionando anotação a ele.

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)
