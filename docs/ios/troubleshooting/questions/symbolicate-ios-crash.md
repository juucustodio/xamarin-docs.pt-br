---
title: Onde posso encontrar o arquivo .dSYM symbolicate logs de falha de iOS?
ms.topic: article
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 223e1977bb6229760d6428fdca2f5372b1e25c23
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Onde posso encontrar o arquivo .dSYM symbolicate logs de falha de iOS?

Ao criar aplicativos do iOS do visual studio, .dSYM final do arquivo que pode ser usado para relatórios de falha de symbolicate no host de compilação no caminho:
```
    /Users/<username>/Library/Caches/Xamarin/mtbs/builds/<appname>/<guid>/bin/iPhone/<configuration>
```

Observe que o `~/Library` pasta está oculto por padrão no localizador, portanto, se precisa usar o localizador **Ir > vá para a pasta** menu e digite: `~/Library/Caches/Xamarin/mtbs/builds/` para abrir a pasta.  

Como alternativa, você pode reexibir o `~/Library` pasta usando o **Mostrar opções de exibição** painel para sua pasta base. Se você selecionar a pasta base na barra lateral do localizador e use o menu de localizador **exibição > Mostrar opções de exibição** (ou cmd-j), em seguida, você verá uma caixa de seleção para **Mostrar pasta de biblioteca**.


### <a name="see-also"></a>Consulte também
- Relatórios de falhas de etapas estendidas para symbolicating iOS: [http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmistificando Logs de falhas de aplicativo do iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
