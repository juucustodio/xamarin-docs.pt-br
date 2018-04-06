---
title: mtouch
ms.prod: xamarin
ms.assetid: BCA491DA-E4C1-8689-3EC9-E4C72495A798
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 933ad24a8778ffbee3a1b6089c6ebcf33d26bf84
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="mtouch"></a>mtouch


Aplicativos do iPhone são enviados como pacotes de aplicativo. Estes são os diretórios com a extensão `.app` que contêm seu código, dados, arquivos de configuração e um manifesto que usa o iPhone para saber mais sobre o seu aplicativo.

O processo de transformar um executável .NET em um aplicativo geralmente é controlado pelo comando `mtouch`, uma ferramenta que se integra a muitas das etapas necessárias para transformar o aplicativo em um pacote. Essa ferramenta também é usada para iniciar o aplicativo no simulador e implantar o software em um dispositivo iPhone ou iPod Touch real.


## <a name="detailed-instructions"></a>Instruções detalhadas

Verifique nossa página de manual do [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) com todos os possíveis usos da ferramenta mtouch.

## <a name="installation"></a>Instalação

Em um Mac, o `mtouch` é fornecido com o Xamarin.iOS. Ele pode ser encontrado no seguinte diretório:

**/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**

Para facilitar o uso do `mtouch`, adicione o diretório pai dele à variável de ambiente `PATH` do seu sistema.  

Por exemplo, para fazer isso no Bash, adicione a seguinte linha ao final do arquivo **~/.bash_profile**:

```bash
export PATH=$PATH:/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin
```

> [!WARNING]
> Para usar o `mtouch`, não dependa da existência do **/Developer/MonoTouch/usr/bin**, um link simbólico que aponta para **/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**. Esse link simbólico existe apenas para manter a compatibilidade com versões mais antigas do MonoTouch que não foram instaladas em **/Library/Frameworks/...** e pode desaparecer em uma versão futura.

## <a name="building"></a>Compilação

O comando `mtouch` pode compilar o código de três maneiras diferentes:

-  Compilar para testes do simulador.
-  Compilar para implantação no dispositivo.
-  Implante o executável no dispositivo.


### <a name="building-for-the-simulator"></a>Compilar para o simulador

Quando você começar, o cenário de uso mais comum será testar o aplicativo no simulador, de modo que você usará o `mtouch -sim` para compilar o código em um pacote do simulador. Isso é feito da seguinte maneira:

```bash
$ mtouch -sim Hello.app hello.exe
```

### <a name="building-for-the-device"></a>Compilar para o dispositivo

Para compilar software para o dispositivo, você compilará seu aplicativo usando a opção `mtouch -dev` e, além disso, você precisará fornecer o nome do certificado usado para assinar seu aplicativo. O exemplo a seguir mostra como o aplicativo é compilado para o dispositivo:

```bash
$ mtouch -dev -c "iPhone Developer: Miguel de Icaza" foo.exe
```

Nesse caso específico, estamos usando o certificado "iPhone Developer: Miguel de Icaza" para assinar o aplicativo. Essa etapa é muito importante, ou o dispositivo físico não aceitará carregar o aplicativo.

 <a name="Running_your_Application" />


## <a name="running-your-application"></a>Executar o aplicativo


### <a name="launching-on-the-simulator"></a>Iniciar no simulador

Iniciar o simulador é muito simples, uma vez que você tem um pacote de aplicativo:

```bash
$ mtouch --sdkroot /Applications/Xcode.app -launchsim Hello.app 
```

Se o sinalizador `--sdkroot` não está definido, ele assume o caminho xcode-select por padrão e isso resulta no aviso a seguir:

> Por exemplo: aviso MT0061: Nenhum Xcode.app especificado (usando --sdkroot), usando o sistema do Xcode conforme relatado por 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

A linha de comando acima produzirá uma saída como esta:

```bash
Launching application
Application launched
PID: 98460
Press enter to terminate the application
```



É altamente recomendável que você também mantenha um log dos arquivos de saída padrão e de erro padrão para ajudar na depuração. A saída de `Console.WriteLine` vai para `stdout`, enquanto a saída de `Console.Error.WriteLine` e eventuais outras mensagens de erro de tempo de execução vai para `stderr`.

Para fazer isso, use os sinalizadores `--stdout` e `--stderr`:

```bash
../../tools/mtouch/mtouch --launchsim=Hello.app --stdout=output --stderr=error
```

Se o aplicativo falhar, você poderá ver a saída e o erro para diagnosticar o problema.


### <a name="deploying-to-a-device"></a>Implantação em um dispositivo

Para implantar seu dispositivo, você precisa provisionar seu dispositivo conforme descrito no documento [Gerenciando dispositivos](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/00-About_the_iOS_Application_Development_Workflow/introduction.html) da Apple. Depois que o dispositivo tiver sido configurado corretamente, você poderá usar o comando mtouch para implantar um ".app" compilado em seu dispositivo. Você faz isso usando este comando:

```bash
$ mtouch —sdkroot /Applications/Xcode.app -installdev=MyApp.app
```

Se o sinalizador `--sdkroot` não está definido, ele assume o caminho xcode-select por padrão e isso resulta no aviso a seguir:

> Por exemplo: aviso MT0061: Nenhum Xcode.app especificado (usando --sdkroot), usando o sistema do Xcode conforme relatado por 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

Essas etapas são normalmente executadas pelo Visual Studio para Mac.

## <a name="reference"></a>Referência

Consulte a página de manual do [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) para obter detalhes sobre as outras opções de linha de comando.



## <a name="related-links"></a>Links relacionados

- [mtouch(1)](http://iosapi.xamarin.com/?link=man%3amtouch(1))
