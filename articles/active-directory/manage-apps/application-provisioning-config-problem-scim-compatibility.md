---
title: Problemas conhecidos com a conformidade do protocolo SCIM 2,0-Azure AD
description: Como solucionar problemas de compatibilidade de protocolo comuns enfrentados ao adicionar um aplicativo inexistente na galeria que dá suporte a SCIM 2.0 ao Microsoft Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da171db6535100342342571a5c1f6468abd0fc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712350"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemas conhecidos e as resoluções em conformidade com o protocolo SCIM 2.0 do serviço de Provisionamento de Usuário do Microsoft Azure Active Directory

O Microsoft Azure Active Directory (Azure AD) pode provisionar automaticamente usuários e grupos para qualquer aplicativo ou sistema que seja administrado por um serviço Web com a interface definida no [Sistema para especificação do protocolo de Gerenciamento de Identidade Entre Domínios (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Suporte do Azure AD para o protocolo SCIM 2.0 é descrito em [Usando o Sistema para Gerenciamento de Identidade Entre Domínios (SCIM) para provisionar automaticamente usuários e grupos do Microsoft Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md), que lista o partes específicas do protocolo que implementa a fim de provisionar automaticamente usuários e grupos do Azure AD para aplicativos que dão suporte a SCIM 2.0.

Este artigo descreve os problemas atuais e anteriores com a aderência ao serviço de provisionamento do usuário do Azure Active Directory ao protocolo SCIM 2.0 e como trabalhar nesses problemas.

> [!IMPORTANT]
> A atualização mais recente para o cliente SCIM do serviço de provisionamento do usuário do Microsoft Azure Active Directory foi feita em 18 de dezembro de 2018. Essa atualização abordou as questões de compatibilidade conhecidas listadas na tabela abaixo. Consulte as perguntas frequentes abaixo para obter mais informações sobre essa atualização.

## <a name="scim-20-compliance-issues-and-status"></a>Problemas de conformidade de SCIM 2.0 e o status

| **Problemas de conformidade SCIM 2.0** |  **Corrigido?** | **Corrigir a data**  |  
|---|---|---|
| O Microsoft Azure Active Directory requer "/ scim" para estar na raiz da URL do ponto de extremidade do SCIM do aplicativo  | Sim  |  18 de dezembro de 2018 | 
| Os atributos de extensão usam ponto de notação "." antes de nomes de atributo, em vez de notação de dois pontos “:” |  Sim  | 18 de dezembro de 2018  | 
|  As solicitações de patch para atributos com vários valores contêm a sintaxe de filtro de caminho inválido | Sim  |  18 de dezembro de 2018  | 
|  As solicitações de criação de grupo contêm um URI de esquema inválido | Sim  |  18 de dezembro de 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>As correções de serviços foram aplicadas automaticamente ao meu aplicativo SCIM já existente?

Não. Como constituiria uma alteração significativa para aplicativos SCIM que foram codificados para trabalhar com o comportamento mais antigo, as alterações não foram aplicadas automaticamente para os aplicativos existentes.

As alterações são aplicadas a todos os novos [aplicativos SCIM não existentes na galeria](configure-single-sign-on-non-gallery-applications.md) configuradas no portal do Azure, após a data da correção.

Para obter informações sobre como migrar um trabalho de provisionamento pré-existente para incluir as correções mais recentes, consulte a próxima seção.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Posso migrar um trabalho de provisionamento do usuário baseado em SCIM para incluir as últimas correções de serviço?

Sim. Se você já estiver usando essa instância de aplicativo para logon único e precisa migrar o trabalho de provisionamento existente para incluir as correções mais recentes, siga o procedimento abaixo. Este procedimento descreve como usar a API do Microsoft Graph e o Gerenciador de API do Microsoft Graph para remover o trabalho de provisionamento antigo do seu aplicativo SCIM existente e criar um novo que exibe o novo comportamento.

> [!NOTE]
> Se o aplicativo ainda estiver em desenvolvimento e ainda não tiver sido implantado para o logon único ou provisionamento do usuário, a solução mais fácil será excluir a entrada do aplicativo na seção **Microsoft Azure Active Directory > Aplicativos Empresariais** do portal do Azure e simplesmente adicionar uma nova entrada para o aplicativo usando a opção **Criar aplicativo > Inexistente na galeria**. Isso é uma alternativa para executar o procedimento abaixo.
 
1. Entre no portal do Azure em: https://portal.azure.com.
2. Na seção **Azure Active Directory > Aplicativos Enterprise** do portal do Azure, localize e selecione o seu aplicativo de SCIM.
3. Na seção **Propriedades** do seu aplicativo existente do SCIM, copie a **ID de objeto**.
4. Em uma nova janela do navegador da web, vá para https://developer.microsoft.com/graph/graph-explorer e entre como o administrador de locatário do Microsoft Azure Active Directory onde seu aplicativo é adicionado.
5. No Explorador do Graph, execute o comando a seguir para localizar a ID do seu trabalho de provisionamento. Substitua "[object-id]" pelo serviço de ID da entidade (ID de objeto) copiado da terceira etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter trabalhos](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obter trabalhos") 


6. Nos resultados, copie a cadeia de caracteres completa de "ID" que começa com "customappsso" ou "scim".
7. Execute o comando a seguir para recuperar a configuração de mapeamento de atributo, para que você possa fazer um backup. Use o mesmo [object-id] como antes e substitua [job-id] pela ID de trabalho de provisionamento copiada da última etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obter esquema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Obter esquema") 

8. Copie a saída JSON da última etapa e salve-a em um arquivo de texto. Isso contém qualquer mapeamento de atributo personalizado que você adicionou para o seu aplicativo antigo e deve ser aproximadamente algumas milhares de linhas de JSON.
9. Execute o comando a seguir para excluir o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Execute o comando a seguir para criar um novo trabalho de provisionamento que tenha as correções mais recentes do serviço.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados da última etapa, copie a cadeia de caracteres completa de "ID" que começa com "scim". Opcionalmente, aplique novamente seus mapeamentos de atributos antigos, executando o comando abaixo, substituindo [new-job-id] com a nova ID de trabalho que você acabou de copiar e inserir que o JSON de saída da etapa 7 como o corpo da solicitação.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte para a primeira janela do navegador da web e selecione a guia **Provisionamento** para o seu aplicativo.
13. Verifique a configuração e, em seguida, inicie o trabalho de provisionamento. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>É possível adicionar um novo aplicativo inexistente na Galeria que tem o comportamento de provisionamento de usuário antigo?

Sim. Se você tivesse codificado um aplicativo para o comportamento antigo que existia antes das correções e precisar implantar uma nova instância dele, siga o procedimento a seguir. Este procedimento descreve como usar a API do Microsoft Graph e o Gerenciador de API do Microsoft Graph para criar um trabalho de provisionamento de SCIM que exiba o comportamento antigo.
 
1. Entre no portal do Azure em: https://portal.azure.com.
2. a seção **Azure Active Directory > aplicativos empresariais > Criar aplicativo** do portal do Azure, crie um novo aplicativo **inexistente na galeria**.
3. Na seção **propriedades** do seu novo aplicativo personalizado, copie a **ID de objeto**.
4. Em uma nova janela do navegador da web, vá para https://developer.microsoft.com/graph/graph-explorer e entre como o administrador de locatário do Microsoft Azure Active Directory onde seu aplicativo é adicionado.
5. No Graph Explorer, execute o comando a seguir para inicializar a configuração de provisionamento para seu aplicativo.
   Substitua "[object-id]" pelo serviço de ID da entidade (ID de objeto) copiado da terceira etapa.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Volte para a primeira janela do navegador da web e selecione a guia **Provisionamento** para o seu aplicativo.
7. Conclua o configuração de provisionamento de usuário conforme faria normalmente.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
[Saiba mais sobre o provisionamento e desprovisionamento para aplicativos SaaS](user-provisioning.md)

