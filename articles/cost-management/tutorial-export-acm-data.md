---
title: Tutorial – Criar e gerenciar dados exportados do Gerenciamento de Custos do Azure
description: Este artigo mostra como você pode criar e gerenciar dados exportados do Gerenciamento de Custos do Azure para que você possa usá-los em sistemas externos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 070844cbf8f6a550b92d764ddb8a31afec12f437
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374603"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerenciar dados exportados

Se leu o tutorial de análise de custo, você está familiarizado com o download manual dos dados do Gerenciamento de Custos. No entanto, você pode criar uma tarefa recorrente que exporta automaticamente seus dados de Gerenciamento de Custos para o Armazenamento do Azure com uma frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contém todas as informações que são coletadas pelo Gerenciamento de Custos. Em seguida, você pode usar os dados exportados no Armazenamento do Azure com sistemas externos e combiná-los com seus próprios dados personalizados. Você também pode usar os dados exportados em um sistema externo, tal como um painel ou outro sistema financeiro.

Assista ao vídeo [Como agendar exportações para o armazenamento com o Gerenciamento de Custos do Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sobre como criar uma exportação agendada dos seus dados de custo do Azure no Armazenamento do Azure.

Os exemplos neste tutorial orientam você ao exportar os dados do Gerenciamento de Custos e, em seguida, verificar se eles foram exportados com êxito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo clientes do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/). Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Há suporte para as seguintes permissões ou escopos do Azure por assinatura para exportações de dados por usuário e por grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir exportações agendadas de uma assinatura.
- Colaborador – pode criar, modificar ou excluir suas próprias exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outras pessoas.
- Leitor – pode agendar exportações às quais tem permissão.

Para contas de Armazenamento do Azure:
- São necessárias permissões de gravação para alterar a conta de armazenamento configurada, independentemente das permissões de exportação.
- Sua conta de Armazenamento do Azure precisa ser configurada para o Armazenamento de Blobs ou de Arquivos.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou exibir uma exportação de dados ou para agendar uma exportação, abra o escopo desejado no portal do Azure e selecione **Análise de custo** no menu. Por exemplo, navegue até **Inscrições**, selecione uma assinatura na lista e, em seguida, selecione **Análise de custo** no menu. Na parte superior da página de análise de custo, clique em **Exportar** e, em seguida, escolha uma opção de exportação. Por exemplo, clique em **Agendar exportação**.  

> [!NOTE]
> - Além de assinaturas, você pode criar exportações em registros, contas, departamentos e grupos de recursos. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).
>- Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta do Armazenamento do Azure que esteja vinculada à conta de armazenamento do parceiro. No entanto, você precisa ter uma assinatura ativa em seu locatário do CSP.
>


Clique em **Adicionar**, digite um nome para a exportação e, em seguida, selecione a **exportação diária da opção de custos desde o início do mês**. Clique em **Próximo**.

![Novo exemplo de exportação mostrando o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a assinatura da sua conta de Armazenamento do Azure e, depois, selecione sua conta de armazenamento.  Especifique o contêiner de armazenamento e o caminho do diretório para o qual você gostaria de enviar o arquivo de exportação.  Clique em **Próximo**.

![Novo exemplo de exportação mostrando detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Revise os detalhes da exportação e clique em **Criar**.

Sua nova exportação aparece na lista de exportações. Por padrão, as novas exportações estão habilitadas. Se você quiser desabilitar ou excluir uma exportação agendada, clique em qualquer item na lista e, em seguida, clique em **Desabilitar** ou **Excluir**.

Inicialmente, pode levar uma ou duas horas até que a exportação seja executada. No entanto, pode levar até quatro horas para que os dados sejam mostrados em arquivos exportados.

### <a name="export-schedule"></a>Agendamento de exportação

As exportações agendadas são afetadas pela hora e pelo dia da semana de quando a exportação foi inicialmente criada. Quando você cria uma exportação agendada, a exportação é executada na mesma hora do dia para cada ocorrência seguinte de exportação. Por exemplo, você cria uma exportação diária às 13h. A próxima exportação é executada às 13h do dia seguinte. A hora atual afeta todos os outros tipos de exportação da mesma maneira – eles sempre são executados na mesma hora do dia de quando a exportação foi inicialmente criada. Em outro exemplo, você cria uma exportação semanal às 16h na segunda-feira. O próximo relatório é executado às 16h na próxima segunda-feira. *Os dados exportados ficam disponíveis no prazo de quatro horas após a hora de execução.*

Cada exportação cria um arquivo e, portanto, as exportações mais antigas não são substituídas.

Há três tipos de opções de exportação:

**Exportação diária dos custos do mês atual** – a exportação inicial é executada imediatamente. As exportações seguintes são executadas no dia seguinte, na mesma hora da exportação inicial. Os últimos dados são agregados das exportações diárias anteriores.

**Exportação semanal dos custos dos últimos 7 dias** – a exportação inicial é executada imediatamente. As exportações seguintes são executadas no dia da semana e na mesma hora da exportação inicial. Os custos referem-se aos últimos sete dias.

**Personalizado** – permite que você agende exportações semanal e mensalmente com opções de semana e mês atual. *A exportação inicial será executada imediatamente.*

Se você tiver uma assinatura de Pagamento Conforme o Uso, do MSDN ou do Visual Studio, o período de cobrança da fatura talvez não esteja alinhado ao mês do calendário. Para esses tipos de assinaturas e grupos de recursos, você pode criar uma exportação alinhada ao período da fatura ou aos meses do calendário. Para criar uma exportação alinhada ao mês da fatura, navegue até **Personalizado**, em seguida, selecione **Período de cobrança até a data**.  Para criar uma exportação alinhada ao mês do calendário, selecione **Mês até a data**.
>
>

![Guia Nova exportação – Informações Básicas mostrando uma seleção semanal personalizada da semana atual](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são coletados

Você pode verificar facilmente se os dados do Gerenciamento de Custos estão sendo coletados e exibir o arquivo CSV exportado usando o Gerenciador de Armazenamento do Azure.

Na lista de exportação, clique no nome da conta de armazenamento. Na página da conta de armazenamento, clique em Abrir no Gerenciador. Se você vir uma caixa de confirmação, clique em **Sim** para abrir o arquivo no Gerenciador de Armazenamento do Azure.

![Página de conta de armazenamento mostrando informações de exemplo e link para abrir no Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

No Gerenciador de Armazenamento, navegue até o contêiner que você deseja abrir e selecione a pasta que corresponde ao mês atual. É mostrada uma lista de arquivos CSV. Selecione um deles e clique em **Abrir**.

![Informações de exemplo mostradas no Gerenciador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O arquivo é aberto com o programa ou aplicativo que está configurado para abrir as extensões de arquivo CSV. Aqui está um exemplo no Excel.

![Exemplo de dados CSV exportados mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Acessar dados exportados de outros sistemas

Uma das finalidades de exportar os dados do Gerenciamento de Custos é acessar os dados de sistemas externos. Você pode usar um sistema de painéis ou outro sistema financeiro. Tais sistemas variam amplamente, portanto, mostrar um exemplo não seria conveniente.  No entanto, você pode obter uma introdução sobre como acessar os dados dos aplicativos na [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

Avance para o próximo tutorial para otimizar e melhorar a eficiência, identificando recursos ociosos e subutilizados.

> [!div class="nextstepaction"]
> [Examinar recomendações de otimização e agir com base nelas](tutorial-acm-opt-recommendations.md)
