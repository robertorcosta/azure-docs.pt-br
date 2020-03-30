---
title: Ativação de registros no portal do Diretório Ativo do Azure (pré-visualização) | Microsoft Docs
description: Introdução aos relatórios de atividades de provisionamento no portal do Diretório Ativo do Azure
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73612792"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Relatórios de provisionamento no portal do Diretório Ativo do Azure (pré-visualização)

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs** - de[auditoria Os registros de auditoria](concept-audit-logs.md) fornecem informações sobre a atividade do sistema sobre usuários e gerenciamento de grupo, aplicativos gerenciados e atividades de diretório.
    - **Registros de provisionamento** - Forneça atividades do sistema sobre usuários, grupos e funções que são provisionadas pelo serviço de provisionamento Azure AD. 

- **Segurança** 
    - **Logins arriscados** - Um [login arriscado](concept-risky-sign-ins.md) é um indicador para uma tentativa de login que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de usuário.
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral do relatório de provisionamento.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários nas funções de administrador de segurança, leitor de segurança, leitor de relatórios, administrador de aplicativos e administrador de aplicativos em nuvem
* Administradores globais


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Qual a licença Azure AD que você precisa para acessar atividades de provisionamento?

Seu inquilino deve ter uma licença Azure AD Premium associada a ele para ver o relatório de atividadede provisionamento de todos os anos. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. 

## <a name="provisioning-logs"></a>Provisionando logs

Os registros de provisionamento fornecem respostas às seguintes perguntas:

* Quais grupos foram criados com sucesso no ServiceNow?
* Como os papéis foram importados do Amazon Web Services?
* Quais usuários foram criados sem sucesso no DropBox?

Você pode acessar os logs de provisionamento selecionando **Logs de provisionamento** na seção **De monitoramento** da lâmina do Diretório Ativo do **Azure** no [portal Azure](https://portal.azure.com). Pode levar até duas horas para alguns registros de provisionamento aparecerem no portal.

![Provisionando logs](./media/concept-provisioning-logs/access-provisioning-logs.png "Provisionando logs")


Um registro de provisionamento tem uma exibição de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema de destino
- O status
- A data


![Colunas padrão](./media/concept-provisioning-logs/default-columns.png "Colunas padrão")

Você pode personalizar a exibição da lista clicando em **Colunas** na barra de ferramentas.

![Seletor de colunas](./media/concept-provisioning-logs/column-chooser.png "Seletor de colunas")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Colunas disponíveis](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Informações detalhadas](./media/concept-provisioning-logs/steps.png "Filtrar")


## <a name="filter-provisioning-activities"></a>Atividades de provisionamento de filtros

Para reduzir os dados relatados a um nível que funcione para você, você pode filtrar os dados de provisionamento usando os seguintes campos padrão. Observe que os valores nos filtros são dinamicamente preenchidos com base no seu inquilino. Se, por exemplo, você não tiver nenhum evento de criação em seu inquilino, não haverá uma opção de filtro para criar.

- Identidade
- Ação
- Sistema de origem
- Sistema de destino
- Status
- Data


![Filtrar](./media/concept-provisioning-logs/filter.png "Filtrar")

O filtro **Identidade** permite que você especifique o nome ou a identidade com que você se importa. Essa identidade pode ser um usuário, grupo, função ou outro objeto. Você pode pesquisar pelo nome ou ID do objeto. O ID varia de acordo com o cenário. Por exemplo, ao provisionar um objeto do Azure AD para salesForce, o ID de origem é o ID do objeto do usuário no Azure AD, enquanto o TargetID é o ID do usuário no Salesforce. Ao provisionar do Workday para o Active Directory, o ID de Origem é o ID do funcionário do trabalhador do dia de trabalho. Observe que o Nome do usuário nem sempre pode estar presente na coluna Identidade. Sempre haverá uma id. 

O filtro **Do Sistema de Origem** permite que você especifique de onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para o ServiceNow, o sistema Source é o Azure AD. 

O filtro **Sistema de** Destino permite que você especifique para onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para serviceNow, o sistema de destino é ServiceNow. 

O filtro **Status** permite que você selecione:

- Todos
- Sucesso
- Falha
- Ignorado

O filtro **Action** permite filtrar:

- Criar 
- Atualizar
- Excluir
- Desabilitar
- Outros

O filtro **Data** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Quando você seleciona um período de tempo personalizado, você pode configurar uma data de início e uma data de término.


Além dos campos padrão, quando selecionados, você também pode incluir os seguintes campos em seu filtro:

- **ID de trabalho** - Um ID de trabalho exclusivo está associado a cada aplicativo para o que você habilitou o provisionamento.   

- **ID do ciclo** - Identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar este ID para apoiar para procurar o ciclo em que este evento ocorreu.

- **Alterar ID** - Identificador exclusivo para o evento de provisionamento. Você pode compartilhar este ID para apoiar para procurar o evento de provisionamento.   



  

## <a name="provisioning-details"></a>Detalhes de provisionamento 

Quando você seleciona um item na exibição da lista de provisionamento, você obterá mais detalhes sobre este item.
Os detalhes são agrupados com base nas seguintes categorias:

- Etapas

- Solução de problemas e recomendações

- Propriedades modificadas

- Resumo


![Filtrar](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulações")



### <a name="steps"></a>Etapas

A guia **Passos** descreve as etapas tomadas para provisionar um objeto. O provisionamento de um objeto pode consistir em quatro etapas: 

- Objeto de importação
- Determine se o objeto está no escopo
- Objeto de correspondência entre origem e destino
- Provisão objeto (tomar medidas - isso pode ser uma criação, atualização, exclusão ou desativação)



![Filtrar](./media/concept-provisioning-logs/steps.png "Filtrar")


### <a name="troubleshoot-and-recommendations"></a>Solução de problemas e recomendações


A guia **solucionar problemas e recomendações** fornece o código de erro e a razão. As informações de erro só estão disponíveis em caso de falha. 


### <a name="modified-properties"></a>Propriedades modificadas

As **propriedades modificadas** mostram o valor antigo e o novo valor. Nos casos em que não há valor antigo, a antiga coluna de valor está em branco. 


### <a name="summary"></a>Resumo

A guia **resumo** fornece uma visão geral do que aconteceu e identifica o objeto no sistema de origem e destino. 

## <a name="what-you-should-know"></a>O que você deve saber

- As lojas do portal Azure informaram o fornecimento de dados por 30 dias se você tiver uma edição premium e 7 dias se você tiver uma edição gratuita..

- Você pode usar o atributo Alterar ID como identificador exclusivo. Isso é, por exemplo, útil ao interagir com o suporte ao produto.

- No momento, não há opção para baixar dados de provisionamento.

- Atualmente, não há suporte para análise de log.

- Quando você acessa os registros de provisionamento a partir do contexto de um aplicativo, ele não filtra automaticamente eventos para o aplicativo específico da maneira como os logs de auditoria fazem.

## <a name="error-codes"></a>Códigos de Erro

Use a tabela abaixo para entender melhor como resolver erros que você pode encontrar nos registros de provisionamento. Para quaisquer códigos de erro que estejam faltando, forneça feedback usando o link na parte inferior desta página. 

|Código do Erro|Descrição|
|---|---|
|Conflito, conflito de entrada|Corrija os valores de atributo conflitantes no Azure AD ou no aplicativo ou revise a configuração do atributo correspondente se a conta de usuário conflitante deveria ser igualada e assumida. Revise a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) a seguir para obter mais informações sobre a configuração de atributos correspondentes.|
|TooManyRequests|O aplicativo de destino rejeitou essa tentativa de atualizar o usuário porque ele está sobrecarregado e recebendo muitas solicitações. Não há nada para fazer. Esta tentativa será automaticamente aposentada. A Microsoft também foi notificada sobre esse problema.|
|InternalServerError |O aplicativo de destino retornou um erro inesperado. Pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcionasse. Esta tentativa será automaticamente retirada em 40 minutos.|
|Direitos insuficientes, métodonão permitido, não permitido, não autorizado| O Azure AD foi capaz de autenticar com o aplicativo de destino, mas não estava autorizado a realizar a atualização. Por favor, revise todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicação.|
|Entidade inprocessável|O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo de destino pode não estar correta, ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcionasse.|
|Erro de protocolo de webexception |Ocorreu um erro de protocolo HTTP ao se conectar ao aplicativo de destino. Não há nada para fazer. Esta tentativa será automaticamente retirada em 40 minutos.|
|Âncora inválido|Um usuário que foi criado anteriormente ou correspondido pelo serviço de provisionamento não existe mais. Verifique para garantir que o usuário exista. Para forçar uma recorrespondência de todos os usuários, use a API do MS Graph para [reiniciar o trabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Observe que a reinicialização do provisionamento desencadeará um ciclo inicial, que pode levar tempo para ser concluído. Ele também exclui o cache que o serviço de provisionamento usa para operar, o que significa que todos os usuários e grupos no inquilino terão que ser avaliados novamente e certos eventos de provisionamento podem ser descartados.|
|NotImplemented | O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo pode não estar correta, ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcionasse. Por favor, revise todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicação. |
|MandatoryFieldsMissing, MissingValues |O usuário não pôde ser criado porque os valores necessários estão faltando. Corrija os valores de atributo ausentes no registro de origem ou revise a configuração do atributo correspondente para garantir que os campos necessários não sejam omitidos. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre a configuração de atributos correspondentes.|
|Esquemaatribuinão encontrado |Não foi possível realizar a operação porque foi especificado um atributo que não existe no aplicativo de destino. Consulte a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre personalização de atributos e certifique-se de que sua configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de provisionamento Azure AD. Não há nada para fazer. Esta tentativa será automaticamente repetida em 40 minutos.|
|Domínio inválido |A operação não pôde ser realizada devido a um valor de atributo contendo um nome de domínio inválido. Atualize o nome de domínio no usuário ou adicione-o à lista permitida no aplicativo de destino. |
|Tempo limite |A operação não pôde ser concluída porque o aplicativo alvo demorou muito para responder. Não há nada para fazer. Esta tentativa será automaticamente repetida em 40 minutos.|
|LicenseLimitExceeded|O usuário não pôde ser criado no aplicativo de destino porque não há licenças disponíveis para este usuário. Obtenha licenças adicionais para o aplicativo de destino ou revise suas atribuições de usuário e configuração de mapeamento de atributos para garantir que os usuários corretos sejam atribuídos aos atributos corretos.|
|Entradas duplicadas targete  |A operação não pôde ser concluída porque mais de um usuário no aplicativo de destino foi encontrado com os atributos de correspondência configurados. Remova o usuário duplicado do aplicativo de destino ou reconfigure seus mapeamentos de atributos conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|Entradas duplicadasSourceE | A operação não pôde ser concluída porque mais de um usuário foi encontrado com os atributos de correspondência configurados. Remova o usuário duplicado ou reconfigure seus mapeamentos de atributos conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Próximas etapas

* [Verifique o status do provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


