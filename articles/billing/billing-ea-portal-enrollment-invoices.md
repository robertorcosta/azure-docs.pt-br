---
title: Faturas do Registro Enterprise do Azure
description: Este artigo explica como gerenciar e usar sua fatura do Azure Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 6bd70a58552a0217cff82cad10b11783aec64347
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888411"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas do Registro Enterprise do Azure

Este artigo explica como gerenciar e usar sua fatura do Azure Enterprise. A fatura representa a cobrança, examine-a para verificar se está precisa. Conheça outras tarefas que podem ser necessárias para gerenciar sua fatura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Alterar o número da OC de uma fatura de excedente

O Portal do EA do Azure gera automaticamente uma PO (ordem de compra) padrão, a menos que o administrador do EA configure uma antes da data de faturamento. Um administrador do EA pode atualizar o número da OC até sete dias após receber um email de notificação automático com a fatura.

Um número de ordem de compra também pode ser inserido para uma conta ou assinatura específica. Os relatórios serão padronizados para o número da ordem de compra de nível mais baixo definido na hierarquia, o que significa que, se nenhum número de ordem de compra de assinatura for inserido, o número de ordem de compra da conta será usado. Se nenhuma ordem de compra de conta for inserida, a ordem de compra do departamento será usada.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Para atualizar o número da ordem de compra dos serviços do Azure:

1. No Portal do EA do Azure, clique em **Relatório** e, em seguida, em **Resumo do uso**.
1. Selecione **Editar números da OC** no canto superior direito.
1. Selecione o botão de opção **Serviços do Azure**.
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas. Os números da OC podem ser editados por até sete dias após a notificação da fatura ou até que a fatura seja paga, o que vier primeiro.
1. Insira o novo número da OC no campo **Número da OC**.
1. Clique em **Salvar** para enviar as alterações.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Para atualizar o número da ordem de compra do Marketplace:

1. No Portal do EA do Azure, clique em **Relatório** e, em seguida, em **Resumo do uso**.
1. Selecione **Editar números da OC** no canto superior direito
1. Selecione o botão de opção do **Marketplace**
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas. Os números da OC podem ser editados por até sete dias após a notificação da fatura ou até que a fatura seja paga, o que vier primeiro.
1. Insira o novo número da OC no campo **Número da OC**.
1. Clique em **Salvar** para enviar as alterações.

## <a name="cadence-of-azure-ea-billing"></a>Periodicidade da cobrança do EA do Azure

### <a name="billing-intervals"></a>Intervalos de cobrança

A Microsoft cobrará uma vez ao ano na data de início da vigência do registro para qualquer compra de compromisso de serviços do Microsoft Azure e posteriormente para qualquer uso que exceda os valores do compromisso. Valores de compromisso são cotados com base em uma taxa mensal e cobradas anualmente com antecedência. Valores excedentes são calculados mensalmente e cobrados posteriormente, no final do período de cobrança.

Dependendo de como você opta por fazer suas compras de compromisso, seu compromisso anual será coincidente com sua data de aniversário de registro ou com a data de efetivação da sua assinatura de aditamento de um ano.

Você receberá a fatura excedente dependendo da data de início e da configuração do registro.

**Registros diretos com uma data de início anterior a 1º de maio de 2018**: Clientes diretos do Enterprise Azure (EA) são configurados como um ciclo de cobrança anual para os Serviços do Azure (exceto serviços do Marketplace). Seu período de cobrança é baseado na data de aniversário. A data de aniversário é a data em que seu contrato entrou em vigor. Para receber a primeira fatura excedente do Serviço do Azure, você deve ultrapassar 150% do limite do seu compromisso monetário (“MC”).  Quando o consumo de serviço total ultrapassar 150% do limite do seu MC, você será convertido automaticamente para um ciclo de cobrança trimestral com base na data de aniversário.  Se não ultrapassar 150% do limite do seu MC, o registro permanecerá em um ciclo de cobrança anual e a fatura excedente será recebida no final do ano de compromisso.

**Registros diretos com uma data de início posterior a 1º de maio de 2018**: As faturas de consumo e cobranças do Azure do cliente são cobradas separadamente do cliente e terão um período de cobrança mensal.  Todas as cobranças não cobertas pelo compromisso monetário do Azure serão devidas como um pagamento excedente.  

**Registros indiretos com um registro iniciado antes de 1º de maio de 2018**: Clientes indiretos do Enterprise Azure (EA) são configurados como um ciclo de cobrança trimestral.  O parceiro de canal (CP) faturará o cliente diretamente.  

**Registros indiretos com uma data de início posterior a 1º de maio de 2018**: Qualquer contrato indireto com uma data de início maior ou igual a 1º de maio de 2018 será cobrado mensalmente.  

### <a name="increasing-commitment"></a>Compromisso crescente

O compromisso pode ser aumentado a qualquer momento e será cobrado pelo número de meses restantes para o período de compromisso do ano em questão. Por exemplo, se você se inscrever para uma assinatura de aditamento de um ano e aumentar seu compromisso durante o mês seis, será faturado pelos seis meses restantes desse período. Suas quantidades de compromisso serão atualizadas para os últimos seis meses de seu período de compromisso para determinar quaisquer cobranças excedentes.

### <a name="overage"></a>Excedente

No caso de excedente, você será cobrado pelo uso ou pelas reservas que excederam seu compromisso durante o período de cobrança. Para exibir um detalhamento de como as quantidades excedentes de itens individuais foram calculadas, confira o relatório Resumo do uso ou fale com o parceiro do canal.

Para cada item na fatura, você verá o total de cobranças (valor estendido), a quantidade de compromisso usada para cobrir as cobranças (uso do compromisso) e a quantidade de cobranças que excedem o compromisso (valor líquido).  Os impostos aplicáveis são computados somente no valor líquido que excede o compromisso.

O faturamento excedente é automatizado.  O cronograma das notificações e das faturas depende da data de término do período de cobrança do cliente.  A notificação de excedente é enviada, normalmente, sete dias após a data de término da cobrança do cliente, quando os clientes podem entrar no portal, analisar suas cobranças e atualizar as OCs geradas pelo sistema (isso também pode ser atualizado a qualquer momento antes que o excedente seja liberado).  As faturas excedentes serão enviadas em um período de 7 a 9 dias.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do período de cobrança de abril de 2019, os clientes começarão a receber uma única fatura do Azure, pois combinamos todas as cobranças do Azure e do Azure Marketplace em uma única fatura, ao invés de duas faturas separadas. (Essa alteração não afeta os clientes na Austrália, no Japão ou em Cingapura.) Durante a transição para uma fatura consolidada, você receberá uma fatura parcial do Marketplace. Essa última fatura separada mostrará as cobranças do Marketplace antes da data da sua atualização de cobrança. As cobranças do Marketplace após essa data serão exibidas em sua fatura do Azure. Após o período de transição, você verá todas as cobranças do Azure e do Marketplace na fatura consolidada.  

### <a name="purchase-order-numbers"></a>Números de ordem de compra

O padrão da ordem de compra é um número de ordem de compra gerado pelo sistema. Os usuários podem atualizar seu número de ordem de compra no Enterprise Portal fazendo logon como o administrador corporativo e navegando até a seção Relatórios. Há uma caixa para o número da OC no canto superior direito da janela que permitirá que o administrador corporativo edite o número da OC clicando no ícone de lápis.

## <a name="adjust-billing-frequency"></a>Alterar a frequência da cobrança

A frequência da cobrança do cliente pode ser anual, trimestral ou mensal. O período de cobrança é determinado quando o cliente assina o contrato. A cobrança mensal é o menor intervalo de cobrança.

É necessária a aprovação do admin corporativo para alterar o período de cobrança de anual para trimestral em registros diretos. É necessária a aprovação do administrador do parceiro para registros indiretos. A alteração entra em vigor no fim do trimestre de cobrança atual.

Para alterar um período de cobrança anual ou trimestral para mensal, é necessário acrescentar um aditamento ao contrato.  Qualquer alteração no período de cobrança do registro existente exige aprovação de um admin corporativo ou do indivíduo identificado como _Contato para Cobrança_ no contrato. É possível enviar sua aprovação no [Suporte do Portal do EA do Azure](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) e selecionar a Categoria de Problema **Cobrança e Faturamento**.  A alteração entra em vigor no fim do trimestre de cobrança atual.

Se um Aditamento M503 for assinado, você poderá alterar qualquer contrato de qualquer frequência para a cobrança mensal. 

## <a name="credits-and-adjustments"></a>Créditos e ajustes

Todos os créditos e ajustes aplicados ao registro podem ser vistos em [https://ea.azure.com](https://ea.azure.com) na seção **Relatórios**.

Para exibir os créditos, siga estas etapas:

1. Selecione a seção **Relatórios**.
1. Clique em **Resumo do uso**.
1. No canto superior direito, altere a exibição _M_ para _C_.
1. Aumente o campo de ajuste na tabela de compromisso do serviço do Azure.
1. Nessa linha, você verá os créditos aplicados ao seu registro e uma breve explicação sobre ele, por exemplo: Crédito do Contrato de Nível de Serviço.

## <a name="request-an-invoice-copy"></a>Solicitar uma cópia da fatura

Para solicitar uma cópia da fatura, fale com seu parceiro.

## <a name="overage-offset-by-customers"></a>Excedente compensado pelo cliente

Se o cliente tiver excedentes e quiser usá-los com compromisso monetário, os seguintes critérios precisarão ser cumpridos:

- O cliente deve ter cobranças de excedente incorridas, mas não pagas, no prazo de um ano da data de término do serviço cobrado.
- O valor do compromisso monetário disponível deve cobrir o valor total das cobranças incorridas, incluindo todas as faturas anteriores do Azure não pagas.
- O termo de cobrança cuja conclusão está sendo solicitada precisa estar totalmente encerrado. A cobrança se encerra totalmente após o quinto dia de cada mês.
- O período de cobrança cuja compensação está sendo solicitada precisa estar totalmente encerrado.
- O desconto DAC se baseia no novo compromisso real menos os fundos planejados para consumo anterior. Este requisito se aplica somente às cobranças de excedente incorridas. Isso só funciona para serviços que consomem compromisso monetário, então não é possível cobrir cobranças de Marketplace. As cobranças de Marketplace são feitas separadamente.
- Se um cliente quiser concluir uma compensação de excedente, poderá abrir uma solicitação de suporte. Ou a equipe da conta pode abrir a solicitação de suporte. Para concluir o processo, é necessária uma aprovação por email do administrador do EA do cliente ou o Contato para Cobrança.

## <a name="view-price-sheet-information"></a>Exibir informações sobre a tabela de preços

Administradores corporativos podem ver a tabela de preços associada ao registro nos serviços do Azure.

Para exibir a tabela de preços atual:

1. No Portal do EA do Azure, clique em **Relatórios** e em **Tabela de Preços**.
2. Confira a tabela de preços ou clique em **Baixar**.

![Exemplo mostrando as informações da tabela de preços](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Para baixar o histórico da tabela de preços:

1. No Portal do EA do Azure, clique em **Relatórios** e em **Baixar uso**.
2. Baixe a tabela de preços.

![Exemplo mostrando onde baixar uma tabela de preços mais antiga](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Em caso de dúvidas sobre as discrepâncias nos preços, alguns motivos são:

O preço pode ter sido alterado entre o registro anterior e o novo. Alterações de preço ocorrem porque o preço é definido por contrato para um registro específico da data de início à data de término de um contrato. Quando um registro é transferido para um novo registro, ele segue o preço de um novo contrato. O preço é definido pela tabela de preços do cliente. Desse modo, os preços podem ser mais altos no novo registro.

Se um registro tiver o prazo aumentado, o preço também será alterado. Os preços mudam de acordo com as taxas pagas conforme o uso.

## <a name="request-detailed-usage-information"></a>Solicitar informações detalhadas sobre o uso

Os administradores corporativos podem exibir um resumo dos dados de uso, do compromisso monetário consumido e das cobranças associadas com o uso adicional no Portal do EA do Azure. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

Para exibir o uso detalhado de contas específicas, baixe o relatório Detalhes de Uso navegando até **Relatórios** > **Baixar Uso**. O relatório não inclui as taxas aplicáveis. Talvez haja uma latência de até oito horas do momento em que o uso foi incorrido até ser refletido no relatório.

Para registros indiretos, seu parceiro precisa habilitar a função de marcação antes de você poder ver quaisquer informações relacionadas ao custo.

## <a name="reports"></a>Relatórios

Os administradores corporativos podem exibir um resumo dos dados de uso, do compromisso monetário consumido e das cobranças associadas ao uso adicional no Portal do EA. As cobranças serão apresentadas no resumo para todas as contas e assinaturas.

**Relatórios do EA**

- Resumo e gráficos de uso
- Relatório de uso do serviço
- Relatório de saldo e cobrança
- Relatório de detalhes de uso
- Relatório de cobranças do Marketplace
- Tabela de preços
- Download do relatório avançado
- Formatação do relatório em CSV

**Para exibir os relatórios e gráficos de resumo de uso**

1. No Portal do EA do Azure, clique em **Relatórios** na área de navegação à esquerda e exiba a guia **Resumo do uso**.
1. Selecione o período de compromisso desejado no menu suspenso de intervalos de datas na parte superior esquerda.
1. Selecione o período ou mês desejado no gráfico para exibir detalhes adicionais.
1. Exiba o gráfico de uso de cada mês com um detalhamento do que foi utilizado, as cobranças adicionais de serviço, o que foi cobrado separadamente e as cobranças do Marketplace.
1. Para o mês selecionado, filtre por departamentos, contas e assinaturas abaixo do grafo.
1. Altere entre o detalhamento Cobrar por serviços e o detalhamento Cobrar por hierarquia.
1. Exiba de forma detalhada os serviços do Azure, o que foi cobrado separadamente e as cobranças do Azure Marketplace.

## <a name="service-usage-report"></a>Relatório de uso do serviço

A página Relatório de uso do serviço permite que os administradores corporativos exibam um resumo dos dados de uso do serviço. Embora o uso seja apresentado no nível de resumo para todas as contas e assinaturas, você também pode filtrar o relatório por contas ou assinaturas para exibir o uso de forma detalhada.

Observe que também pode haver uma latência de até cinco dias entre a data de uso incorrida e quando esse uso é refletido neste relatório.

### <a name="to-view-the-report"></a>Para exibir o relatório:

1. Entre no Enterprise Portal.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. Clique na guia **Resumo do uso**.
1. Clique no intervalo de datas desejado.
1. Escolha quais contas ou assinaturas exibir.
1. Altere a exibição de Cobrar por serviços para Cobrar por hierarquia para exibir detalhamentos diferentes.
1. Exiba detalhes, incluindo o nome do serviço, a unidade de medida, as unidades consumidas, a taxa efetiva e o custo estendido.

## <a name="download-csv-reports"></a>Baixar relatórios CSV

A página Download do relatório mensal permite que os administradores corporativos baixem vários relatórios como arquivos .csv, incluindo um relatório de saldo e cobrança, relatório de detalhes de uso, relatório de cobrança do Marketplace e tabela de preços.

## <a name="to-download-reports"></a>Para baixar relatórios:

1. No Portal do EA do Azure, clique em **Relatório**.
1. Selecione **Baixar uso** na faixa de opções superior.
1. Selecione **Baixar** ao lado do relatório do mês apropriado.



## <a name="csv-report-formatting"></a>Formatação do relatório em CSV

Os clientes que visualizam os relatórios em CSV do Portal do EA do Azure em euros podem encontrar problemas de formatação com relação a vírgulas e pontos.

Por exemplo, você talvez veja:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Você deverá ver:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Causa raiz

O Excel importa todos os campos como texto “Geral” e supõe que um número seja separado no padrão matemático: "1,000.00".  Portanto, uma moeda europeia que usa um ponto (.) para o separador de milhar e uma vírgula para o separador de casas decimais (,), isto é, "1.000,00", será exibida incorretamente. Isso pode variar dependendo da configuração de idioma regional.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Ao importar o CSV, aplique as seguintes etapas:

1.    Abra o Excel e vá para Arquivo > Abrir.
1.    O assistente de importação de texto será exibido.
1.    Em Tipo de dados originais, escolha _delimitado_.  O padrão é _Largura Fixa_.
1.    Pressione **Avançar**.
1.    Em Delimitadores, marque a caixa de seleção para Vírgula. O padrão é “Tab” (desmarque este).
1.    Pressione **Avançar**.
1.    Role até as colunas “ResourceRate” e “ExtendedCost”.
1.    Selecione a coluna “ResourceRate” (que aparecerá realçada em preto).
9.    Na seção Formato de dados da coluna, selecione “Texto” em vez de “Geral”.  Você verá que o cabeçalho da coluna muda de “Geral” para “Texto”.
10. Repita as etapas 8 e 9 para a coluna “ExtendedCost”. Selecione **Concluir**.

Se o Excel está definido para abrir automaticamente arquivos \*.csv, use a função “Abrir” no Excel. Abra o Excel e vá para Arquivo > Abrir.

## <a name="balance-and-charge-report"></a>Relatório de saldo e cobrança

O Relatório de saldo e cobrança oferece um resumo mensal de informações sobre saldos, novas compras, cobranças de serviço do Azure Marketplace, ajustes e cobranças excedentes. Todas as linhas na tabela de resumo Compromisso do Serviço do Azure permanecerão estáticas mês a mês, enquanto os detalhes de todas as compras e ajustes serão exibidos nas seções Detalhes da nova compra e Detalhes de ajuste.

### <a name="download-the-balance-and-charge-report"></a>Baixar o relatório de saldo e cobrança

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. Clique na guia **Baixar Relatório**.
1. Selecione o mês apropriado na coluna _Saldo e cobrança_ e clique para baixar o relatório.

## <a name="usage-detail-report"></a>Relatório de detalhes de uso

O Relatório de detalhes de uso oferece um resumo mensal dos serviços e das quantidades que estão sendo consumidas por um registro, incluindo informações sobre nomes de medidores, tipos de medidores e quantidades consumidas.

### <a name="download-the-usage-detail-report"></a>Baixar o relatório de detalhes de uso

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. ***Clique na guia **Baixar uso**.
1. Selecione o mês apropriado na coluna _Detalhes de uso_ e clique para baixar o relatório.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Cobranças do Marketplace nos relatórios do Portal do EA do Azure

Informações adicionais sobre cobranças do Marketplace podem ser encontradas [aqui](https://azure.microsoft.com/marketplace/faq/).

Há dois tipos de cobranças do Marketplace:

- **Baseado em uso:** produtos medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas por hora, as pesquisas de API do Bing são cobradas pelo número de pesquisas.
- **Valor Mensal:** cobrado mensalmente com base no uso/acesso.

Para exibir as diferentes cobranças no Enterprise Portal:

1. **Relatório Resumo do uso**: exibe **ambas** a cobrança baseada em uso e a cobrança mensal de valores do Marketplace.
1. **Relatório de cobranças do Marketplace**: exibe **apenas** as cobranças do Marketplace com base no uso.  Valores únicos não são exibidos.

Não que o Azure Marketplace não esteja disponível para registros de MPSA.

## <a name="advanced-report-download"></a>Download do relatório avançado

Para relatórios sobre contas ou intervalos de datas específicos, é possível usar o download do relatório avançado. Desde 30 de agosto de 2016, o formato do arquivo de saída foi alterado de .xlsx para .csv para acomodar conjuntos de registros maiores.

1. Escolha **Download do relatório avançado**.
1. Escolha **Intervalo de datas apropriado**.
1. Escolha **Contas apropriadas**.
1. Escolha **Solicitar dados de uso**.
1. Escolha o botão **Atualizar** até que o status do relatório seja atualizado para “Baixar”.
1. Baixe o relatório.

## <a name="reporting-for-non-enterprise-administrators"></a>Relatórios para administradores não corporativos

Os administradores corporativos podem habilitar o acesso para exibição de custos a administradores de departamento (DA) e proprietários de conta (AO) em um registro. Uma vez habilitado, um proprietário de conta pode baixar relatórios em .csv específicos para suas contas e assinaturas e exibir as informações visualmente na seção de relatórios do Enterprise Portal.

### <a name="to-enable-access"></a>Para habilitar o acesso:

 1. Entrar como um administrador corporativo.
 1. Clique em **Gerenciar** na área de navegação à esquerda.
 1. Clique na guia **Registro**.
 1. Na seção Detalhes do registro, selecione o ícone de lápis ao lado de:
    - Exibir cobranças de DA
    - Exibir cobranças de AO
 1. Selecione **Habilitado**.
 1. Clique em **Save** (Salvar).

### <a name="to-view-reports"></a>Para exibir relatórios:

1. Entre no Portal do EA do Azure como um administrador de departamento ou um proprietário de conta.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. Clique na guia **Resumo do uso** para exibir informações sobre a conta e a assinatura visualmente.
1. Clique em **Baixar uso** para exibir os relatórios em .csv.

Os administradores de departamento e os proprietários de conta não podem exibir cobranças usando a função _Download do relatório avançado_. Os custos são exibidos como US$ 0,00.

Exibir cobranças de AO aplica-se aos proprietários de conta e a todos os usuários que têm permissões em assinaturas associadas. Se você for um cliente indireto, os recursos de custos deverão ser habilitados pelo seu parceiro de canal.

## <a name="move-usage-data-to-another-enrollment"></a>Mover dados de uso para outro registro

Os dados de uso só serão movidos se a transferência for retroativa. Há duas opções para mover dados de uso de um registro para outro:

- Transferências de conta de um registro para outro
- Transferência de registro de um registro para outro

Para ambas as opções, é preciso enviar uma [solicitação de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipe de Suporte do EA para obter assistência. 

## <a name="azure-ea-pricing-overview"></a>Visão geral de preços do EA do Azure

Este documento fornece detalhes sobre como o uso é calculado e responde a muitas das perguntas mais frequentes sobre os preços de vários serviços do Azure em programas Enterprise em que os cálculos são mais complexos.

### <a name="price-protection"></a>Proteção de preço

Os clientes e parceiros de canal (no caso de nossos canais indiretos) têm a garantia de receber os preços indicados em sua Tabela de Preços ao Cliente (CPS) ou inferiores, ou o preço em vigor na data de efetivação de sua compra do Azure. Esse preço é chamado de preço de linha de base. Para serviços introduzidos após essa compra, é o preço em vigor no desconto de nível aplicável quando o serviço foi introduzido pela primeira vez. Essa proteção de preço aplica-se durante o período de compromisso, que pode ser de um ou três anos, dependendo do programa Enterprise.

### <a name="price-changes"></a>Alterações de preço

A Microsoft pode baixar o preço atual do programa Enterprise para serviços individuais do Azure durante o período de um registro.  Estenderemos essas taxas reduzidas aos clientes existentes enquanto o preço mais baixo estiver em vigor.  Se essas taxas aumentarem posteriormente, o preço do registro de um serviço não aumentará além do teto de proteção de preço do cliente, conforme definido acima, mas poderá aumentar em relação ao preço anterior reduzido.  Em ambos os casos, a Microsoft informará os clientes e os parceiros de canal indiretos sobre alterações de preço futuras por meio de um email aos administradores corporativos e parceiros associados ao registro.

### <a name="current-effective-pricing"></a>Preço efetivo atual

Parceiros de cliente e de canal podem exibir seus preços atuais para um registro fazendo logon no [Enterprise Portal do Azure](https://ea.azure.com/) e navegando até a página da tabela de preços para esse registro.  Se você comprar o Azure indiretamente por meio de um de nossos parceiros de canal, precisará obter as atualizações de preços do seu parceiro de canal, caso eles não tenham habilitado a exibição de preços para seu registro.

### <a name="introduction-of-new-azure-services"></a>Introdução de novos serviços do Azure

Estamos aprimorando continuamente o Azure e adicionamos periodicamente novos serviços que são cobrados separadamente dos serviços existentes.  Alguns serviços de versão prévia estão disponíveis automaticamente, enquanto outros exigem a ação do cliente por meio do [Portal da Conta do Azure](https://account.windowsazure.com/PreviewFeatures). Observe também que, quando os serviços mudam da versão prévia para a disponibilidade geral, os preços podem aumentar à medida que os SLAs de desempenho e confiabilidade são aplicados em sua totalidade. Por fim, alguns serviços são fornecidos com preços promocionais em vigor quando introduzidos pela primeira vez, o que pode aumentar em uma data futura. Qualquer aumento devido à transição da versão prévia ou do preço promocional para a disponibilidade geral não é restringido pela proteção de preço de linha de base normal e será aplicado ao uso desses serviços. Os clientes podem evitar cobranças relacionadas a esses serviços optando por não usar o novo serviço.

### <a name="introduction-of-regional-pricing"></a>Introdução de preços regionais

Além da introdução de novos serviços, os serviços também mudam periodicamente de uma base global única para um modelo mais regional à medida que o suporte regional para esses serviços aumenta. Ao introduzir a regionalização de um serviço pela primeira vez, a proteção de preço aplica-se a essas novas regiões em comparação com o preço global anterior em vigor para o registro. No entanto, as regiões adicionais introduzidas posteriormente para esse mesmo serviço são consideradas novos serviços e são oferecidas com suas próprias taxas individuais, independentemente da proteção de preço de linha de base.

### <a name="enterprise-msdn-devtest"></a>Desenvolvimento/Teste do Enterprise no MSDN

Os administradores corporativos podem habilitar os proprietários de conta para criar assinaturas com base na oferta de Desenvolvimento/Teste do EA no MSDN. Para que isso funcione corretamente, o proprietário da conta deverá configurar as assinaturas de Desenvolvimento/Teste do EA no MSDN necessárias para os assinantes subjacentes do MSDN. Isso permite que os assinantes ativos do MSDN executem cargas de trabalho de desenvolvimento e teste no Azure com taxas especiais de desenvolvimento/teste. Para saber mais, confira [Desenvolvimento/Teste do EA no MSDN](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Diretrizes de cálculo de uso do programa Enterprise

Confira [Serviços do Azure](https://azure.microsoft.com/services/) e [Preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços, unidades de medida, perguntas frequentes e diretrizes de relatório de uso para cada serviço individualmente. Além disso, ao calcular o uso do serviço, deve-se aderir às diretrizes do programa Enterprise a seguir.

### <a name="enterprise-program-units-of-measure"></a>Unidades de medida do programa Enterprise

As unidades de medida para programas Enterprise costumam ser diferentes daquelas utilizadas em nossos outros programas, como o programa de Contrato do Microsoft Online Services (MOSA). Isso significa que, para vários serviços, a unidade de medida é agregada para fornecer os preços normalizados. A unidade de medida mostrada na exibição Resumo do uso do Enterprise Portal é sempre a medida do Enterprise. Apresentamos uma lista completa das unidades atuais de medida e conversões de cada serviço na planilha [Nomes de serviços amigáveis](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Regras de arredondamento

O arredondamento realizado no Enterprise Portal usa o padrão de arredondamento bancário do IEEE ou a lógica de arredondamento gaussiana. Isso arredonda para o dígito par mais próximo para valores de meio dígito, enquanto as funções de arrendondamento de meio dígito mais comuns sempre arredondam meios dígitos para o próximo dígito mais alto. Esse método, na verdade, fornece uma soma total mais precisa sobre o grupo quando comparado à lógica padrão do Excel.

Quando o primeiro dígito eliminado é 5 e não há dígitos a seguir, ou os dígitos a seguir são zeros, o dígito anterior torna-se par (ou seja, é arredondado para o dígito par mais próximo). Por exemplo, 2,315 e 2,325 são ambos 2,32 quando arredondados para o centésimo mais próximo.

Para referência, ao usar o Excel para modelar as regras de arredondamento e conversão usadas no Enterprise Portal, deve-se usar as fórmulas MARRED, conforme mostrado abaixo.

| Cenário | Fórmula da lógica de arredondamento bancária |
| --- | --- |
| Uso de arredondamento | =MARRED({_origem_}, 0,0002) |
| Arredondamento de preços (2 decimais) | =MARRED({_origem_}, 0,02) |
| Arredondamento de preços (0 decimais) | =MARRED({_origem_}, 2) |

**Tabela** **2** **: conversão de horas do serviço de nuvem e da máquina virtual**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Conversão entre o arquivo Baixar uso e a exibição Resumo do uso do portal

Os dados brutos de utilização de recursos são informados até um máximo de seis casas decimais, como pode ser visto no relatório de dados Baixar uso. No entanto, o Enterprise Portal do Azure arredonda o uso para quatro casas decimais para unidades de compromisso e trunca para zero decimais para unidades excedentes. O uso bruto é arredondado primeiro para quatro dígitos antes da conversão para a unidade de medida do Enterprise, e as unidades resultantes do Enterprise são arredondadas novamente para quatro dígitos. As horas reais consumidas antes da conversão são mostradas apenas no relatório Baixar uso e não na própria interface do usuário.

Como exemplo, suponha que 694,533404 horas reais de SQL Server sejam relatadas no relatório Baixar uso. Essas unidades são, então, convertidas em 6,94533404 unidades de 100 horas de computação que, em seguida, são arredondadas para 6,9453 a serem exibidas no Enterprise Portal.

Essas unidades são, então, simplesmente multiplicadas pelo preço unitário do compromisso e o resultado é truncado para dois decimais para determinar o valor da cobrança estendida. Para o iene japonês (JPY) e o won coreano (KRW), o valor estendido é arredondado para zero decimais.

Para esse mesmo exemplo no excedente, as unidades faturáveis seriam truncadas para seis e, em seguida, multiplicadas pelo preço unitário excedente para determinar o valor da cobrança estendida. Para a cobrança do Provedor de Serviços Gerenciados (MSP), todo o uso associado a um departamento marcado como MSP será truncado para zero decimais após a conversão para a unidade de medida do EA, como é feito para relatórios de excedentes. Como resultado, a soma desse uso pode ser menor do que a soma total de todos os usos relatados na interface do usuário, dependendo se o MSP ainda estiver dentro de seu saldo de compromisso monetário ou já estiver em excedente.

### <a name="graduated-pricing"></a>Preços graduados

Atualmente, o preço do programa Enterprise não inclui preços graduados (em que os preços por unidade diminuem à medida que a utilização aumenta). Ao mudar de um programa MOSA com preços graduados para um programa Enterprise, o preço é definido proporcionalmente ao nível base para esse serviço, após o ajuste dos descontos de programas Enterprise, se aplicável.

### <a name="partial-hour-billing"></a>Cobrança de hora parcial

Todo uso é cobrado com base em minutos convertidos em horas parciais no lugar de incrementos de hora inteira.  As taxas corporativas listadas como por hora são simplesmente aplicadas ao total de horas, incluindo quaisquer horas parciais.

### <a name="average-daily-consumption"></a>Consumo diário médio

Quando um serviço é cobrado mensalmente, mas o uso é relatado diariamente, esse uso é avaliado uma vez por dia, dividido por 31 e somado ao longo do número de dias no mês de cobrança. Isso resulta em taxas que nunca são maiores do que o esperado para qualquer mês e são ligeiramente menores para meses com menos de 31 dias.

### <a name="compute-hours-conversion"></a>Conversão de horas de computação

Anteriormente, todo o uso das máquinas virtuais Standard e Basic A0, A2, A3 e A4 e Serviços de Nuvem era emitido como frações (para A0) ou múltiplos (para A2, A3 e A4) de minutos dos medidores das máquinas virtuais A1. Isso causou alguma confusão para nossos clientes, então estamos implementando uma alteração para atribuir o uso por minuto para os medidores A0, A2, A3 e A4 dedicados.

A nova medição entrará em vigor entre 27 de janeiro e 28 de janeiro de 2016. No download do .csv para sua implantação durante esse período de transição, você notará dois itens de linha: um para uso emitido no medidor A1 e outro para uso emitido no novo medidor dedicado correspondente ao tamanho da sua implantação.

| **Tamanho da implantação** | **Uso emitido como múltiplo de A1 até 26 de janeiro de 2016** | **Uso emitido no medidor dedicado a partir de 27 de janeiro de 2016** |
| --- | --- | --- |
| A0 | 0,25 de hora em A1 | 1 hora em A0 |
| A2 | 2 horas em A1 | 1 hora em A2 |
| A3 | 4 horas em A1 | 1 hora em A3 |
| A4 | 8 horas em A1 | 1 hora em A4 |

### <a name="regions"></a>Regiões

Para os serviços em que a zona e a região afetam os preços, a tabela a seguir mostra o mapeamento de regiões geográficas:

| Localização geográfica | Regiões de transferência de dados | Regiões CDN |
| --- | --- | --- |
| Zona 1 | Norte da Europa <br> Europa Ocidental <br> Oeste dos EUA <br> Leste dos EUA <br> Centro-Norte dos EUA <br> Centro-Sul dos EUA <br> Leste dos EUA 2 <br> Centro dos EUA | América do Norte <br> Europa |
| Zona 2 | Leste do Pacífico Asiático <br> Sudeste do Pacífico Asiático <br> Leste do Japão <br> Oeste do Japão <br> Leste da Austrália <br> Sudeste da Austrália | Pacífico Asiático <br> Japão <br> América Latina <br> Oriente Médio e África <br> Leste da Austrália <br> Sudeste da Austrália |
| Zona 3 | Sul do Brasil |   |

**Tabela** **4** **: regiões de transferência de dados**

Não há cobranças para saída de dados entre serviços (por exemplo, O365 e Azure) hospedados no mesmo data center.

### <a name="monetary-commitment-and-unbilled-usage"></a>Compromisso monetário e uso não faturado

O compromisso monetário do Azure é um valor pago adiantadamente pelos serviços do Azure. O compromisso monetário é consumido conforme os serviços são usados. Serviços internos do Azure usam o compromisso monetário. No entanto, há exceções para encargos cobrados separadamente e serviços de Marketplace.

### <a name="charges-billed-separately"></a>Cobranças arrecadadas separadamente

Alguns produtos e serviços fornecidos por fontes de terceiros não consomem o compromisso monetário do Azure. Ao invés disso, esses itens serão cobrados separadamente como parte da fatura excedente do período de cobrança padrão.

Combinamos todas as cobranças do Azure e do Marketplace em uma única fatura que se alinha com o período de cobrança do registro. (Isso não se aplica aos clientes na Austrália, no Japão ou em Cingapura.)

A fatura consolidada mostrará primeiro o uso do Azure, seguido por qualquer cobrança do Marketplace. Os clientes da Austrália, do Japão ou de Cingapura continuarão a receber as cobranças do Marketplace em uma fatura separada.

Se não houver uso excedente no final do período de cobrança padrão, as cobranças arrecadadas separadamente serão faturadas separadamente. (Aplicável aos clientes na Austrália, no Japão e em Cingapura)

**Os serviços cobrados separadamente incluem:**

- Canônico
- Citrix XenApp Essentials
- Usuário registrado do Citrix XenDesktop
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para clientes diretos, as cobranças do Marketplace podem ser conferidas no Portal do EA do Azure. Compras e consumo no Marketplace serão faturadas fora do compromisso monetário e cobradas trimestralmente/mensalmente e em atraso.

Clientes indiretos podem encontrar suas assinaturas do Azure Marketplace na página “Gerenciar Assinaturas” do Portal do EA do Azure, mas os preços estarão ocultos. Os clientes devem entrar em contato com seus LSPs para saber mais sobre encargos do Marketplace.

Novas compras mensais ou anuais recorrentes do Marketplace serão cobradas pelo valor total no período em que os itens do Marketplace foram comprados. Esses itens serão renovados automaticamente após o período seguinte no mesmo dia da compra original.

Isso não afetará nenhuma cobrança recorrente do Marketplace em vigor. As cobranças mensais recorrentes continuarão a ser renovadas no primeiro dia de cada mês do calendário, e as cobranças anuais serão renovadas no aniversário da data de compra.

Alguns serviços de revendedores disponíveis no Azure Marketplace agora consumirão o saldo do compromisso monetário do seu Contrato Enterprise (EA). Anteriormente, esses serviços eram cobrados fora do compromisso monetário do EA, sendo faturados separadamente. O compromisso monetário do EA para esses serviços no Marketplace ajudam a simplificar o gerenciamento de compras e pagamentos do cliente. Para obter uma lista completa dos serviços que agora consomem o compromisso monetário, confira o [site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Parceiros

Os LSPs podem baixar uma lista de preços específica do Marketplace navegando até a tabela de preços no Portal do EA do Azure e clicando no link **Lista de preços do Marketplace** no canto superior direito. A lista de preços do Marketplace refletirá todos os serviços disponíveis e seus preços.

**Para baixar a lista de preços, siga as seguintes etapas:**

1. Navegue até Relatórios > Tabela de Preços.
1. No canto superior direito, localize o link para a lista de preços do Azure Marketplace sob seu nome de usuário.
1. Clique com o botão direito do mouse no link e selecione **Salvar Destino Como**.
1. Na janela Salvar, altere o título do documento para _AzureMarketplacePricelist.zip_, o que alterará o arquivo de *.xlsx para um arquivo *.zip.
1. Quando o download for concluído, você terá um arquivo zip com listas de preços específicas de cada país.
1. Os LSPs devem consultar o arquivo de seu país para obter os preços específicos. Os LSPs devem usar a guia “Notificações” para conferir os SKUs que são novos no Marketplace e os SKUs que foram removidos.
1. As alterações de preço não serão frequentes, mas, quando ocorrerem, os LSPs serão notificados de aumentos de preço e alterações de FX com 30 dias de antecedência por email.
1. Os LSPs receberão uma fatura por registro, por ISV, por trimestre.

### <a name="enabling-marketplace-purchases"></a>Habilitar compras no Marketplace

Os administradores corporativos têm a capacidade de “desabilitar” ou “habilitar” compras no Marketplace para todas as assinaturas do Azure sob esse registro. Se o administrador corporativo desabilitar as compras e houver assinaturas do Azure que já têm assinaturas do Marketplace, essas assinaturas do Marketplace não serão canceladas ou afetadas.

Embora os clientes possam converter suas assinaturas diretas do Azure para o EA associando-as ao registro no Portal do EA do Azure, essa ação não converte automaticamente as assinaturas filhas do Marketplace.

**Para habilitar compras no Marketplace:**

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Navegue até _Gerenciar_.
1. Em _Detalhe do registro_, clique no ícone de lápis ao lado do item de linha _Azure Marketplace_.
1. Selecione _Habilitado/Desabilitado_ ou _Somente SKUs Gratuitas e BYOL\*_ , conforme apropriado.
1. Clique em **Save** (Salvar).

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Cobranças do Marketplace nos relatórios do Portal do EA do Azure

Informações adicionais sobre cobranças do Marketplace podem ser encontradas [aqui](https://azure.microsoft.com/marketplace/faq/).

Há dois tipos de cobranças do Marketplace:

- **Com base no uso:** produtos são medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas por hora enquanto as pesquisas de API do Bing são cobradas pelo número de pesquisas.
- **Não baseadas em uso:** cobrança única ou valor mensal recorrente, que é independente do uso.

As cobranças baseadas em uso e não baseadas em uso serão registradas no relatório de cobrança do Marketplace.

Observe que o Azure Marketplace não está disponível para registros de MPSA.

\*A opção Somente SKUs Gratuitas e BYOL (traga sua própria licença) limitaria a compra e a aquisição de SKUs do Azure Marketplace a apenas SKUs Gratuitas e BYOL.

### <a name="services-billed-hourly-for-ea"></a>Serviços cobrados por hora para o EA

A Rede de Distribuição de Aplicativos e o Firewall de Aplicativo Web são cobrados por hora para o EA em vez da taxa mensal no MOSP.

### <a name="remote-app"></a>Aplicativo remoto

Os clientes do EA pagam pelo aplicativo remoto com base em seu nível de preço EA e não são cobrados adicionalmente. O preço padrão inclui 40 horas iniciais, enquanto o preço ilimitado cobre 80 horas iniciais. O aplicativo remoto para de emitir o uso além das 80 horas.

## <a name="azure-marketplace-faq"></a>Perguntas frequentes sobre o Azure Marketplace

Este documento de perguntas frequentes revisa as atualizações para a aplicabilidade do compromisso monetário do Azure para alguns serviços de terceiros publicados no Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>O que estamos mudando em relação aos serviços do Marketplace e ao compromisso monetário do Azure?

Além das instâncias de VM reservadas do Azure (RIs), os clientes recebem uma fatura separada para todos os serviços comprados no Azure Marketplace. Estamos expandindo o uso do compromisso monetário do Azure para incluir alguns dos serviços do Azure Marketplace publicados por terceiros que são adquiridos com mais frequência por nossos clientes.

### <a name="why-are-we-making-this-change"></a>Por que estamos fazendo essa alteração?

Os clientes estão continuamente procurando maneiras adicionais de utilizar o pagamento antecipado que fizeram na forma de compromisso monetário do Azure.  Atendemos a uma solicitação frequente dos clientes, impactando uma grande parte dos nossos clientes do Azure Marketplace ao expandirmos o MC para esses serviços.

### <a name="what-is-the-customer-benefit"></a>O que é o benefício do cliente?

Os clientes obtêm uma experiência de cobrança mais simples e a possibilidade de garantir que gastem seu compromisso monetário do Azure.  Adicionar esse benefício ao compromisso monetário pré-pago e aos RIs usando o MC agrega ainda mais valor ao compromisso monetário do Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Quais serviços serão deduzidos do compromisso monetário do Azure e como meu cliente saberá?

Durante a experiência de compra no Azure Marketplace, distinguiremos cada serviço que usará o compromisso monetário com um aviso de isenção de responsabilidade. Os fornecedores com suporte no momento incluem determinados serviços publicados pela Red Hat, SUSE, Autodesk e Oracle. Os serviços que têm convenções de nomenclatura semelhantes, mas que são publicados por outras partes não identificadas acima não serão deduzidos do compromisso monetário. Uma lista completa está disponível no final das perguntas frequentes.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>E se meu cliente esgotar o compromisso monetário?

Para os clientes que consumiram todos os seus MC e estão agora em excedente, as cobranças relacionadas a esses serviços aparecerão na próxima fatura excedente, com quaisquer outros serviços de consumo.  Isso foi alterado, uma vez que essas cobranças teriam sido faturadas em suas próprias faturas com outras ofertas do Azure Marketplace.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Por que não habilitamos o compromisso monetário do Azure para todas as compras do Marketplace?

Estamos sempre trabalhando para fornecer a melhor experiência do cliente relacionada ao compromisso monetário do Azure. Essa alteração afetará uma grande quantidade de clientes e uma parte significativa do gasto total no Azure Marketplace. Outros serviços poderão ser adicionados no futuro.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Como isso afeta o registro indireto e os parceiros?

Nossos clientes ou parceiros de registro indireto não serão afetados. Esses serviços estão sujeitos aos mesmos recursos de marcação de parceiros que outros serviços de consumo. A única alteração será a fatura na qual elas aparecem e o pagamento de cobranças do compromisso monetário.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Lista de serviços que serão deduzidos do compromisso monetário do Azure

As ofertas específicas do Azure Marketplace podem usar fundos do compromisso monetário. Confira [Compromisso monetário do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa de produtos participantes desse programa.

## <a name="additional-information"></a>Informações adicionais

Confira essas fontes de informação adicionais para saber mais. Esses arquivos são atualizados duas vezes por mês, no dia 6 e no dia 20. Confira a seguir os detalhes de cada arquivo:

| Título do apêndice | DESCRIÇÃO | Convenção de nomenclatura de URL |
| --- | --- | --- |
| [**Nomes de serviços amigáveis**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Fornece uma lista de todos os serviços ativos com a categoria de serviço, nome de serviço amigável, nome do compromisso e número de bloco, nome de consumo e número de bloco, unidades de medida e fatores de conversão entre o uso relatado e o uso exibido no Enterprise Portal. | Nomes\_Serviços\_Amigáveis.xlsx |
| [**Campos de download do serviço**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Esta planilha fornece uma lista de todas as combinações possíveis dos campos relacionados ao serviço no relatório Baixar uso. | Campos\_Download\_Serviço.xlsx |

**Tabela** **5** **: fontes de informação adicionais**

## <a name="power-bi-reporting"></a>Relatórios do Power BI

### <a name="power-bi-pro"></a>Power BI Pro

O Power BI Pro agora está disponível para clientes EA. Com o Power BI Pro, você pode gerar e compartilhar relatórios para gerenciar efetivamente seus dados de custo, com recursos adicionais de colaboração e atualização de dados. O Power BI Pro oferece maior capacidade de dados e limites de fluxo de dados. Novos recursos de gerenciamento de custos empolgantes para clientes do Azure Enterprise estarão disponíveis em breve.

Usuários gratuitos do Power BI que usam o pacote de conteúdo do Microsoft Azure Consumption Insights são elegíveis para uma avaliação gratuita de 60 dias do Power BI Pro. Se você quiser continuar usando Power BI Pro após a avaliação gratuita, poderá fazer isso obtendo uma licença.

Para se inscrever para a avaliação gratuita, vá para o ícone de engrenagem e selecione **Gerenciar armazenamento pessoal**. Em seguida, selecione **Experimente o Pro gratuitamente** à direita. Confira [Inscrição para autoatendimento do Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) para saber mais sobre a avaliação gratuita do Power BI Pro.

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>***Períodos da avaliação do Power BI Pro para o Microsoft Azure EA

- **Uso geral**: O Power BI Pro estendido para a oferta de avaliação do pacote de conteúdo "Microsoft Azure Enterprise" (a "Oferta") está disponível para os usuários qualificados existentes durante o período da oferta, para permitir que eles acessem determinadas informações relacionadas ao consumo do Microsoft Azure usando um pacote de conteúdo do Power BI específico.
- **Elegibilidade**: Os usuários sob um EA (Enterprise Agreement) poderão participar da oferta se tiverem uma função relacionada à cobrança, serviço ou ao gerenciamento de serviço e/ou custos do Microsoft Azure em sua organização.
- **Exclusões**:
  - Os usuários que já participam da versão de avaliação estendida do Power BI Pro continuarão qualificados na oferta pré-existente e poderão não entrar na oferta de avaliação do Power BI Pro para Azure EA.
  - Os usuários que participam da oferta só poderão usar o Power BI Pro com o pacote de conteúdo do Microsoft Azure Enterprise. Qualquer outro uso do Power BI Pro é proibido.
  - Período: A oferta inicia em 1º de junho de 2017 e termina em 31 de maio de 2018.  A aceitação pode ocorrer a qualquer momento durante o período de 12 meses, embora a oferta termine em 31 de maio de 2018 para todos os usuários, independentemente de quando eles aceitaram a Oferta.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Para acessar o pacote de conteúdo do Microsoft Azure Consumption Insights:

1. Navegue até [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Clique em **Obtenha agora**.
1. Forneça o número de registro e o número de meses. Clique em **Próximo**.
1. Forneça a chave de acesso da API para se conectar. A chave do seu registro está disponível no [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Clique em **Entrar**.
1. O processo de importação inicia automaticamente. Depois de concluído, um novo painel, relatório e modelo serão exibidos no painel de navegação. Clique no painel para exibir os dados importados.

Para saber mais sobre como gerar a chave de API para seu registro, visite o arquivo de ajuda sobre Relatórios de API no[Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Para saber mais sobre o novo pacote de conteúdo, baixe o documento [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Para acessar o pacote de conteúdo herdado do Power BI para EA:

 1. Navegue até o [site do Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Entre com uma conta corporativa ou de estudante válida.
    - A conta corporativa ou de estudante pode ser a mesma ou uma diferente da usada para acessar o registro pelo Portal do EA do Azure.
 1. No painel de serviços, selecione **Microsoft Azure Enterprise** e clique em **Conectar**.
 1. Na tela "Conectar-se ao Azure Enterprise", escolha:
    - URL do ambiente do Azure: [https://ea.azure.com](https://ea.azure.com/).
    - Número de meses: escolha entre 1 e 36.
    - Número do registro: insira o número do registro.
    - Clique em **Próximo**.
 1. Na caixa Chave de Autenticação, insira a Chave de API. Acesse a chave de API aqui no Enterprise Portal do Azure, na guia "Baixar uso" acima, clicando em"Chave de Acesso da API"
    - Copie e cole a chave na caixa "Chave de Conta"
 1. Os dados levarão entre 5 e 30 minutos para carregar no Power BI, dependendo do tamanho dos conjuntos de dados.

O Power BI Reporting está disponível para clientes diretos, indiretos e parceiros do EA que tenham acesso para exibir informações de cobrança.

## <a name="report-faq"></a>Perguntas frequentes de relatórios

Esta seção do artigo responde a perguntas comuns relacionadas à interpretação de relatórios.

### <a name="why-is-my-cost-showing-as-0"></a>Por que meu custo é mostrado como US$ 0,00?

**Registro direto** Se você é um proprietário de conta ou administrador de departamento, entre em contato com seu administrador do EA para habilitar o recurso de preços:

1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique no símbolo de lápis azul ao lado de Exibir cobranças de DA (administrador de departamento).
1. Selecione **Habilitado** e salve.
1. Clique no lápis azul ao lado de Exibir cobranças de AO (proprietário de conta).
1. Selecione **Habilitado** e salve.

Essa ação fornecerá aos proprietários de conta e aos administradores de departamento acesso a informações de custo/preço nos relatórios de uso.

**Registro indireto** Verifique com seu parceiro se ele habilitou o recurso de preços para você. Isso só pode ser feito pelo parceiro e, depois que eles ativarem o recurso, você poderá exibir o custo e os preços de seu registro como um administrador do EA.

Se quiser habilitar o recurso Exibir cobranças para seus proprietários de conta e administradores de departamento, siga as etapas listadas acima em **Registro direto**.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Não há informações de SKU no relatório Detalhes de uso

O relatório Detalhes de uso não tem informações de SKU; no entanto, é possível exibir as informações de serviço utilizadas no relatório. Em seguida, baixe o relatório Tabela de preços para obter as informações de SKU.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>O valor total no Marketplace não corresponde ao resumo de uso e ao relatório CSV

O Relatório de cobranças do Marketplace exibe apenas as cobranças do Marketplace com base no uso. Valores únicos não são exibidos. Você pode consultar a página Resumo de uso para obter os usos mais atualizados nas cobranças com base no uso e nos valores únicos.

### <a name="there-is-no-information-on-my-api-report"></a>Não há informações em meu relatório de API

As chaves de API expiram a cada seis meses. Gere uma nova chave de API se estiver enfrentando um problema. Também é importante solicitar que o administrador do EA gere as novas chaves de API e siga as etapas nas perguntas frequentes sobre o relatório de API.

### <a name="my-power-bi-report-isnt-working"></a>Meu relatório do Power BI não está funcionando

Para solucionar problemas com o Power BI, envie um tíquete técnico para a equipe do Power BI em [https://support.powerbi.com](https://support.powerbi.com) para que a equipe possa ajudar.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>Minhas marcas de recurso não estão aparecendo em meus relatórios

As marcas de recurso são gerenciadas no portal do Azure. Você pode entrar em contato com a equipe de assinatura do Azure em [https://portal.azure.com](https://portal.azure.com). Siga as etapas [neste link](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para gerar uma solicitação de suporte.

### <a name="why-does-my-resource-rate-change-every-day"></a>Por que minha taxa de recursos muda todos os dias?

A taxa de recursos no relatório Detalhes de uso é um valor calculado e representa a taxa mensal média cobrada por um serviço. Essa taxa é calculada usando a média do compromisso mensal e as cobranças mensais excedentes para uma unidade de serviço. A porção de uso cobrada do compromisso e das tarifas excedentes mudará para o dia em que o mês for fechado. Por isso, a taxa de recursos também será alterada durante o mês. A taxa de recursos é bloqueada no quinto dia após o fim do mês.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossário de processos para calcular a taxa de recursos

**Total de unidades BRUTAS**: quantidade consumida no relatório de uso detalhado.
**Recurso de MOCP por unidade**: o sistema de uso upstream emite os usos para cada serviço em unidades diferentes. (Predefinição) **Consumo por unidade**: unidade de medida do EA. (Predefinição) **Preço**: preço da unidade do Portal do EA do Azure.
**Custo total**: custo estendido do relatório Detalhes de uso ou uso do compromisso + excedente do Portal do EA do Azure.


### <a name="charges-calculation"></a>Cálculo de cobranças

**Converter em recurso MOCP por unidade** = ARREND(Unidades BRUTAS totais*Recurso de MOCP por unidade,4) **Converter em unidades** = Unidades após a conversão em recursos de MOCP por unidade/Consumo por unidade **Custo total** = Unidades*Preço

### <a name="download-usage-calculation-logic"></a>Lógica de cálculo de Baixar uso

**Taxa de recursos** = Custo total/(Total de unidades BRUTAS/Recurso MOCP por unidade)

Deriva-se a taxa de recursos com base nas cobranças e, geralmente, isso não corresponde ao preço unitário real na tabela de preços.

Para cálculos de excedente, os dados brutos de utilização de recursos são informados até um máximo de seis casas decimais, como pode ser visto no relatório de dados Baixar uso. No entanto, o Portal do EA do Azure arredonda o uso para quatro casas decimais para unidades de compromisso e trunca para zero decimais para unidades excedentes. Isso significa que, no Portal do EA do Azure, para todo uso cobrado como excedente, cobramos apenas pelas unidades completas. Haverá uma grande diferença entre o preço unitário e a taxa de recursos para uso cobrado como excedente ou em meses mistos.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como funcionam as faturas e as cobranças, confira [Entender a fatura do Contrato Enterprise do Azure](billing-understand-your-bill-ea.md).
- Para começar a usar o Portal do EA do Azure, confira [Introdução ao Portal do EA do Azure](billing-ea-portal-get-started.md).
