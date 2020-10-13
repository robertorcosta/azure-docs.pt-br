---
title: Provisionando logs no portal de Azure Active Directory (versão prévia) | Microsoft Docs
description: Introdução ao provisionamento de relatórios de logs no portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/07/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6109f35c42d4b4a44430eeb99ec115f4cdc1a619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812549"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisionando relatórios no portal de Azure Active Directory (versão prévia)

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs**  -  de auditoria [Os logs de auditoria](concept-audit-logs.md) fornecem informações de atividade do sistema sobre gerenciamento de usuários e de grupos, aplicativos gerenciados e atividades de diretório.
    - **Logs de provisionamento** – forneça a atividade do sistema sobre usuário, grupos e funções que são provisionadas pelo serviço de provisionamento do Azure AD. 

- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](../identity-protection/overview-identity-protection.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** – um [usuário arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral do relatório de provisionamento.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Proprietários do aplicativo
* Usuários das funções administrador de segurança, leitor de segurança, leitor de relatórios, administrador de aplicativos e administrador de aplicativos de nuvem
* Administradores globais


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Qual licença do Azure AD você precisa para acessar as atividades de provisionamento?

Seu locatário deve ter uma licença de Azure AD Premium associada a ele para ver o relatório de atividade de provisionamento. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. 

## <a name="provisioning-logs"></a>Provisionando logs

Os logs de provisionamento fornecem respostas para as seguintes perguntas:

* Quais grupos foram criados com êxito no ServiceNow?
* Como as funções foram importadas do Amazon Web Services?
* Quais usuários não foram criados com êxito no DropBox?

Você pode acessar os logs de provisionamento selecionando **logs de provisionamento** na seção **monitoramento** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registros de provisionamento sejam exibidos no Portal.

![Logs de provisionamento](./media/concept-provisioning-logs/access-provisioning-logs.png "Provisionando logs")


Um log de provisionamento tem uma exibição de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema de destino
- O status
- A data


![Colunas padrão](./media/concept-provisioning-logs/default-columns.png "Colunas padrão")

Você pode personalizar o modo de exibição de lista clicando em **colunas** na barra de ferramentas.

![Seletor de coluna](./media/concept-provisioning-logs/column-chooser.png "Seletor de coluna")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Colunas disponíveis](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Informações detalhadas](./media/concept-provisioning-logs/steps.png "Filtrar")


## <a name="filter-provisioning-activities"></a>Filtrar atividades de provisionamento

Você pode filtrar os dados de provisionamento. Alguns valores de filtro são preenchidos dinamicamente com base no seu locatário. Se, por exemplo, você não tiver nenhum evento de criação em seu locatário, não haverá uma opção de filtro para Create.
Na exibição padrão, você pode selecionar os seguintes filtros:

- Identidade
- Data
- Status
- Ação


![Adicionar filtros](./media/concept-provisioning-logs/default-filter.png "Filtrar")

O filtro de **identidade** permite que você especifique o nome ou a identidade sobre a qual você se preocupa. Essa identidade pode ser um usuário, grupo, função ou outro objeto. Você pode Pesquisar pelo nome ou ID do objeto. A ID varia de acordo com o cenário. Por exemplo, ao provisionar um objeto do Azure AD para o SalesForce, a ID de origem é a ID de objeto do usuário no Azure AD, enquanto a TargetId é a ID do usuário no Salesforce. Ao provisionar do WORKDAY para Active Directory, a ID de origem é a ID de funcionário do workday Worker. Observe que o nome do usuário talvez nem sempre esteja presente na coluna de identidade. Sempre haverá uma ID. 


O filtro **Data** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Ao selecionar um período de tempo personalizado, você pode configurar uma data de início e uma data de término.


O filtro **Status** permite que você selecione:

- Todos
- Sucesso
- Falha
- Ignorado



O filtro de **ação** permite filtrar o:

- Criar 
- Atualizar
- Excluir
- Desabilitar
- Outros

Além disso, para os filtros do modo de exibição padrão, você também pode definir os seguintes filtros:

- ID do Trabalho
- ID do ciclo
- Alterar ID
- ID da origem
- ID de destino
- Aplicativo


![Escolha um campo](./media/concept-provisioning-logs/add-filter.png "Escolha um campo")


- **ID do trabalho** – uma ID de trabalho exclusiva está associada a cada aplicativo para o qual você habilitou o provisionamento.   

- **ID do ciclo** -identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar essa ID para dar suporte à pesquisa do ciclo em que esse evento ocorreu.

- **ID de alteração** -identificador exclusivo do evento de provisionamento. Você pode compartilhar essa ID para dar suporte à pesquisa do evento de provisionamento.   


- **Sistema de origem** – permite que você especifique onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para o ServiceNow, o sistema de origem é o Azure AD. 

- **Sistema de destino** – permite especificar para onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para o ServiceNow, o sistema de destino é ServiceNow. 

- **Aplicativo** – permite que você mostre somente os registros de aplicativos com um nome de exibição que contém uma cadeia de caracteres específica.

 

## <a name="provisioning-details"></a>Detalhes de provisionamento 

Ao selecionar um item no modo de exibição de lista de provisionamento, você obtém mais detalhes sobre esse item.
Os detalhes são agrupados com base nas seguintes categorias:

- Etapas

- Solução de problemas e recomendações

- Propriedades modificadas

- Resumo


![Detalhes de provisionamento](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulações")



### <a name="steps"></a>Etapas

A guia **etapas** descreve as etapas executadas para provisionar um objeto. O provisionamento de um objeto pode consistir em quatro etapas: 

- Importar objeto
- Determinar se o objeto está no escopo
- Corresponder objeto entre origem e destino
- Provisionar objeto (executar ação-pode ser criar, atualizar, excluir ou desabilitar)



![Captura de tela mostra a guia etapas, que mostra as etapas de provisionamento.](./media/concept-provisioning-logs/steps.png "Filtrar")


### <a name="troubleshoot-and-recommendations"></a>Solução de problemas e recomendações


A guia **solucionar problemas e recomendações** fornece o código de erro e o motivo. As informações de erro só estão disponíveis no caso de uma falha. 


### <a name="modified-properties"></a>Propriedades modificadas

As **propriedades modificadas** mostram o valor antigo e o novo valor. Nos casos em que não há nenhum valor antigo, a coluna valor antigo fica em branco. 


### <a name="summary"></a>Resumo

A guia **Resumo** fornece uma visão geral do que aconteceu e identificadores para o objeto no sistema de origem e de destino. 

## <a name="what-you-should-know"></a>O que você deve saber

- O portal do Azure repositórios relataram dados de provisionamento por 30 dias se você tiver uma edição Premium e 7 dias se tiver uma edição gratuita. Os logs de provisionamento podem ser publicados no log Analytics para retenção além de 30 dias. 

- Você pode usar o atributo ID de alteração como um identificador exclusivo. Isso é, por exemplo, útil ao interagir com o suporte ao produto.

- Atualmente, não há nenhuma opção para baixar dados de provisionamento como um arquivo CSV, mas você pode exportar os dados usando [Microsoft Graph](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http).

- Você pode ver eventos ignorados para usuários que não estão no escopo. Isso é esperado, especialmente quando o escopo de sincronização é definido como todos os usuários e grupos. Nosso serviço avaliará todos os objetos no locatário, mesmo aqueles que estão fora do escopo. 

- Os logs de provisionamento não estão disponíveis no momento na nuvem governamental. Se não for possível acessar os logs de provisionamento, use os logs de auditoria como uma solução alternativa temporária.  

## <a name="error-codes"></a>Códigos de erro

Use a tabela a seguir para entender melhor como resolver erros que podem ser encontrados nos logs de provisionamento. Para quaisquer códigos de erro ausentes, forneça comentários usando o link na parte inferior desta página. 

|Código do Erro|Descrição|
|---|---|
|Conflito, EntryConflict|Corrija os valores de atributo conflitantes no Azure AD ou no aplicativo ou revise a configuração de atributo correspondente se a conta de usuário conflitante deveria ser correspondida e assumida. Examine a [documentação](../app-provisioning/customize-application-attributes.md) a seguir para obter mais informações sobre como configurar atributos correspondentes.|
|TooManyRequests|O aplicativo de destino rejeitou essa tentativa de atualizar o usuário porque ele está sobrecarregado e recebendo muitas solicitações. Não há nada a fazer. Essa tentativa será desativada automaticamente. A Microsoft também foi notificada desse problema.|
|InternalServerError |O aplicativo de destino retornou um erro inesperado. Pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione. Essa tentativa será desativada automaticamente em 40 minutos.|
|InsufficientRights, MethodNotAllowed, não permitido, não autorizado| O Azure AD foi capaz de autenticar com o aplicativo de destino, mas não foi autorizado a executar a atualização. Examine todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](../saas-apps/tutorial-list.md)de aplicativo.|
|UnprocessableEntity|O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo de destino pode não estar correta, ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione.|
|WebExceptionProtocolError |Ocorreu um erro de protocolo HTTP durante a conexão com o aplicativo de destino. Não há nada a fazer. Essa tentativa será desativada automaticamente em 40 minutos.|
|InvalidAnchor|Um usuário que foi criado ou correspondido anteriormente pelo serviço de provisionamento não existe mais. Verifique se o usuário existe. Para forçar uma nova correspondência de todos os usuários, use o MS API do Graph para [reiniciar o trabalho](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Observe que a reinicialização do provisionamento disparará um ciclo inicial, o que pode levar tempo para ser concluído. Ele também exclui o cache que o serviço de provisionamento usa para operar, o que significa que todos os usuários e grupos no locatário terão que ser avaliados novamente e determinados eventos de provisionamento poderão ser descartados.|
|NotImplemented | O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo pode não estar correta ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione. Examine todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](../saas-apps/tutorial-list.md)de aplicativo. |
|MandatoryFieldsMissing, MissingValues |Não foi possível criar o usuário porque os valores necessários estão ausentes. Corrija os valores de atributo ausentes no registro de origem ou examine a configuração de atributo correspondente para garantir que os campos obrigatórios não sejam omitidos. [Saiba mais](../app-provisioning/customize-application-attributes.md) sobre como configurar atributos correspondentes.|
|SchemaAttributeNotFound |Não foi possível executar a operação porque foi especificado um atributo que não existe no aplicativo de destino. Consulte a [documentação](../app-provisioning/customize-application-attributes.md) sobre personalização de atributo e verifique se a configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de provisionamento do Azure AD. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|InvalidDomain |A operação não pôde ser executada devido a um valor de atributo que contém um nome de domínio inválido. Atualize o nome de domínio no usuário ou adicione-o à lista de permitidos no aplicativo de destino. |
|Tempo limite |A operação não pôde ser concluída porque o aplicativo de destino demorou muito tempo para responder. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|LicenseLimitExceeded|Não foi possível criar o usuário no aplicativo de destino porque não há licenças disponíveis para esse usuário. Adquira licenças adicionais para o aplicativo de destino ou examine as atribuições de usuário e a configuração de mapeamento de atributo para garantir que os usuários corretos sejam atribuídos com os atributos corretos.|
|DuplicateTargetEntries  |A operação não pôde ser concluída porque mais de um usuário no aplicativo de destino foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado do aplicativo de destino ou RECONFIGURE os mapeamentos de atributo conforme descrito [aqui](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | A operação não pôde ser concluída porque mais de um usuário foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado ou RECONFIGURE os mapeamentos de atributo conforme descrito [aqui](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Quando cada usuário é avaliado, tentamos importar o usuário do sistema de origem. Esse erro geralmente ocorre quando o usuário que está sendo importado não tem a propriedade correspondente definida em seus mapeamentos de atributo. Sem um valor presente no objeto de usuário para o atributo correspondente, não podemos avaliar as alterações de escopo, correspondência ou exportação. Observação: a presença desse erro não indica que o usuário está no escopo, pois ainda não avaliamos o escopo do usuário.|
|EntrySynchronizationSkipped | O serviço de provisionamento consultou com êxito o sistema de origem e identificou o usuário. Nenhuma ação adicional foi realizada no usuário e elas foram ignoradas. O Skip pode ser devido ao usuário estar fora do escopo ou ao usuário já existente no sistema de destino sem nenhuma alteração adicional necessária.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Ao executar uma solicitação GET para recuperar um usuário ou grupo, recebemos vários usuários ou grupos na resposta. Esperávamos receber apenas um usuário ou grupo na resposta. Se, [por exemplo](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#get-group), fizermos uma solicitação get para recuperar um grupo e fornecer um filtro para excluir Membros e seu ponto de extremidade scim retornar os membros, lançaremos esse erro.|

## <a name="next-steps"></a>Próximas etapas

* [Verificar o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API do grafo de logs de provisionamento](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
