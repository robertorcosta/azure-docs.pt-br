---
title: Autenticar runbooks da Automação do Azure com a Amazon Web Services
description: Este artigo descreve como criar e validar uma credencial do AWS para runbooks na Automação do Azure que gerenciam recursos do AWS.
keywords: autenticação aws, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1bf60d17af1d9866de6a62ac538fa0bd9a15ce52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82113384"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Autenticar runbooks da Automação do Azure com a Amazon Web Services

A automação de tarefas comuns com recursos do AWS (Amazon Web Services) pode ser realizada com runbooks de Automação no Azure. Você pode automatizar muitas tarefas no AWS usando runbooks de Automação, assim como faz com recursos no Azure. Bastam duas coisas:

* Uma assinatura do AWS e um conjunto de credenciais. Especificamente, sua Chave Secreta e sua Chave de Acesso do AWS. Para saber mais, examine o artigo [Uso de credenciais do AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma assinatura do Azure e uma conta de Automação.

Para a autenticação com o AWS, você deve especificar um conjunto de credenciais do AWS para autenticar seus runbooks em execução da Automação do Azure. Se já tiver criado uma conta de Automação e quiser usá-la para a autenticação com o AWS, você poderá executar as etapas na seção a seguir. Se você quiser dedicar uma conta para runbooks direcionados a recursos de AWS, primeiro crie uma nova [conta de automação](automation-create-standalone-account.md) e ignore a etapa para criar uma conta Executar como. Depois de criar a conta, siga as etapas abaixo para concluir a configuração.

## <a name="configure-automation-account"></a>Configurar conta de Automação

Para que a Automação do Azure se comunique com o AWS, primeiro você precisará recuperar suas credenciais do AWS e armazená-las como ativos na Automação do Azure. Execute as etapas a seguir registradas no documento do AWS [Gerenciamento de Chaves de Acesso para sua conta do AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copie a **ID da Chave de Acesso** e a **Chave de Acesso do Segredo** (opcionalmente, baixe o arquivo de chave para armazená-lo em um lugar seguro).

Após criar e copiar as chaves de segurança do AWS, você precisará criar um ativo de Credencial com uma conta de Automação do Azure para armazená-las com segurança e fazer referência a elas com seus runbooks. Siga as etapas na seção: **Como criar uma credencial nova** no artigo [Ativos de credenciais na Automação do Azure](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) e insira as seguintes informações:

1. Na caixa **Nome**, digite **AWScred** ou um valor apropriado seguindo seus padrões de nomenclatura.
2. Na caixa **Nome de usuário**, digite a **ID de Acesso** e a **Chave de Acesso do Segredo** nas caixas **Senha** e **Confirmar senha**.

## <a name="next-steps"></a>Próximas etapas

* Examine o artigo sobre solução [Automatização da implantação de uma VM no Amazon Web Services](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar tarefas no AWS.
