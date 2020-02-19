---
title: Conectar uma conta do Amazon Web Services para o Cloudyn no Azure | Microsoft Docs
description: Conectar uma conta do Amazon Web Services para exibição dos dados de uso e custo em relatórios do Cloudyn.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: 28229ad71327daefb8e42881cf001b6a3ddd3a53
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086849"
---
# <a name="connect-an-amazon-web-services-account"></a>Conectar uma conta do Amazon Web Services

Você tem duas opções para conectar sua conta do AWS (Amazon Web Services) ao Cloudyn. Você pode conectar a uma função IAM ou a uma conta de usuário IAM somente leitura. A função IAM é recomendada porque permite que você delegue o acesso com permissões definidas para entidades confiáveis. A função IAM não requer que você compartilhe chaves de acesso de longo prazo. Após conectar uma conta AWS ao Cloudyn, os dados de uso e custo estão disponíveis nos relatórios do Cloudyn. Este documento irá guiá-lo através de ambas as opções.

Para obter mais informações sobre as identidades IAM do AWS, consulte [Identidades (Usuários, Grupos e Funções)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Além disso, você habilita os relatórios de cobrança detalhados do AWS e armazena as informações em bucket de serviço (S3) de armazenamento simples do AWS. Relatórios detalhados de cobrança incluem encargos de cobrança com informações de marca e recurso por hora. Armazenar os relatórios permite ao Cloudyn recuperá-los do bucket e exibir as informações em seus relatórios.


## <a name="aws-role-based-access"></a>Acesso baseado em função do AWS

As seções a seguir detalham como criar uma função IAM somente leitura para fornecer acesso ao Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Obtenha sua ID externa de conta do Cloudyn

A primeira etapa é obter a frase secreta de conexão exclusiva do portal do Cloudyn. Ela é usada em AWS como a **ID Externa**.

1. Abra o portal da Cloudyn por meio do portal do Azure ou navegue até [https://azure.cloudyn.com](https://azure.cloudyn.com) e entre.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas de Nuvem**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +** .
4. Na caixa de diálogo **Adicionar Conta do AWS**, copie a **ID Externa** e salve o valor para as etapas de criação da Função do AWS na próxima seção. A ID externa é exclusiva para a sua conta. Na imagem a seguir, a ID externa de exemplo é _Contoso_ seguida por um número. Sua ID é diferente.  
    ![ID externa mostrada na caixa Adicionar conta do AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Adicionar o acesso baseado em função somente leitura do AWS

1. Entre no console do AWS em [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) e selecione **Funções**.
2. Clique em **Criar Função** e, em seguida, selecione **Outra Conta do AWS**.
3. Na caixa **ID da conta**, cole `432263259397`. Essa ID da Conta é a conta do coletor de dados do Cloudyn atribuída pelo AWS ao serviço Cloudyn. Use a ID da conta exata mostrada.
4. Ao lado de **Opções**, selecione **Exigir ID externa**. Cole o valor exclusivo que copiou anteriormente do campo **ID Externa** no Cloudyn. Em seguida, clique em **Próximo: Permissões.**  
    ![Cole a ID externa do Cloudyn na página Criar função](./media/connect-aws-account/create-role01.png)
5. Em **Anexar políticas de permissões**, na pesquisa de caixa de filtros **Tipo de política** digite `ReadOnlyAccess`, selecione **ReadOnlyAccess** e, em seguida, clique em **Próximo: Revisão**.  
    ![Selecione o acesso somente leitura na lista de nomes de política](./media/connect-aws-account/readonlyaccess.png)
6. Na página de Revisão, assegure-se de que suas seleções estão corretas e digite um **Nome da função**. Por exemplo, *Azure-Cost-Mgt*. Insira uma **Descrição da função**. Por exemplo, _Atribuição de função do Cloudyn_ e, em seguida, clique em **Criar função**.
7. Na lista **Funções**, clique na função criada e copie o valor da **ARN da Função** na página Resumo. Use o valor de ARN (Amazon Resource Name) da Função posteriormente ao registrar sua configuração no Cloudyn.  
    ![Copie a função ARN da página de Resumo](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Configurar o acesso da função do IAM do AWS no Cloudyn

1. Abra o portal da Cloudyn por meio do portal do Azure ou navegue até https://azure.cloudyn.com/ e entre.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas de Nuvem**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +** .
4. Em **Nome da Conta**, digite um nome para a conta.
5. Próximo ao **Tipo de Acesso**, selecione **Função do IAM**.
6. No campo **ARN da Função**, cole o valor copiado anteriormente e, em seguida, clique em **Salvar**.  
    ![Cole a função ARN na caixa Adicionar conta do AWS](./media/connect-aws-account/add-aws-account-box.png)


A conta do AWS aparece na lista de contas. A **ID do Proprietário** listada corresponde ao valor ARN da Função. O **Status da conta** deve ter um símbolo de marca de seleção verde, indicando que o Cloudyn pode acessar sua conta do AWS. Até você habilitar a cobrança do AWS detalhada, seu status de consolidação será exibido como **Autônomo**.

![Status da conta do AWS mostrado na página Gerenciamento de Contas](./media/connect-aws-account/aws-account-status01.png)

O Cloudyn começa coletando dados e populando relatórios. Em seguida, [habilite a cobrança do AWS detalhada](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Acesso baseado no usuário do AWS

As seções a seguir detalham como criar um usuário somente leitura para proporcionar acesso ao Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Adicionar acesso baseado em usuário somente leitura do AWS

1. Entre no console do AWS em [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) e selecione **Usuários**.
2. Clique em **Adicionar Usuário**.
3. No campo **Nome de usuário**, digite um nome de usuário.
4. Para **Tipo de Acesso**, selecione **Acesso programático** e clique em **Próximo: Permissões**.  
    ![Insira um nome de usuário na página Adicionar usuário](./media/connect-aws-account/add-user01.png)
5. Para permissões, selecione **Anexar políticas existentes diretamente**.
6. Em **Anexar políticas de permissões**, na pesquisa de caixa de filtros **Tipo de política**, digite `ReadOnlyAccess`, selecione **ReadOnlyAccess** e, em seguida, clique em **Próximo: Revisão**.  
    ![Selecione ReadOnlyAccess para definir permissões para o usuário](./media/connect-aws-account/set-permission-for-user.png)
7. Na página de Revisão, assegure-se de que suas seleções estão corretas e, em seguida, clique em **Criar usuário**.
8. Página de Conclusão, sua ID de Chave de Acesso e a tecla de acesso Secreta são mostradas. Essas informações são usadas para configurar o registro no Cloudyn.
9. Clique em **Baixar CSV** e salve o arquivo credentials.csv em um local seguro.  
    ![Clique em Baixar .csv para salvar as credenciais](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Configurar o acesso baseado no usuário do AWS do IAM no Cloudyn

1. Abra o portal da Cloudyn por meio do portal do Azure ou navegue até https://azure.cloudyn.com/ e entre.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas de Nuvem**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +** .
4. Para **Nome da Conta**, digite um nome da conta.
5. Próximo ao **Tipo de Acesso**, selecione o **Usuário do IAM**.
6. Em **Chave de Acesso**, cole o valor da **ID da chave de acesso**  do arquivo credentials.csv.
7. Em **Chave Secreta**, cole o valor da **Tecla de acesso secreta**  do arquivo credentials.csv e, em seguida, clique em **Salvar**.  

A conta do AWS aparece na lista de contas. O **Status da Conta** deverá ter um símbolo de marca de seleção verde.

O Cloudyn começa coletando dados e populando relatórios. Em seguida, [habilite a cobrança do AWS detalhada](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Habilitar a cobrança do AWS detalhada

Use as etapas a seguir para obter o ARN da Função do AWS. Use o ARN da Função para conceder permissões de leitura a um bucket de cobrança.

1. Entre no console do AWS em [https://console.aws.amazon.com](https://console.aws.amazon.com) e selecione **Serviços**.
2. Na caixa Pesquisa de Serviço, digite *IAM* e selecione essa opção.
3. Selecione **Funções** no menu à esquerda.
4. Na lista de funções, selecione a função que você criou para acesso ao Cloudyn.
5. Na página Resumo de Funções, clique para copiar o **ARN da Função**. Mantenha o ARN de Função à mão para etapas posteriores.

### <a name="create-an-s3-bucket"></a>Criar um bucket S3

Crie um bucket S3 para armazenar informações de cobrança detalhadas.

1. Entre no console do AWS em [https://console.aws.amazon.com](https://console.aws.amazon.com) e selecione **Serviços**.
2. Na caixa Pesquisa de Serviço, digite *S3* e selecione **S3**.
3. Na página Amazon S3, clique em **Criar bucket**.
4. No assistente Criar bucket, escolha um nome de Bucket e Região e, em seguida, clique em **Avançar**.  
    ![Informações de exemplo na página Criar bucket](./media/connect-aws-account/create-bucket.png)
5. Na página **Definir propriedades**, mantenha os valores padrão e, em seguida, clique em **Avançar**.
6. Na página Revisão, clique em **Criar bucket**. A lista de buckets é exibida.
7. Clique no bucket que você criou e selecione a guia **Permissões** e, em seguida, selecione **Política de Bucket**. O editor de políticas de Bucket é aberto.
8. Copie o JSON de exemplo a seguir e cole-o no editor de políticas de Bucket.
   - Substitua `<BillingBucketName>` pelo nome do bucket de S3.
   - Substitua `<ReadOnlyUserOrRole>` pelo ARN de Função ou Usuário que você copiou anteriormente.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Clique em **Save** (Salvar).  
    ![Clique em Salvar no editor de Política de bucket](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Habilitar relatórios de cobrança de AWS

Depois de criar e configurar o bucket de S3, navegue até [Preferências de Cobrança](https://console.aws.amazon.com/billing/home?#/preference) no console do AWS.

1. Na página Preferências, selecione **Receber relatórios de Cobrança**.
2. Em **Receber relatórios de Cobrança**, insira o nome do bucket que você criou e, em seguida, clique em **Verificar**.  
3. Selecione todas as quatro opções de granularidade de relatório e, em seguida, clique em **Salvar preferências**.  
    ![Selecione a granularidade para habilitar relatórios](./media/connect-aws-account/enable-reports.png)

O Cloudyn recupera informações detalhadas de cobrança do seu bucket de S3 e preenche relatórios depois de a cobrança detalhada ser habilitada. Pode levar até 24 horas para os dados detalhados de cobrança aparecerem no console do Cloudyn. Quando dados detalhados de cobrança estiverem disponíveis, o status de consolidação da conta será exibido como **Consolidado**. O status da conta é exibido como **Concluído**.

![Status de consolidação mostrado na guia Contas do AWS](./media/connect-aws-account/consolidated-status.png)

Alguns dos relatórios de otimização podem exigir alguns dias de dados para obter um tamanho de amostra de dados adequado para recomendações precisas.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Cloudyn, continue no tutorial [Revisar o uso e os custos](tutorial-review-usage.md) do Cloudyn.
