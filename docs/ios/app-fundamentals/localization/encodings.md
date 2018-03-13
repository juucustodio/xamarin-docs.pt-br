---
title: "Codificações de internacionalização"
ms.topic: article
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 3829d8ed94bab482b0da9e52e5ee6e1f488e3992
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="internationalization-encodings"></a>Codificações de internacionalização

Nem todas as codificações são incluídas na biblioteca de classes xamarin por padrão.

Para reduzir o tamanho do aplicativo, xamarin não inclui qualquer codificação específica, e você deve instruir mtouch para incluir os assemblies que contêm o suporte para a codificação que você precisa.

Isso é feito selecionando as codificações extras no painel de compilação/avançado iOS do Visual Studio para Mac ou o Visual Studio:

 [![](encodings-images/00.png "Selecionar as codificações extras")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Selecionar as codificações extras")](encodings-images/00a.png#lightbox)

Você pode selecionar uma destas opções:

-  CJK: Chineese, japonês e coreano
-  Ásia: árabe, hebraico, turco e Latin5.
-  outros: cirílico, báltico, vietnamita, ucraniano e tailandês
-  raro: EBCDIC codificações e outras páginas de código raros
-  Oeste: idiomas latinos Páscoa e Europa Ocidental
-  all


 <a name="cjk" />


## <a name="cjk"></a>cjk

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>Ásia

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>outras

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>raros

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>Oeste

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

