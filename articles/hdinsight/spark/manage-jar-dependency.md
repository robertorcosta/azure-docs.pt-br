---
title: Gerenciar dependências JAR - Azure HDInsight
description: Este artigo discute as melhores práticas para gerenciar as dependências do Java Archive (JAR) para aplicativos HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135727"
---
# <a name="jar-dependency-management-best-practices"></a>Práticas recomendadas de gestão de dependência sinD

Os componentes instalados em clusters HDInsight têm dependências de bibliotecas de terceiros. Normalmente, uma versão específica de módulos comuns como goiaba é referenciada por esses componentes incorporados. Quando você envia um aplicativo com suas dependências, ele pode causar um conflito entre diferentes versões do mesmo módulo. Se a versão do componente que você faz referência no classpath primeiro, os componentes incorporados podem lançar exceções devido à incompatibilidade da versão. No entanto, se os componentes incorporados injetarem suas dependências `NoSuchMethod`no classpath primeiro, seu aplicativo pode lançar erros como .

Para evitar conflitos de versão, considere sombrear suas dependências de aplicativos.

## <a name="what-does-package-shading-mean"></a>O que significa sombreamento de pacotes?
O sombreamento fornece uma maneira de incluir e renomear dependências. Ele realoca as classes e regrava o bytecode e os recursos afetados para criar uma cópia privada de suas dependências.

## <a name="how-to-shade-a-package"></a>Como sombrear um pacote?

### <a name="use-uber-jar"></a>Use uber-jar
Uber-jar é um único arquivo de jarro que contém tanto o frasco de aplicativo quanto suas dependências. As dependências do Uber-jar não são sombreadas. Em alguns casos, isso pode introduzir conflito de versão se outros componentes ou aplicativos referenciarem uma versão diferente dessas bibliotecas. Para evitar isso, você pode construir um arquivo Uber-Jar com algumas (ou todas) das dependências sombreadas.

### <a name="shade-package-using-maven"></a>Pacote de sombra usando Maven
Maven pode construir aplicações escritas tanto em Java quanto em Scala. O plugin maven-shade pode ajudá-lo a criar um uber-jar sombreado facilmente.

O exemplo abaixo `pom.xml` mostra um arquivo que foi atualizado para sombrear um pacote usando o plugin maven-shade.  A seção `<relocation>…</relocation>` XML `com.google.guava` move `com.google.shaded.guava` as classes do pacote para o pacote, movendo as entradas de arquivo JAR correspondentes e reescrevendo o bytecode afetado.

Depois `pom.xml`de mudar, `mvn package` você pode executar para construir o uber-jar sombreado.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Pacote de sombra usando SBT
O SBT também é uma ferramenta de construção para Scala e Java. O SBT não tem um plugin de sombra como o plugin maven-shade. Você pode `build.sbt` modificar arquivo para pacotes de sombra. 

Por exemplo, `com.google.guava`para sombrear, você pode `build.sbt` adicionar o comando abaixo ao arquivo:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Então você `sbt clean` pode `sbt assembly` correr e construir o arquivo de frasco sombreado. 

## <a name="next-steps"></a>Próximas etapas

* [Use ferramentas HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Crie um aplicativo Scala Maven para Spark na IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
