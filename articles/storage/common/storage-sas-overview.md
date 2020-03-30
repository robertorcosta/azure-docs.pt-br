---
title: Conceder acesso limitado a dados com assinaturas de acesso compartilhado (SAS)
titleSuffix: Azure Storage
description: Saiba como usar assinaturas de acesso compartilhado (SAS) para delegar o acesso aos recursos do Azure Storage, incluindo blobs, filas, tabelas e arquivos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255230"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)

Uma assinatura de acesso compartilhado (SAS) fornece acesso delegado seguro aos recursos em sua conta de armazenamento sem comprometer a segurança de seus dados. Com um SAS, você tem controle granular sobre como um cliente pode acessar seus dados. Você pode controlar quais recursos o cliente pode acessar, quais permissões eles têm sobre esses recursos e quanto tempo o SAS é válido, entre outros parâmetros.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de Acesso compartilhado.

O Azure Storage suporta três tipos de assinaturas de acesso compartilhadas:

- **Delegação de usuários SAS.** Uma delegação de usuários SAS é protegida com credenciais do Azure Active Directory (Azure AD) e também pelas permissões especificadas para o SAS. Uma delegação de usuário SAS aplica-se apenas ao armazenamento Blob.

    Para obter mais informações sobre a delegação de usuários SAS, consulte [Criar uma delegação de usuários SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **SAS de Serviço.** Um SAS de serviço é protegido com a chave da conta de armazenamento. Um serviço SAS delega acesso a um recurso em apenas um dos serviços de armazenamento do Azure: armazenamento Blob, armazenamento na fila, armazenamento de tabela ou arquivos Azure. 

    Para obter mais informações sobre o serviço SAS, consulte [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **SAS de Conta.** Uma conta SAS é protegida com a chave da conta de armazenamento. Uma SAS de conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço ou delegação de usuário SAS também estão disponíveis através de uma conta SAS. Além disso, com a conta SAS, você pode delegar o acesso às operações que se aplicam ao nível do serviço, como as propriedades de **serviço Get/Set** e as operações **Get/Set Service Stats.** Você também pode delegar acesso a operações de leitura, gravação e exclusão em contêineres de blob, tabelas, filas e compartilhamentos de arquivos que não são permitidos com um SAS de serviço. 

    Para obter mais informações sobre a conta SAS, [crie uma conta SAS (Rest API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que você use credenciais Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave da conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhadas para acesso ao armazenamento Blob, use as credenciais do Azure AD para criar um SAS de delegação de usuário quando possível para uma segurança superior.

Uma assinatura de acesso compartilhado pode assumir uma destas duas formas:

- **Ad hoc SAS:** Quando você cria um SAS ad hoc, o tempo de início, o tempo de validade e as permissões para o SAS são todos especificados no Uri SAS (ou implícito, se o tempo de início for omitido). Qualquer tipo de SAS pode ser um SAS ad hoc.
- **SAS de serviço com política de acesso armazenada:** Uma política de acesso armazenado é definida em um contêiner de recursos, que pode ser um contêiner blob, tabela, fila ou compartilhamento de arquivos. A política de acesso armazenado pode ser usada para gerenciar restrições para uma ou mais assinaturas de acesso compartilhadas de serviço. Quando você associa um SAS de serviço a uma política&mdash;de acesso armazenada, o SAS&mdash;herda as restrições do tempo de início, do tempo de validade e das permissões definidas para a política de acesso armazenado.

> [!NOTE]
> Uma delegação de usuário SAS ou uma conta SAS deve ser um SAS ad hoc. As políticas de acesso armazenadas não são suportadas para a delegação de usuários SAS ou para a conta SAS.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave que foi usada para criar o SAS. Esta assinatura é usada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

### <a name="sas-signature"></a>Assinatura SAS

Você pode assinar um SAS de duas maneiras:

- Com uma *chave de delegação de usuário* que foi criada usando credenciais do Azure Active Directory (Azure AD). Uma delegação de usuários SAS é assinada com a chave de delegação de usuários.

    Para obter a chave de delegação do usuário e criar o SAS, um princípio de segurança Azure AD deve ser atribuído a uma função RBAC (Role-based Access Control, controle de acesso baseado em função) que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** action. Para obter informações detalhadas sobre as funções RBAC com permissões para obter a chave de delegação do usuário, consulte [Criar uma delegação de usuários SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Com a chave da conta de armazenamento. Tanto um SAS de serviço quanto um SAS de conta são assinados com a chave da conta de armazenamento. Para criar um SAS assinado com a chave da conta, um aplicativo deve ter acesso à chave da conta.

### <a name="sas-token"></a>Token SAS

O token SAS é uma string que você gera no lado do cliente, por exemplo, usando uma das bibliotecas de clientes do Azure Storage. O token SAS não é rastreado pelo Azure Storage de forma alguma. Você pode criar um número ilimitado de tokens SAS no lado do cliente. Depois de criar um SAS, você pode distribuí-lo para aplicativos clientes que requerem acesso a recursos em sua conta de armazenamento.

Quando um aplicativo cliente fornece um Uri SAS para o Armazenamento Azure como parte de uma solicitação, o serviço verifica os parâmetros e a assinatura do SAS para verificar se ele é válido para autorizar a solicitação. Se o serviço verificar que a assinatura é válida, a solicitação será autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

Aqui está um exemplo de um serviço SAS URI, mostrando o recurso URI e o token SAS:

![Componentes de um serviço SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso compartilhada

Use um SAS quando quiser fornecer acesso seguro aos recursos em sua conta de armazenamento para qualquer cliente que não tenha permissões para esses recursos.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:

1. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, a criação de um serviço que possa ser dimensionado de acordo com a demanda pode ser difícil ou dispendiosa.

   ![Diagrama do cenário: serviço de proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Um serviço leve autentica o cliente conforme necessário e gera uma SAS. Uma vez que o aplicativo cliente recebe o SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pelo SAS e para o intervalo permitido pelo SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

   ![Diagrama do cenário: serviço do provedor de SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços reais podem usar uma combinação dessas duas abordagens. Por exemplo, alguns dados podem ser processados e validados por meio do proxy front-end, enquanto outros são salvos e/ou lidos diretamente usando SAS.

Além disso, é necessário um SAS para autorizar o acesso ao objeto de origem em uma operação de cópia em determinados cenários:

- Quando você copia um blob para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao blob de origem. Outra opção é usar uma SAS para também autorizar acesso ao blob de destino.
- Quando você copia um arquivo para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao arquivo de origem. Outra opção é usar uma SAS para também autorizar acesso ao arquivo de destino.
- Quando você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma SAS para autorizar acesso ao objeto de origem, mesmo se os objetos de origem e destino residirem dentro da mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.
- Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor que estiver realizando um ataque intermediário poderá lê-la e, em seguida, usá-la exatamente como o usuário previsto, o que poderá comprometer dados confidenciais ou gerar dados corrompidos pelo usuário mal-intencionado.
- **Use uma delegação de usuários SAS quando possível.** Uma delegação de usuários SAS fornece segurança superior a um SAS de serviço ou a uma conta SAS. Uma delegação de usuário SAS é protegida com credenciais Azure AD, de modo que você não precisa armazenar sua chave de conta com o seu código.
- **Tenha um plano de revogação para um SAS.** Certifique-se de que está preparado para responder se um SAS estiver comprometido.
- **Defina uma política de acesso armazenada para um SAS de serviço.** As políticas de acesso armazenadas lhe dão a opção de revogar permissões para um SAS de serviço sem ter que regenerar as chaves da conta de armazenamento. Defina o vencimento para um momento muito distante no futuro (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data ainda mais além no futuro.
- **Use tempos de expiração de curto prazo em um sas serviço SAS ad hoc ou conta SAS.** Dessa forma, mesmo se uma SAS for comprometida, ela será válida apenas por um breve período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Períodos de vencimento breves também limitam a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.
- **Faça com que os clientes renovem a SAS automaticamente, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para que haja tempo para novas tentativas, caso o serviço que fornece a SAS não esteja disponível. Se o SAS se destinar a ser usado para um pequeno número de operações imediatas e de curta duração que devem ser concluídas no período de expiração, isso poderá ser desnecessário, pois não se espera que a SAS seja renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração consiste em equilibrar a necessidade de curta duração da SAS (como mencionado acima) com a necessidade de garantir que o cliente solicite renovação com antecedência suficiente (para evitar uma interrupção devido ao vencimento da SAS antes de uma renovação bem-sucedida).
- **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS para **agora**, poderão ser observadas falhas intermitentemente para os primeiros minutos devido à defasagem horária (diferenças na hora atual de acordo com computadores diferentes). Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, simplesmente não a defina, o que a tornará imediatamente válida em todos os casos. Em geral, o mesmo também se aplica à hora de vencimento – lembre-se de que pode haver até 15 minutos de defasagem horária em um dos dois sentidos em qualquer solicitação. Para os clientes que usam uma versão de REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é 1 hora, e qualquer política que especifique um período maior do que esse falhará.
- **Tenha cuidado com o formato de data-hora SAS.** Se você definir a hora de início e/ou o vencimento de um SAS, para alguns utilitários (por exemplo, para o utilitário de linha de comando AzCopy) você precisa que o formato de data-hora seja '+%Y-%d-%dT%H:%M:%SZ', incluindo especificamente os segundos para que ele funcione usando o token SAS.  
- **Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança consiste em fornecer os privilégios mínimos necessários a um usuário. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a reduzir o dano caso uma SAS seja comprometida, pois a SAS terá menos poder nas mãos de um invasor.
- **Entenda que sua conta será cobrada por qualquer uso, inclusive através de um SAS.** Se você fornecer acesso à gravação a uma bolha, um usuário pode optar por carregar uma bolha de 200 GB. Se você também tiver concedido o acesso de leitura, será possível baixá-la 10 vezes, incorrendo em 2 TB de custos de saída. Como mencionado, forneça permissões limitadas para ajudar a reduzir a ações em potencial de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).
- **Valide os dados gravados usando um SAS.**  Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se o seu aplicativo necessitar de que esses dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá realizar essa validação depois que os dados forem gravados e antes que eles sejam usados pelo seu aplicativo. Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.
- **Saiba quando não usar um SAS.** Às vezes, os riscos associados a uma determinada operação contra sua conta de armazenamento superam os benefícios de usar um SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras. Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.
- **Use registros de armazenamento azure monitor e azure para monitorar seu aplicativo.** Você pode usar o registro de análise do Monitor e do armazenamento do Azure para observar qualquer pico de falhas de autorização devido a uma paralisação no serviço do provedor SAS ou à remoção inadvertida de uma política de acesso armazenada. Para obter mais informações, consulte [as métricas do Azure Storage no registro de registro do Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e do [Azure Storage Analytics](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Comece com o SAS

Para começar com assinaturas de acesso compartilhadas, consulte os seguintes artigos para cada tipo de SAS.

### <a name="user-delegation-sas"></a>Delegação de usuários SAS

- [Crie uma delegação de usuários SAS para um contêiner ou blob com powerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Crie uma delegação de usuários SAS para um contêiner ou blob com o Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Crie uma delegação de usuário SAS para um contêiner ou blob com .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Serviço SAS

- [Crie um SAS de serviço para um contêiner ou blob com .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Conta SAS

- [Crie uma conta SAS com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Próximas etapas

- [Delegar acesso com uma assinatura de acesso compartilhado (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Crie uma delegação de usuários SAS (Rest API)](/rest/api/storageservices/create-user-delegation-sas)
- [Crie um Serviço SAS (API REST)](/rest/api/storageservices/create-service-sas)
- [Crie uma conta SAS (API REST)](/rest/api/storageservices/create-account-sas)
