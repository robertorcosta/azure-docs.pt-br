---
title: Acessar o plano de dados com o Azure AD, RBAC
description: Como acessar o plano de dados com Azure Active Directory controle de acesso baseado em função.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220104"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Acessar o plano de dados com Azure Active Directory e controle de acesso baseado em função

Este artigo explica como acessar o servidor de configuração do Spring Cloud do Azure e os pontos de extremidade do registro de serviço usando o controle de acesso baseado em função (AAD) Azure Active Directory).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Atribuir função a usuário/grupo, MSI ou entidade de serviço do AAD

Para usar o AAD e o RBAC, você deve atribuir a função de *leitor de dados do Azure Spring Cloud* a um usuário, grupo ou entidade de serviço pelo seguinte procedimento:

1. Vá para a página Visão geral do serviço de sua instância de serviço.

2. Clique em **controle de acesso (iam)** para abrir a folha controle de acesso.

3. Clique no botão **Adicionar** e **adicione atribuições de função** (a autorização pode ser necessária para adicionar).

4. Localize e selecione *leitor de dados do Azure Spring Cloud* em **função**.
5. Atribua acesso a `User, group, or service principal` ou `User assigned managed identity` de acordo com o tipo de usuário. Procure e selecione usuário.  
6. Clique em `Save`

   ![atribuir função](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Plano de dados do Access

Depois que um usuário do AAD recebe a função de *leitor de dados de nuvem do Azure Spring* , os clientes podem fazer logon no CLI do Azure com usuário, entidade de serviço ou identidade gerenciada.  Consulte [autenticar CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) para obter um token de acesso.  Em seguida, use os comandos a seguir.

```azurecli
az login
az account get-access-token
```

Atualmente, a CLI dá suporte a pontos de extremidade padrão do servidor de configuração e do registro de serviço. Para obter mais informações, consulte [pontos de extremidade prontos para produção](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). 

Os clientes também podem obter uma lista completa de pontos de extremidade com suporte do servidor de configuração e do registro de serviço acessando pontos de extremidade:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

O token de acesso no cabeçalho fornece autorização. Há suporte apenas para o método "GET".

Por exemplo, acesse um ponto de extremidade como *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* para ver o status de integridade de Eureka.

Vários pontos de extremidade raiz são mostrados abaixo de acordo com diferentes tipos de nuvem.

| Nuvem          | Ponto de extremidade raiz              |
| -------------- | -------------------------- |
| Público         | svc.azuremicroservices.io  |
| Mooncake/China | svc.microservices.azure.cn |

Se a resposta for *401 não autorizada*, verifique se a função foi atribuída com êxito.  Levará vários minutos para que a função tenha efeito ou verifique se o token de acesso não expirou.

## <a name="next-steps"></a>Próximas etapas
* [Autenticar CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Pontos de extremidade prontos para produção](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Confira também
* [Criar funções e permissões](spring-cloud-howto-permissions.md)