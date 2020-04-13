---
title: Kit de desenvolvimento Xamarin.Android e Java 9
description: Este artigo explica como resolver java development kit (JDK) 9 ou posteriores erros no Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026828"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Kit de desenvolvimento Xamarin.Android e Java 9 ou posterior

_Este artigo explica como resolver java development kit (JDK) 9 ou posteriores erros no Xamarin.Android._

## <a name="overview"></a>Visão geral

Xamarin.Android usa o Java Development Kit (JDK) para integrar com o Android SDK para construir aplicativos Android e executar o designer Android. As versões mais recentes do Android SDK (API 24 ou superior) exigem JDK 8 (1.8) ou o Microsoft Mobile OpenJDK Preview. **Como as ferramentas Android SDK disponíveis no Google ainda não são compatíveis com jDK 9, o Xamarin.Android não funciona com JDK 9 ou posterior.**

## <a name="jdk-errors"></a>Erros de JDK

Se você tentar construir um projeto Xamarin.Android com uma versão do JDK mais tarde que jDK 8, você terá um erro explícito indicando que esta versão do JDK não é suportada. Por exemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Para resolver esses erros, você deve instalar o JDK 8 (1.8) conforme explicado em [Como atualizar a versão Java Development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md).
Alternativamente, você pode instalar o [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) O Microsoft Mobile OpenJDK eventualmente substituirá o JDK 8 pelo desenvolvimento do Xamarin.Android.

## <a name="checking-the-jdk-version"></a>Verificando a versão JDK

Você pode verificar para ver qual versão do Java você instalou `bin` inserindo o `PATH`seguinte comando (o diretório JDK deve estar em seu ):

```shell
java -version
```

Se o JDK 9 estiver instalado, você verá uma mensagem como a seguinte:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se o JDK 9 ou posterior estiver instalado, você deve instalar o Java JDK 8 (1.8) ou o Microsoft Mobile OpenJDK Preview. Para obter informações sobre como instalar o JDK 8, consulte [Como atualizar a versão do Java Development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md). Para obter informações sobre como instalar o Microsoft Mobile OpenJDK, consulte [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Observe que você não precisa desinstalar uma versão posterior do JDK; no entanto, você deve garantir que Xamarin está usando JDK 8 em vez de uma versão JDK posterior. No Visual Studio, clique em **Ferramentas > Opções > Xamarin > Configurações do Android**. Se **o Java Development Kit Location** não estiver definido como um local JDK 8 (como **C:\\Arquivos do\\programa Java\\jdk1.8.0_111**), clique em **Alterar** e configurá-lo no local onde o JDK 8 está instalado. No Visual Studio para Mac, navegue **até Preferências > Projetos > Locais SDK > Android > Java SDK (JDK)** e clique **em Procurar** para atualizar este caminho.

## <a name="known-issues-with-jdk-9"></a>Problemas conhecidos com JDK 9

### <a name="apksigner"></a>apksigner

Há um problema conhecido com apksigner e JDK 9 `apksigner.jar` `-Djava.ext.dirs` em `-classpath` que o `apksigner.bat` arquivo invoca o com em vez do qual JDK 9 espera. Recomenda-se o uso de JDK 8 (1.8). Para obter informações sobre como instalar o JDK 8, consulte [Como atualizar a versão java development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md)

Se você tiver instalado o JDK 9, certifique-se `PATH` de que o caminho a seguir `C:\ProgramData\Oracle\Java\javapath`não está definido na variável do ambiente, pois ele ainda apontará para JDK 9: . Depois de removê-lo, `java-version` em uma linha de comando deve mostrar JDK 8.
