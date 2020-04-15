---
title: Tutorial – Criar e gerenciar dados exportados do Gerenciamento de Custos do Azure
description: Este artigo mostra como você pode criar e gerenciar dados exportados do Gerenciamento de Custos do Azure para que você possa usá-los em sistemas externos.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: f0a1515816fe7a9e1d79f69655e6bf21725a0b5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877942"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerenciar dados exportados

Se leu o tutorial de análise de custo, você está familiarizado com o download manual dos dados do Gerenciamento de Custos. No entanto, você pode criar uma tarefa recorrente que exporta automaticamente seus dados de Gerenciamento de Custos para o Armazenamento do Azure com uma frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contém todas as informações que são coletadas pelo Gerenciamento de Custos. Em seguida, você pode usar os dados exportados no Armazenamento do Azure com sistemas externos e combiná-los com seus próprios dados personalizados. Você também pode usar os dados exportados em um sistema externo, tal como um painel ou outro sistema financeiro.

Assista ao vídeo [Como agendar exportações para o armazenamento com o Gerenciamento de Custos do Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sobre como criar uma exportação agendada dos seus dados de custo do Azure no Armazenamento do Azure. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Os exemplos neste tutorial orientam você ao exportar os dados do Gerenciamento de Custos e, em seguida, verificar se eles foram exportados com êxito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo clientes do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) e do [Contrato de Cliente da Microsoft](get-started-partners.md). Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Há suporte para as seguintes permissões ou escopos do Azure por assinatura para exportações de dados por usuário e por grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir exportações agendadas de uma assinatura.
- Colaborador – pode criar, modificar ou excluir suas próprias exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outras pessoas.
- Leitor – pode agendar exportações às quais tem permissão.

Para contas de Armazenamento do Azure:
- São necessárias permissões de gravação para alterar a conta de armazenamento configurada, independentemente das permissões de exportação.
- Sua conta de Armazenamento do Azure precisa ser configurada para o Armazenamento de Blobs ou de Arquivos.

Se você tiver uma nova assinatura, não poderá usar imediatamente os recursos do Gerenciamento de Custos. Poderá levar até 48 horas para você poder usar todos os recursos do Gerenciamento de Custos.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou exibir uma exportação de dados ou para agendar uma exportação, abra o escopo desejado no portal do Azure e selecione **Análise de custo** no menu. Por exemplo, navegue até **Inscrições**, selecione uma assinatura na lista e, em seguida, selecione **Análise de custo** no menu. Na parte superior da página de Análise de custo, selecione **Exportar** e, em seguida, escolha uma opção de exportação. Por exemplo, selecione **Agendar exportação**.  

> [!NOTE]
> - Além de assinaturas, você pode criar exportações em registros, contas, departamentos e grupos de recursos. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).
>- Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta do Armazenamento do Azure que esteja vinculada à conta de armazenamento do parceiro. No entanto, você precisa ter uma assinatura ativa em seu locatário do CSP.
>


Selecione **Adicionar**, digite um nome para a exportação e, em seguida, selecione a opção **Exportação diária de custos do mês atual**. Selecione **Avançar**.

![Novo exemplo de exportação mostrando o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a assinatura da sua conta de Armazenamento do Azure e, depois, selecione sua conta de armazenamento.  Especifique o contêiner de armazenamento e o caminho do diretório para o qual você gostaria de enviar o arquivo de exportação. Selecione **Avançar**.

![Novo exemplo de exportação mostrando detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Examine os detalhes da exportação e selecione **Criar**.

Sua nova exportação aparece na lista de exportações. Por padrão, as novas exportações estão habilitadas. Se quiser desabilitar ou excluir uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **Desabilitar** ou **Excluir**.

Inicialmente, pode levar uma ou duas horas até que a exportação seja executada. No entanto, pode levar até quatro horas para que os dados sejam mostrados em arquivos exportados.

### <a name="export-schedule"></a>Agendamento de exportação

As exportações agendadas são afetadas pela hora e pelo dia da semana de quando a exportação foi inicialmente criada. Quando você cria uma exportação agendada, a exportação é executada na mesma frequência para cada ocorrência seguinte de exportação. Por exemplo, para um conjunto de exportação do mês atual definido a uma frequência diária, a exportação é executada diariamente. Da mesma forma, para uma exportação semanal, a exportação é executada todas as semanas no mesmo dia em que foi agendada. O tempo de entrega exato da exportação não é garantido e os dados exportados ficam disponíveis dentro de quatro horas após a hora da execução."
Cada exportação cria um arquivo e, portanto, as exportações mais antigas não são substituídas.

Há dois tipos de opções de exportação:

**Exportação diária dos custos do mês atual** – a exportação inicial é executada imediatamente. As exportações seguintes são executadas no dia seguinte, na mesma hora da exportação inicial. Os últimos dados são agregados das exportações diárias anteriores.

**Personalizado** – permite que você agende exportações semanal e mensalmente com opções de semana e mês atual. *A exportação inicial será executada imediatamente.*

Se você tiver uma assinatura de Pagamento Conforme o Uso, do MSDN ou do Visual Studio, o período de cobrança da fatura talvez não esteja alinhado ao mês do calendário. Para esses tipos de assinaturas e grupos de recursos, você pode criar uma exportação alinhada ao período da fatura ou aos meses do calendário. Para criar uma exportação alinhada ao mês da fatura, navegue até **Personalizado**, em seguida, selecione **Período de cobrança até a data**.  Para criar uma exportação alinhada ao mês do calendário, selecione **Mês até a data**.
>
>

![Guia Nova exportação – Informações Básicas mostrando uma seleção semanal personalizada da semana atual](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são coletados

Você pode verificar facilmente se os dados do Gerenciamento de Custos estão sendo coletados e exibir o arquivo CSV exportado usando o Gerenciador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione Abrir no Explorador. Se vir uma caixa de confirmação, selecione **Sim** para abrir o arquivo no Gerenciador de Armazenamento do Azure.

![Página de conta de armazenamento mostrando informações de exemplo e link para abrir no Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

No Gerenciador de Armazenamento, navegue até o contêiner que você deseja abrir e selecione a pasta que corresponde ao mês atual. É mostrada uma lista de arquivos CSV. Selecione um deles e selecione **Abrir**.

![Informações de exemplo mostradas no Gerenciador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O arquivo é aberto com o programa ou aplicativo que está configurado para abrir as extensões de arquivo CSV. Aqui está um exemplo no Excel.

![Exemplo de dados CSV exportados mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Acessar dados exportados de outros sistemas

Uma das finalidades de exportar os dados do Gerenciamento de Custos é acessar os dados de sistemas externos. Você pode usar um sistema de painéis ou outro sistema financeiro. Tais sistemas variam amplamente, portanto, mostrar um exemplo não seria conveniente.  No entanto, você pode obter uma introdução sobre como acessar os dados dos aplicativos na [Introdução ao Armazenamento do Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

Avance para o próximo tutorial para otimizar e melhorar a eficiência, identificando recursos ociosos e subutilizados.

> [!div class="nextstepaction"]
> [Examinar recomendações de otimização e agir com base nelas](tutorial-acm-opt-recommendations.md)
