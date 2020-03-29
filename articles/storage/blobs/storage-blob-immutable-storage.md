---
title: Armazenamento de bolhas imutável - Armazenamento Azure
description: O Armazenamento do Microsoft Azure oferece suporte WORM (Gravar uma vez, reutilizar frequentemente) para o armazenamento de Blob (objeto) que possibilita que os usuários armazenem dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367611"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Armazene dados de bolha críticos para os negócios com armazenamento imutável

O armazenamento imutável para armazenamento Azure Blob permite que os usuários armazenem objetos de dados essenciais para os negócios em um estado WORM (Write Once, Read Many). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante o intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou excluídas. O armazenamento imutável está disponível para contas v1, v2 de uso geral, BlobStorage e BlockBlobStorage em todas as regiões do Azure.

Para obter informações sobre como definir e limpar os meios legais ou criar uma política de retenção baseada no tempo usando o portal Azure, PowerShell ou Azure CLI, consulte [Definir e gerenciar políticas de imutabilidade para armazenamento Blob](storage-blob-immutability-policies-manage.md).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Sobre o armazenamento blob imutável

O armazenamento imutável ajuda a organização de saúde, instituições financeiras e indústrias relacionadas,&mdash;particularmente organizações&mdash;de corretores, a armazenar dados com segurança. O armazenamento imutável também pode ser aproveitado em qualquer cenário para proteger dados críticos contra modificações ou exclusões.

Os aplicativos típicos incluem:

- **Conformidade regulatória**: armazenamento imutável para armazenamento de Blobs do Azure ajuda as organizações a atender às regulamentações SEC 17a-4 (f), CFTC 1.31 (d), FINRA e outras. Um whitepaper técnico da Cohasset Associates detalha como o armazenamento imutável aborda esses requisitos regulatórios pode ser baixado através do [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). O [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre nossas certificações de conformidade.

- **Retenção segura de documentos**: O armazenamento imutável para o armazenamento Do Azure Blob garante que os dados não podem ser modificados ou excluídos por qualquer usuário, incluindo usuários com privilégios administrativos da conta.

- **Porção legal**: O armazenamento imutável para armazenamento Azure Blob permite que os usuários armazenem informações confidenciais que são essenciais para litígios ou uso comercial em um estado à prova de adulteração durante a duração desejada até que o porto seja removido. Esse recurso não se limita apenas a casos de uso legal, mas também pode ser considerado como um hold baseado em eventos ou um bloqueio corporativo, onde a necessidade de proteger dados com base em gatilhos de eventos ou políticas corporativas é necessária.

O armazenamento imutável suporta os seguintes recursos:

- **[Suporte à política de retenção baseada no tempo](#time-based-retention-policies)**: Os usuários podem definir políticas para armazenar dados por um intervalo especificado. Quando uma política de retenção baseada no tempo é definida, os blobs podem ser criados e lidos, mas não modificados ou excluídos. Depois que o período de retenção expirar, as bolhas podem ser excluídas, mas não substituídas.

- **[Suporte à política de retenção legal](#legal-holds)**: Se o intervalo de retenção não for conhecido, os usuários podem definir os meios legais para armazenar dados imutáveis até que o porto legal seja liberado.  Quando uma política de reter legalmente é definida, blobs podem ser criados e lidos, mas não modificados ou excluídos. Cada reter legal está associado a uma tag alfanumérica definida pelo usuário (como um ID de caso, nome do evento, etc.) que é usada como uma seqüência de identificadores. 

- **Suporte para todos os níveis de blob**: as políticas do WORM são independentes da camada de armazenamento do Azure Blob e se aplicam a todas as camadas: quente, legal e arquivamento. Os usuários podem fazer a transição dos dados de suas cargas de trabalho para a camada de custo mais otimizado, mantendo a imutabilidade dos dados.

- **Configuração no nível do contêiner**: os usuários podem configurar políticas de retenção com base no tempo e marcas de retenção legal no nível do contêiner. Ao usar configurações simples no nível do contêiner, os usuários podem criar e bloquear políticas de retenção baseadas em tempo, estender intervalos de retenção, definir e limpar retenção legal, entre outros. Essas políticas se aplicam a todos os blobs no contêiner, existentes e novos.

- **Suporte de registro de auditoria**: Cada contêiner inclui um registro de auditoria de políticas. Ele mostra até sete comandos de retenção baseados no tempo para políticas de retenção baseadas em tempo bloqueado e contém o ID do usuário, o tipo de comando, os carimbos de tempo e o intervalo de retenção. Para retenções legais, o log contém as identificações de ID do usuário, tipo de comando, carimbos de tempo e retenção legal. Este registro é retido durante toda a vida da apólice, de acordo com as diretrizes regulatórias da SEC 17a-4(f). O [Registro de Atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) mostra um registro mais abrangente de todas as atividades do avião de controle; enquanto habilita [o Azure Diagnostic Logs](../../azure-monitor/platform/platform-logs-overview.md) retém e mostra as operações do plano de dados. É responsabilidade do usuário armazenar esses logs de forma persistente, conforme o necessário para regulamentações ou outros fins.

## <a name="how-it-works"></a>Como ele funciona

O armazenamento imutável para armazenamento de Blobs do Azure oferece suporte a dois tipos de políticas WORM ou imutáveis: retenção baseada em tempo e retenções legais. Quando uma política de retenção baseada no tempo ou retenção legal é aplicada em um contêiner, todas as bolhas existentes se movem para um estado WORM imutável em menos de 30 segundos. Todas as novas bolhas que são carregadas para esse contêiner protegido por apólice também se moverão para um estado imutável. Uma vez que todas as bolhas estejam em um estado imutável, a política imutável é confirmada e quaisquer operações de sobregravação ou exclusão no contêiner imutável não são permitidas.

A exclusão de contas de contêiner e armazenamento também não é permitida se houver quaisquer bolhas em um contêiner que estejam protegidas por uma redução legal ou uma política baseada no tempo bloqueado. Uma política de reter legalmente protegerá contra a exclusão de contas de blob, contêiner e armazenamento. As políticas desbloqueadas e bloqueadas de tempo protegerão contra a exclusão de bolhas pelo tempo especificado. As políticas desbloqueadas e bloqueadas somente protegerão contra a exclusão de contêineres se houver pelo menos uma bolha dentro do contêiner. Apenas um contêiner com política baseada em tempo *bloqueado* protegerá contra exclusões de contas de armazenamento; contêineres com políticas baseadas no tempo desbloqueados não oferecem proteção de exclusão de contas de armazenamento nem conformidade.

Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas no tempo, consulte [Definir e gerenciar políticas de imutabilidade para armazenamento Blob](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Políticas de retenção baseadas em tempo

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve ser *bloqueada* para que a bolha esteja em um estado imutável (gravar e excluir protegido) compatível para a SEC 17a-4(f) e outras conformidades normativa. Recomendamos que você bloqueie a apólice em uma quantidade razoável de tempo, normalmente menos de 24 horas. O estado inicial de uma política de retenção baseada em tempo aplicada é *desbloqueado,* permitindo que você teste o recurso e faça alterações na diretiva antes de bloqueá-lo. Embora o estado *desbloqueado* forneça proteção contra imutabilidade, não recomendamos usar o estado *desbloqueado* para qualquer finalidade que não seja testes de recursos de curto prazo. 

Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os blobs no contêiner ficará no estado imutável durante o *efetivo* período de retenção. O período de retenção efetivo para blobs é igual à diferença entre o tempo de **criação** da bolha e o intervalo de retenção especificado pelo usuário. Como os usuários podem estender o intervalo de retenção, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

Por exemplo, suponha que um usuário crie uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos. Uma bolha existente naquele recipiente, _testblob1,_ foi criada há um ano; assim, o período de retenção efetivo para _testblob1_ é de quatro anos. Quando uma nova bolha, _testblob2_, é carregada no recipiente, o período de retenção efetivo para o _testblob2_ é de cinco anos a partir do momento de sua criação.

Uma política de retenção baseada em tempo desbloqueada é recomendada apenas para testes de recursos e uma política deve ser bloqueada para estar em conformidade com a SEC 17a-4(f) e outras conformidades normativa. Uma vez que uma política de retenção baseada em tempo é bloqueada, a diretiva não pode ser removida e um máximo de cinco aumentos para o período de retenção efetivo é permitido.

Os seguintes limites se aplicam às políticas de retenção:

- Para uma conta de armazenamento, o número máximo de contêineres com políticas imutáveis baseadas em tempo bloqueado é de 10.000.
- O intervalo mínimo de retenção é de 1 dia. O máximo é de 146.000 dias (400 anos).
- Para um contêiner, o número máximo de edições para estender um intervalo de retenção para políticas imutáveis baseadas em tempo bloqueado é 5.
- Para um contêiner, um máximo de sete logs de auditoria de política de retenção baseados em tempo são retidos para uma política bloqueada.

### <a name="allow-protected-append-blobs-writes"></a>Permitir que as bolhas de apêndice protegidas escrevam

Os blobs de apêndice são compostos por blocos de dados e otimizados para operações de apêndice de dados exigidas por cenários de auditoria e registro. Pelo design, as bolhas de apêndice só permitem a adição de novos blocos até o final da bolha. Independentemente da imutabilidade, modificação ou exclusão de blocos existentes em uma bolha de apêndice não é fundamentalmente permitida. Para saber mais sobre apêndice slobs, consulte [About Append Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Apenas as políticas de `allowProtectedAppendWrites` retenção baseadas em tempo têm uma configuração que permite escrever novos blocos para uma bolha de apêndice, mantendo a proteção e a conformidade da imutabilidade. Se ativado, você pode criar uma bolha de apêndice diretamente no contêiner protegido por políticas e continuar a adicionar novos blocos de dados ao fim dos blobs de apêndice existentes usando a API *do AppendBlock.* Apenas novos blocos podem ser adicionados e quaisquer blocos existentes não podem ser modificados ou excluídos. A proteção contra imutabilidade de retenção de tempo ainda se aplica, impedindo a exclusão da bolha do apêndice até que o período de retenção efetivo tenha transcorrido. Ativar essa configuração não afeta o comportamento de imutabilidade de blobs de bloco ou blobs de página.

Como essa configuração faz parte de uma política de retenção baseada no tempo, as bolhas de apêndice ainda permanecem no estado imutável durante o período de retenção *efetivo.* Uma vez que novos dados podem ser anexados além da criação inicial do bolha de apêndice, há uma pequena diferença na forma como o período de retenção é determinado. A retenção efetiva é a diferença entre o tempo de última modificação do **apêndice** e o intervalo de retenção especificado pelo usuário. Da mesma forma, quando o intervalo de retenção é estendido, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

Por exemplo, suponha que um usuário `allowProtectedAppendWrites` crie uma política de retenção baseada no tempo com um intervalo de retenção ativado e um intervalo de retenção de 90 dias. Um bolha de apêndice, _logblob1_, é criado no contêiner hoje, novos logs continuam a ser adicionados ao bolha de apêndice pelos próximos 10 dias; assim, o período de retenção efetivo para o _logblob1_ é de 100 dias a partir de hoje (o tempo de seu último apêndice + 90 dias).

As políticas de retenção `allowProtectedAppendWrites` baseadas em tempo desbloqueadas permitem que a configuração seja ativada e desativada a qualquer momento. Uma vez que a diretiva de `allowProtectedAppendWrites` retenção baseada no tempo esteja bloqueada, a configuração não pode ser alterada.

As políticas de `allowProtectedAppendWrites` retenção legal não podem ser habilitadas e quaisquer detenções legais anularão a propriedade 'allowProtectedAppendWrites'. Se uma retenção legal for aplicada a `allowProtectedAppendWrites` uma política de retenção baseada em tempo com ativada, a API do *AppendBlock* falhará até que a suspensão legal seja levantada.

## <a name="legal-holds"></a>Retenções legais

Os atenções legais são de responsabilidade temporária que podem ser usados para fins de investigação legal ou políticas gerais de proteção. Cada política de deter legal precisa ser associada a uma ou mais tags. As tags são usadas como um identificador nomeado, como um ID de caso ou evento, para categorizar e descrever o propósito do hold.

Um contêiner pode ter uma retenção legal e uma política de retenção baseada no tempo ao mesmo tempo. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido compensadas.

Os seguintes limites se aplicam aos porões legais:

- Para uma conta de armazenamento, o número máximo de contêineres com uma configuração de reter legal é de 10.000.
- Para um contêiner, o número máximo de tags de retenção legal é 10.
- O comprimento mínimo de uma tag de reter legal é de três caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contêiner, um máximo de 10 registros de auditoria de política de retenção legal são retidos durante a duração da apólice.

## <a name="scenarios"></a>Cenários
A tabela a seguir mostra os tipos de operações de armazenamento Blob que estão desativadas para os diferentes cenários imutáveis. Para obter mais informações, consulte a documentação da [API do Azure Blob Service REST.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

|Cenário  |Estado do blob  |Operações blob negadas  |Proteção de contêineres e contas
|---------|---------|---------|---------|
|O intervalo efetivo de retenção no blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra exclusão e gravação         | Coloque Blob<sup>1,</sup>Coloque bloco<sup>1,</sup>coloque a lista de<sup>blocos 1,</sup>exclua recipiente, exclua blob, defina metadados blob, coloque página, defina propriedades blob, bolha de instantâneo, bolha de cópia incremental, bloco de apêndice<sup>2</sup>         |Exclusão de contêineres negada; Exclusão da conta de armazenamento negada         |
|O intervalo de retenção efetivo na bolha expirou e nenhuma retenção legal foi definida    |Protegido apenas contra gravação  (operações de exclusão são permitidas)         |Coloque Blob<sup>1,</sup>Coloque bloco<sup>1,</sup>coloque a lista de<sup>blocos 1,</sup>defina metadados blob, coloque a página, defina as propriedades do blob, blob de snapshot, blob de cópia incremental, bloco de apêndice<sup>2</sup>         |Exclusão de contêineres negada se houver pelo menos 1 bolha dentro de recipiente protegido; Exclusão da conta de armazenamento negada apenas para políticas baseadas em tempo *bloqueado*         |
|Nenhuma política WORM aplicada (sem retenção baseada em tempo e sem marca de retenção legal)     |Mutável         |Nenhum         |Nenhum         |

<sup>1</sup> O serviço blob permite que essas operações criem uma nova bolha uma vez. Não são permitidas todas as operações subseqüentes de sobregravação em um caminho de bolha existente em um contêiner imutável.

<sup>2</sup> O Bloco de Apêndice só `allowProtectedAppendWrites` é permitido para políticas de retenção baseadas em tempo com a propriedade ativada. Para obter mais informações, consulte a [seção Permitir apêndice slobs de apêndice protegido.](#allow-protected-append-blobs-writes)

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso. Os dados imutáveis são precificados da mesma forma que os dados mutáveis. Para obter detalhes sobre os detalhes de preços sobre o armazenamento Do Azure Blob, consulte a [página de preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Perguntas frequentes

**Você pode fornecer documentação de conformidade com worm?**

Sim. Para documentar a conformidade, a Microsoft contratou uma empresa líder de avaliação independente especializada em gerenciamento de registros e governança de informações, a Cohasset Associates, para avaliar o armazenamento blob imutável e sua conformidade com requisitos específicos para o indústria de serviços financeiros. A Cohasset validou que o armazenamento blob imutável, quando usado para reter Blobs baseados em tempo em um estado WORM, atende aos requisitos de armazenamento relevantes da Regra 1.31(c)-(d), da Regra FINRA 4511 e da Regra 17a-4 da SEC. A Microsoft tem como alvo esse conjunto de regras, pois representam a orientação mais prescritiva global para a retenção de registros para instituições financeiras. O relatório Cohasset está disponível no [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade com a imutabilidade do WORM, entre em contato com o suporte do Azure.

**O recurso se aplica apenas a bolhas de bloco e bolhas de apêndice, ou a blobs de página também?**

O armazenamento imutável pode ser usado com qualquer tipo de bolha, pois é definido no nível do contêiner, mas recomendamos que você use WORM para recipientes que armazenam principalmente bolhas de bloco e bolhas de apêndice. As bolhas existentes em um contêiner serão protegidas por uma política WORM recém-definida. Mas qualquer novo blobs de página precisa ser criado fora do contêiner WORM e, em seguida, copiado dentro Uma vez copiado em um recipiente WORM, não são permitidas alterações adicionais em uma bolha de página. A definição de uma política WORM em um contêiner que armazena VHDs (blobs de página) para quaisquer máquinas virtuais ativas é desencorajada, pois bloqueará o disco VM. Recomendamos que você revise minuciosamente a documentação e teste seus cenários antes de bloquear quaisquer políticas baseadas no tempo.

**Preciso criar uma nova conta de armazenamento para usar esse recurso?**

Não, você pode usar armazenamento imutável com quaisquer contas v1, v2 de uso geral, BlobStorage ou BlockBlobStorage existentes ou recém-criadas. Contas de armazenamento v1 de uso geral são suportadas, mas recomendamos a atualização para o v2 de uso geral, de forma que você possa tirar proveito de mais recursos. Para obter informações sobre como atualizar uma conta de armazenamento v1 de uso geral existente, consulte [Atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

**Posso aplicar uma política legal de retenção e de retenção baseada em tempo?**

Sim, um contêiner pode ter uma retenção legal e uma política de retenção baseada no tempo ao mesmo tempo; no entanto, a configuração 'allowProtectedAppendWrites' não se aplicará até que a recarga legal seja liberada. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido compensadas. 

**As políticas legais são apenas para processos judiciais ou existem outros cenários de uso?**

Não, Legal Hold é apenas o termo geral usado para uma política de retenção não baseada em tempo. Não precisa ser usado apenas para processos judiciais. As políticas de Detenção Legal são úteis para desativar a sobregravação e as exclusões para proteger dados worm susforos corporativos importantes, onde o período de retenção é desconhecido. Você pode usá-la como uma política corporativa para proteger as cargas de trabalho críticas do WORM da missão ou usá-la como uma política de preparação antes que um gatilho de evento personalizado exija o uso de uma política de retenção baseada no tempo. 

**Posso remover uma política de retenção baseada em tempo _bloqueado_ ou uma retenção legal?**

Somente as políticas de retenção baseadas em tempo desbloqueadas podem ser removidas de um contêiner. Uma vez que uma política de retenção baseada em tempo é bloqueada, ela não pode ser removida; apenas extensões efetivas do período de retenção são permitidas. As etiquetas de reter legais podem ser excluídas. Quando todas as tags legais são excluídas, o porão legal é removido.

**O que acontece se eu tentar excluir um contêiner com uma política de retenção baseada no tempo ou um porão legal?**

A operação Excluir contêiner falhará se houver pelo menos uma bolha dentro do contêiner com uma política de retenção baseada em tempo bloqueada ou desbloqueada ou se o contêiner tiver uma retenção legal. A operação Delete Container só terá sucesso se não houver bolhas dentro do contêiner e não houver retenções legais. 

**O que acontece se eu tentar excluir uma conta de armazenamento com um contêiner que tenha uma política de retenção baseada em tempo ou retenção legal?**

A exclusão da conta de armazenamento falhará se houver pelo menos um contêiner com um conjunto de reter legal ou uma política baseada em tempo **bloqueado.** Um contêiner com uma diretiva desbloqueada baseada no tempo não protege contra a exclusão da conta de armazenamento. Você deve remover todos os caderis legais e excluir todos os recipientes **bloqueados** antes de poder excluir a conta de armazenamento. Para obter informações sobre exclusão de contêiner, consulte a pergunta anterior. Você também pode aplicar proteções de exclusão adicionais para sua conta de armazenamento com [bloqueios do Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**Posso mover os dados através de diferentes camadas blob (quente, legal, arquivo) quando a bolha está no estado imutável?**

Sim, você pode usar o comando Set Blob Tier para mover dados através dos níveis blob, mantendo os dados no estado imutável compatível. O armazenamento imutável tem suporte nas camadas de blob frequente, esporádica e de arquivos.

**O que acontece se eu não conseguir pagar e meu intervalo de retenção não expirou?**

No caso de não pagamento, as políticas normais de retenção de dados serão aplicadas conforme estipulado nos termos e condições do seu contrato com a Microsoft. Para obter informações gerais, consulte [gerenciamento de dados na Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Há oferta de um período de avaliação ou de cortesia para apenas experimentar o recurso?**

Sim. Quando uma política de retenção baseada em tempo é criada pela primeira vez, ela está em um estado *desbloqueado.* Nesse estado, você pode fazer qualquer alteração desejada no intervalo de retenção, como aumentar ou diminuir e até excluir a política. Depois que a política é bloqueada, ela permanece bloqueada até que o intervalo de retenção expire. Esta política bloqueada impede a exclusão e a modificação do intervalo de retenção. É altamente recomendável que você use o *desbloqueada* apenas para fins de avaliação de estado e a política de bloqueio dentro de um período de 24 horas. Essas práticas ajudarão-lo a cumprir 17a-4(f) s e outros regulamentos.

**Posso usar o soft delete ao lado de políticas de blob imutáveis?**

Sim, se seus requisitos de conformidade permitirem que a exclusão suave seja ativada. [A exclusão suave para o armazenamento Azure Blob](storage-blob-soft-delete.md) se aplica a todos os contêineres dentro de uma conta de armazenamento, independentemente de uma política legal de retenção ou de tempo. Recomendamos ativar a exclusão suave para proteção adicional antes que quaisquer políticas worm imutáveis sejam aplicadas e confirmadas.

## <a name="next-steps"></a>Próximas etapas

- [Definir e gerenciar políticas de imutabilidade para armazenamento Blob](storage-blob-immutability-policies-manage.md)
- [Defina regras para nívelr e excluir automaticamente dados blob com o gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md)
- [Exclusão reversível para blobs do Armazenamento do Azure ](../blobs/storage-blob-soft-delete.md)
- [Proteja assinaturas, grupos de recursos e recursos com bloqueios do Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
