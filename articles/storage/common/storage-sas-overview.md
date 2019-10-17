---
title: Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)
description: Saiba mais sobre como usar SAS (assinaturas de acesso compartilhado) para delegar acesso aos recursos de armazenamento do Azure, incluindo BLOBs, filas, tabelas e arquivos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9623152bdea5cc56e6b9bcb7d9911a730fd7a4a4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72382008"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)

Uma SAS (assinatura de acesso compartilhado) fornece acesso delegado seguro aos recursos em sua conta de armazenamento sem comprometer a segurança de seus dados. Com uma SAS, você tem controle granular sobre como um cliente pode acessar seus dados. Você pode controlar quais recursos o cliente pode acessar, quais permissões eles têm sobre esses recursos e por quanto tempo a SAS é válida, entre outros parâmetros.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de Acesso compartilhado.

O armazenamento do Azure dá suporte a três tipos de assinaturas de acesso compartilhado:

- **SAS de delegação de usuário (visualização).** Uma SAS de delegação de usuário é protegida com as credenciais do Azure Active Directory (AD do Azure) e também pelas permissões especificadas para a SAS. Uma SAS de delegação de usuário se aplica somente ao armazenamento de BLOB.

    Para obter mais informações sobre a SAS de delegação de usuário, consulte [criar uma delegação de usuário de SAS (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- **SAS de Serviço.** Uma SAS de serviço é protegida com a chave da conta de armazenamento. Uma SAS de serviço Delega acesso a um recurso em apenas um dos serviços de armazenamento do Azure: armazenamento de BLOBs, armazenamento de filas, armazenamento de tabelas ou arquivos do Azure. 

    Para obter mais informações sobre a SAS do serviço, consulte [criar um serviço SAS (API REST)](/rest/api/storageservices/create-service-sas).

- **SAS de Conta.** Uma SAS de conta é protegida com a chave da conta de armazenamento. Uma SAS de conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis por meio de uma SAS de delegação de serviço ou de usuário também estão disponíveis por meio de uma SAS de conta. Além disso, com a SAS da conta, você pode delegar acesso a operações que se aplicam ao nível do serviço, como **obter/definir propriedades de serviço** e obter as operações de **Estatísticas de serviço** . Você também pode delegar acesso a operações de leitura, gravação e exclusão em contêineres de blob, tabelas, filas e compartilhamentos de arquivos que não são permitidos com um SAS de serviço. 

    Para obter mais informações sobre a SAS da conta, [crie uma SAS da conta (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhado para acesso ao armazenamento de BLOB, use as credenciais do Azure AD para criar uma SAS de delegação de usuário quando possível para segurança superior.

Uma assinatura de acesso compartilhado pode assumir uma destas duas formas:

- **SAS ad hoc:** Quando você cria uma SAS ad hoc, a hora de início, a hora de expiração e as permissões para a SAS são todas especificadas no URI de SAS (ou implícita, se a hora de início for omitida). Qualquer tipo de SAS pode ser uma SAS ad hoc.
- **SAS de serviço com política de acesso armazenada:** Uma política de acesso armazenada é definida em um contêiner de recursos, que pode ser um contêiner de BLOBs, uma tabela, uma fila ou um compartilhamento de arquivos. A política de acesso armazenada pode ser usada para gerenciar restrições para uma ou mais assinaturas de acesso compartilhado do serviço. Quando você associa uma SAS de serviço a uma política de acesso armazenada, a SAS herda as restrições @ no__t-0the hora de início, hora de expiração e permissões @ no__t-1defined para a política de acesso armazenada.

> [!NOTE]
> Uma SAS de delegação de usuário ou uma SAS de conta deve ser uma SAS ad hoc. As políticas de acesso armazenadas não têm suporte para a SAS de delegação de usuário ou a SAS da conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros de SAS e assinado com a chave que foi usada para criar a SAS. Esta assinatura é usada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

### <a name="sas-signature"></a>Assinatura SAS

Você pode assinar uma SAS de uma das duas maneiras:

- Com uma *chave de delegação de usuário* que foi criada usando as credenciais do Azure Active Directory (AD do Azure). Uma SAS de delegação de usuário é assinada com a chave de delegação de usuário.

    Para obter a chave de delegação de usuário e criar a SAS, uma entidade de segurança do Azure AD deve ser atribuída a uma função de RBAC (controle de acesso baseado em função) que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Para obter informações detalhadas sobre as funções RBAC com permissões para obter a chave de delegação de usuário, consulte [criar uma delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- Com a chave da conta de armazenamento. Uma SAS de serviço e uma SAS de conta são assinadas com a chave da conta de armazenamento. Para criar uma SAS que é assinada com a chave de conta, um aplicativo deve ter acesso à chave de conta.

### <a name="sas-token"></a>Token SAS

O token SAS é uma cadeia de caracteres que você gera no lado do cliente, por exemplo, usando uma das bibliotecas de cliente do armazenamento do Azure. O token SAS não é acompanhado pelo armazenamento do Azure de nenhuma forma. Você pode criar um número ilimitado de tokens SAS no lado do cliente. Depois de criar uma SAS, você pode distribuí-la aos aplicativos cliente que exigem acesso aos recursos em sua conta de armazenamento.

Quando um aplicativo cliente fornece um URI de SAS para o armazenamento do Azure como parte de uma solicitação, o serviço verifica os parâmetros SAS e a assinatura para verificar se ele é válido para autorizar a solicitação. Se o serviço verificar que a assinatura é válida, a solicitação será autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

Aqui está um exemplo de um URI de SAS de serviço, mostrando o URI de recurso e o token SAS:

![Componentes de um URI de SAS de serviço](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso compartilhado

Use uma SAS quando desejar fornecer acesso seguro aos recursos em sua conta de armazenamento para qualquer cliente que não tenha permissões para esses recursos.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:

1. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, a criação de um serviço que possa ser dimensionado de acordo com a demanda pode ser difícil ou dispendiosa.

   ![Diagrama do cenário: serviço de proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Um serviço leve autentica o cliente conforme necessário e gera uma SAS. Depois que o aplicativo cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

   ![Diagrama do cenário: serviço do provedor de SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços reais podem usar uma combinação dessas duas abordagens. Por exemplo, alguns dados podem ser processados e validados por meio do proxy front-end, enquanto outros são salvos e/ou lidos diretamente usando SAS.

Além disso, uma SAS é necessária para autorizar o acesso ao objeto de origem em uma operação de cópia em determinados cenários:

- Quando você copia um blob para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao blob de origem. Outra opção é usar uma SAS para também autorizar acesso ao blob de destino.
- Quando você copia um arquivo para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao arquivo de origem. Outra opção é usar uma SAS para também autorizar acesso ao arquivo de destino.
- Quando você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma SAS para autorizar acesso ao objeto de origem, mesmo se os objetos de origem e destino residirem dentro da mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.
- Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor que estiver realizando um ataque intermediário poderá lê-la e, em seguida, usá-la exatamente como o usuário previsto, o que poderá comprometer dados confidenciais ou gerar dados corrompidos pelo usuário mal-intencionado.
- **Use uma SAS de delegação de usuário quando possível.** Uma SAS de delegação de usuário fornece segurança superior a uma SAS de serviço ou a uma SAS de conta. Uma SAS de delegação de usuário é protegida com as credenciais do Azure AD, para que você não precise armazenar sua chave de conta com seu código.
- **Ter um plano de revogação em vigor para uma SAS.** Verifique se você está preparado para responder se uma SAS está comprometida.
- **Defina uma política de acesso armazenada para uma SAS de serviço.** As políticas de acesso armazenadas oferecem a opção de revogar permissões para uma SAS de serviço sem a necessidade de regenerar as chaves da conta de armazenamento. Defina o vencimento para um momento muito distante no futuro (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data ainda mais além no futuro.
- **Use tempos de expiração de curto prazo em uma SAS do serviço SAS ad hoc ou SAS da conta.** Dessa forma, mesmo se uma SAS for comprometida, ela será válida apenas por um breve período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Períodos de vencimento breves também limitam a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.
- **Faça com que os clientes renovem a SAS automaticamente, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para que haja tempo para novas tentativas, caso o serviço que fornece a SAS não esteja disponível. Se o SAS se destinar a ser usado para um pequeno número de operações imediatas e de curta duração que devem ser concluídas no período de expiração, isso poderá ser desnecessário, pois não se espera que a SAS seja renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração consiste em equilibrar a necessidade de curta duração da SAS (como mencionado acima) com a necessidade de garantir que o cliente solicite renovação com antecedência suficiente (para evitar uma interrupção devido ao vencimento da SAS antes de uma renovação bem-sucedida).
- **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS para **agora**, poderão ser observadas falhas intermitentemente para os primeiros minutos devido à defasagem horária (diferenças na hora atual de acordo com computadores diferentes). Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, simplesmente não a defina, o que a tornará imediatamente válida em todos os casos. Em geral, o mesmo também se aplica à hora de vencimento – lembre-se de que pode haver até 15 minutos de defasagem horária em um dos dois sentidos em qualquer solicitação. Para os clientes que usam uma versão de REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é 1 hora, e qualquer política que especifique um período maior do que esse falhará.
- **Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança consiste em fornecer os privilégios mínimos necessários a um usuário. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a reduzir o dano caso uma SAS seja comprometida, pois a SAS terá menos poder nas mãos de um invasor.
- **Entenda que sua conta será cobrada por qualquer uso, incluindo por meio de uma SAS.** Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB. Se você também tiver concedido o acesso de leitura, será possível baixá-la 10 vezes, incorrendo em 2 TB de custos de saída. Como mencionado, forneça permissões limitadas para ajudar a reduzir a ações em potencial de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).
- **Validar os dados gravados usando uma SAS.** Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se o seu aplicativo necessitar de que esses dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá realizar essa validação depois que os dados forem gravados e antes que eles sejam usados pelo seu aplicativo. Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.
- **Saiba quando não usar uma SAS.** Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios do uso de uma SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras. Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.
- **Use Azure Monitor e logs de armazenamento do Azure para monitorar seu aplicativo.** Você pode usar o Azure Monitor e o log da análise de armazenamento para observar qualquer pico nas falhas de autorização devido a uma interrupção no serviço do provedor SAS ou à remoção inadvertida de uma política de acesso armazenada. Para obter mais informações, consulte [métricas de armazenamento do Azure em log de Azure monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [análise de armazenamento do Azure](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introdução à SAS

Para começar a usar as assinaturas de acesso compartilhado, consulte os artigos a seguir para cada tipo de SAS.

### <a name="user-delegation-sas"></a>SAS de delegação de usuário

- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com a CLI do Azure (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS do serviço

- [Criar uma SAS de serviço para um contêiner ou BLOB com .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>SAS da conta

- [Criar uma SAS de conta com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Próximos passos

- [Delegar acesso com uma assinatura de acesso compartilhado (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar uma SAS de serviço (API REST)](/rest/api/storageservices/create-service-sas)
- [Criar uma SAS de conta (API REST)](/rest/api/storageservices/create-account-sas)
