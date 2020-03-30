---
title: Visão geral do portal de desenvolvedores da API Management do Azure
titleSuffix: Azure API Management
description: Conheça o portal de desenvolvedores em Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335905"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão Geral do portal do desenvolvedor do Gerenciamento de API do Azure

O portal do desenvolvedor é um site gerado automaticamente e totalmente personalizável com a documentação de suas APIs. É onde os consumidores de API podem descobrir suas APIs, aprender como usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre versões autohospedadas e gerenciadas do portal do desenvolvedor no Gerenciamento de API. Também explica sua arquitetura e fornece respostas para perguntas frequentes.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Versões gerenciadas e autohospedadas

Você pode construir seu portal de desenvolvedores de duas maneiras:

- **Versão gerenciada** - editando e personalizando o portal, que é incorporado em `<your-api-management-instance-name>.developer.azure-api.net`sua instância de gerenciamento de API e é acessível através da URL . Consulte [este artigo de documentação](api-management-howto-developer-portal-customize.md) para saber como acessar e personalizar o portal gerenciado.
- **Versão autohospedada** - implantando e auto-hospedando seu portal fora de uma instância de gerenciamento de API. Essa abordagem permite editar a base de código do portal e estender a funcionalidade principal fornecida - por exemplo, implementar widgets personalizados para integrações com sistemas de terceiros. Neste cenário, você é o mantenedor do portal e é responsável por atualizar o portal para a versão mais recente. Para obter detalhes e instruções, consulte o [repositório do GitHub com o código fonte do portal][1] e o tutorial sobre a [implementação de um widget][3]. O [tutorial para a versão gerenciada](api-management-howto-developer-portal-customize.md) passa pelo painel administrativo do portal, que é comum para as versões gerenciadas e autohospedadas.

## <a name="portal-architectural-concepts"></a>Conceitos arquitetônicos portal

Os componentes do portal podem ser logicamente divididos em duas categorias: *código* e *conteúdo.*

*O código* é mantido [no repositório gitHub][1] e inclui:

- Widgets - que representam elementos visuais e combinam HTML, JavaScript, capacidade de estilo, configurações e mapeamento de conteúdo. Exemplos são uma imagem, um parágrafo de texto, um formulário, uma lista de APIs etc.
- Definições de estilo - que especificam como widgets podem ser estilizados
- Engine - que gera páginas estáticas do conteúdo do portal e é escrito em JavaScript
- Editor visual - que permite a personalização e experiência de autor no navegador

*O conteúdo* é dividido em duas subcategorias: *conteúdo do portal* e conteúdo de Gerenciamento de *API.*

*O conteúdo do portal* é específico para o portal e inclui:

- Páginas - por exemplo, landing page, tutoriais de API, posts de blog
- Mídia - imagens, animações e outros conteúdos baseados em arquivos
- Layouts - modelos, que são combinados com uma URL e definem como as páginas são exibidas
- Estilos - valores para definições de estilo, por exemplo, fontes, cores, bordas
- Configurações - configuração, por exemplo, favicon, metadados do site

*O conteúdo do portal,* exceto para a mídia, é expresso como documentos JSON.

*O conteúdo de gerenciamento de API* inclui entidades como APIs, Operações, Produtos, Assinaturas.

O portal é baseado em um garfo adaptado da [estrutura Paperbits](https://paperbits.io/). A funcionalidade original do Paperbits foi estendida para fornecer widgets específicos para gerenciamento de API (por exemplo, uma lista de APIs, uma lista de produtos) e um conector para o serviço de gerenciamento de API para salvar e recuperar conteúdo.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Perguntas frequentes

Nesta seção, respondemos perguntas comuns sobre o portal do desenvolvedor, que são de natureza geral. Para perguntas específicas da versão auto-hospedada, consulte [a seção wiki do repositório GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Como posso migrar da versão de pré-visualização do portal?

Usando a versão de visualização do portal do desenvolvedor, você provisionou o conteúdo de visualização em seu serviço de gerenciamento de API. O conteúdo padrão foi significativamente modificado na versão geralmente disponível para melhor experiência do usuário. Também inclui novos widgets.

Se estiver usando a versão gerenciada, redefini-lo com o conteúdo do portal clicando em **Redefinir** o conteúdo na seção **Menu Operações.** Confirmando que esta operação removerá todo o conteúdo do portal e providenciará o novo conteúdo padrão. O motor do portal foi atualizado automaticamente em seu serviço de Gerenciamento de API.

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se você estiver usando a versão auto-hospedada, use o `scripts/cleanup.bat` repositório `scripts/generate.bat` do GitHub para remover o conteúdo existente e provisionar novos conteúdos. Certifique-se de atualizar o código do seu portal para a versão mais recente do repositório gitHub de antemão.

Se você não quiser redefinir o conteúdo do portal, você pode considerar o uso de widgets recém-disponíveis em suas páginas. Os widgets existentes foram atualizados automaticamente para as versões mais recentes.

Se o seu portal foi provisionado após o anúncio de disponibilidade geral, ele já deve apresentar o novo conteúdo padrão. Nenhuma ação é necessária do seu lado.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Como posso migrar do antigo portal de desenvolvedores para o portal do desenvolvedor?

Os portais são incompatíveis e você precisa migrar o conteúdo manualmente.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>O portal tem todas as características do antigo portal?

O portal de desenvolvedores não suporta mais *aplicativos* e *problemas.*

A autenticação com o OAuth no console interativo do desenvolvedor ainda não é suportada. Você pode acompanhar o progresso através [do problema do GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>O velho portal foi preterido?

Os antigos portais de desenvolvedores e editores agora são recursos *legados* - eles receberão apenas atualizações de segurança. Novos recursos serão implementados apenas no novo portal de desenvolvedores.

A depreciação dos portais legados será anunciada separadamente. Se você tiver perguntas, preocupações ou comentários, levante-os em um problema dedicado do [GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funcionalidade que eu preciso não é suportada no portal

Você pode abrir uma [solicitação de recurso](https://aka.ms/apimwish) ou implementar a funcionalidade ausente você [mesmo][3]. Se você mesmo implementar a funcionalidade, você pode auto-hospedar o portal do desenvolvedor ou abrir uma solicitação de tração no GitHub para incluir as alterações na versão gerenciada.

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar implantações de portais?

Você pode acessar e gerenciar programáticamente o conteúdo do portal do desenvolvedor através da API REST, independentemente se você estiver usando uma versão gerenciada ou auto-hospedada.

A API está documentada [na seção wiki do repositório do GitHub][2]. Ele pode ser usado para automatizar migrações de conteúdo de portais entre ambientes - por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre esse processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) no GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal suporta modelos do Azure Resource Manager e/ou é compatível com o Kit de recursos de gerenciamento de DevOps de gerenciamento de API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Preciso ativar conectividade VNet adicional para as dependências de portal gerenciadas?

Na maioria dos casos, não.

Se o seu serviço de Gerenciamento de API estiver em um VNet interno, seu portal de desenvolvedores só será acessível dentro da rede. O nome de host do ponto final de gerenciamento deve ser resolvido no VIP interno do serviço a partir da máquina que você usa para acessar a interface administrativa do portal. Certifique-se de que o ponto final do gerenciamento está registrado no DNS. Em caso de erro de configuração, `Unable to start the portal. See if settings are specified correctly in the configuration (...)`você verá um erro: .

Se o seu serviço de Gerenciamento de API estiver em um VNet interno e você estiver acessando-o através do Application Gateway da Internet, certifique-se de habilitar a conectividade ao portal do desenvolvedor e aos pontos finais de gerenciamento do Gerenciamento de API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Eu designei um domínio personalizado de Gerenciamento de API e o portal publicado não funciona

Depois de atualizar o domínio, você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Eu adicionei um provedor de identidade e eu não posso vê-lo no portal

Depois de configurar um provedor de identidade (por exemplo, AAD, AAD B2C), você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Eu montei a delegação e o portal não a usa.

Depois de configurar a delegação, você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as mudanças entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Minhas outras alterações de configuração de gerenciamento de API não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, sign-in e termos do produto) requerem [a republicação do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Estou recebendo um erro CORS ao usar o console interativo

O console interativo faz uma solicitação de API do lado do cliente do navegador. Resolva o problema do CORS adicionando [uma política CORS](api-management-cross-domain-policies.md#CORS) em suas API(s).

Você pode verificar o status da política cors na seção **visão geral** do Portal do seu serviço de Gerenciamento de API no portal Azure. Uma caixa de aviso indica uma política ausente ou mal configurada.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Aplique automaticamente a política cors clicando no botão **Ativar CORS.**

Você também pode ativar o CORS manualmente.

1. Clique no **Manualmente aplicá-lo no** link de nível global para ver o código de política gerado.
2. Navegue para **Todas as APIs** na seção **APIs** do seu serviço de gerenciamento de API no portal Azure.
3. Clique no **</>** ícone na seção **De processamento de Entrada.**
4. Insira a **<inbound>** diretiva na seção do arquivo XML. Certifique-se **<origin>** de que o valor corresponda ao domínio do seu portal de desenvolvedores.

> [!NOTE]
> 
> Se você aplicar a diretiva CORS no escopo do Produto, em vez do escopo de API(s), e sua API usar autenticação de chave de assinatura através de um cabeçalho, seu console não funcionará.
>
> O navegador emite automaticamente uma solicitação HTTP opções, que não contém um cabeçalho com a chave de assinatura. Devido à chave de assinatura ausente, o Gerenciamento de API não pode associar a chamada OPTIONS a um Produto, por isso não pode aplicar a política cors.
>
> Como solução de solução, você pode passar a chave de assinatura em um parâmetro de consulta.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quais permissões preciso para editar o portal do desenvolvedor?

Se você estiver `Oops. Something went wrong. Please try again later.` vendo o erro ao abrir o portal no modo administrativo, você pode estar sem as permissões necessárias (RBAC).

Os portais legados `Microsoft.ApiManagement/service/getssotoken/action` exigiam a`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`permissão no escopo do serviço ( ) para permitir que o administrador do usuário acesse os portais. O novo portal `Microsoft.ApiManagement/service/users/token/action` requer a `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`permissão no escopo.

Você pode usar o seguinte script PowerShell para criar uma função com a permissão necessária. Lembre-se `<subscription-id>` de mudar o parâmetro. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Uma vez criada a função, ela pode ser concedida a qualquer usuário da seção Controle de **Acesso (IAM)** no portal Azure. Atribuir essa função a um usuário atribuirá a permissão no escopo de serviço. O usuário poderá gerar tokens SAS em nome de *qualquer* usuário do serviço. No mínimo, essa função precisa ser atribuída ao administrador do serviço. O seguinte comando PowerShell demonstra como atribuir `user1` a função a um usuário no escopo mais baixo para evitar a concessão de permissões desnecessárias ao usuário: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois que as permissões forem concedidas a um usuário, o usuário deve sair e fazer login novamente no portal Azure para que as novas permissões entrem em vigor.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou vendo o `Unable to start the portal. See if settings are specified correctly (...)` erro.

Este erro é `GET` mostrado `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` quando uma chamada falha. A chamada é emitida a partir do navegador pela interface administrativa do portal.

Se o serviço de gerenciamento de API estiver em um VNet - consulte a questão de conectividade VNet acima.

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável pelo navegador. Como atenuação, você pode remover o domínio personalizado do ponto final de gerenciamento - o Gerenciamento de API voltará ao ponto final padrão com um certificado confiável.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual é o suporte do navegador para o portal?

| Navegador                     | Com suporte       |
|-----------------------------|-----------------|
| Apple Safari                | Sim<sup>1</sup> |
| Google Chrome               | Sim<sup>1</sup> |
| Microsoft Edge              | Sim<sup>1</sup> |
| Microsoft Internet Explorer | Não              |
| Mozilla Firefox             | Sim<sup>1</sup> |

 <small><sup>1</sup> Suportado nas duas versões de produção mais recentes.</small>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o novo portal de desenvolvedores:

- [Acesse e personalize o portal de desenvolvedores gerenciados](api-management-howto-developer-portal-customize.md)
- [Configure a versão auto-hospedada do portal][2]
- [Implemente seu próprio widget][3]

Procurar outros recursos:

- [Repositório do GitHub com o código fonte][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend