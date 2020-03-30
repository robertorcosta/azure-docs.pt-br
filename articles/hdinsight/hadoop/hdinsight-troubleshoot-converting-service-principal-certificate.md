---
title: Conversão do conteúdo do certificado para base-64 - Azure HDInsight
description: Conversão do conteúdo do certificado principal do serviço para o formato de seqüência codificado base-64 no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894185"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversão do conteúdo do certificado principal do serviço para o formato de seqüência codificado base-64 no HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe uma mensagem de erro afirmando que a entrada não é uma seqüência de caracteres Base-64 válida, pois contém um caractere não-base 64, mais de dois caracteres de preenchimento ou um caractere de espaço não branco entre os caracteres de preenchimento.

## <a name="cause"></a>Causa

Ao usar a implantação do modelo PowerShell ou Azure para criar clusters com o Data Lake como armazenamento principal ou adicional, o conteúdo do certificado principal de serviço fornecido para acessar a conta de armazenamento Data Lake está no formato base-64. A conversão indevida do conteúdo do certificado pfx para a seqüência codificada base-64 pode levar a esse erro.

## <a name="resolution"></a>Resolução

Depois de ter o certificado principal de serviço no formato pfx (veja [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) para as etapas principais de criação do serviço de amostra), use o seguinte comando PowerShell ou c# snippet para converter o conteúdo do certificado para o formato base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
