---
title: Usando as associações do Xamarin. Mac para aplicativos de console
description: Crie um aplicativo de console sem periféricos usando o Xamarin. Mac para acessar as APIs nativas do macOS.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769889"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Associações do Xamarin. Mac em aplicativos de console

Há alguns cenários em que você deseja usar algumas APIs nativas da Apple no C# para criar um aplicativo &ndash; sem periféricos que não tenha uma interface &ndash; do usuário C#usando o.

Os modelos de projeto para aplicativos Mac incluem uma chamada `NSApplication.Init()` para seguida de uma chamada `NSApplication.Main(args)`para, geralmente é semelhante ao seguinte:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

A chamada para `Init` prepara o tempo de execução do Xamarin. Mac, a `Main(args)` chamada para inicia o loop principal do aplicativo Cocoa, que prepara o aplicativo para receber eventos de teclado e mouse e mostrar a janela principal do seu aplicativo.   A chamada para `Main` também tentará localizar recursos Cocoa, preparar uma janela TopLevel e espera que o programa faça parte de um pacote de aplicativos (programas distribuídos em um diretório com a `.app` extensão e um layout muito específico).

Aplicativos sem periféricos não precisam de uma interface do usuário e não precisam ser executados como parte de um pacote de aplicativos.

## <a name="creating-the-console-app"></a>Criando o aplicativo de console

Portanto, é melhor começar com um tipo de projeto de console .NET normal.

Você precisa fazer algumas coisas:

- Crie um projeto vazio.
- Referencie a biblioteca Xamarin. Mac. dll.
- Traga a dependência não gerenciada para seu projeto.

Essas etapas são explicadas em mais detalhes abaixo:

### <a name="create-an-empty-console-project"></a>Criar um projeto de console vazio

Crie um novo projeto de console .NET, verifique se ele é .NET e não .NET Core, pois Xamarin. Mac. dll não é executado no tempo de execução do .NET Core, ele só é executado com o tempo de execução mono.

### <a name="reference-the-xamarinmac-library"></a>Referência à biblioteca Xamarin. Mac

Para compilar seu código, você deve fazer referência ao assembly `Xamarin.Mac.dll` a partir deste diretório:`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Para fazer isso, vá para as referências do projeto, selecione a guia **assembly .net** e clique no botão **procurar** para localizar o arquivo no sistema de arquivos.  Navegue até o caminho acima e, em seguida, selecione **Xamarin. Mac. dll** desse diretório.

Isso dará acesso às APIs Cocoa no momento da compilação.   Neste ponto, você pode adicionar `using AppKit` à parte superior do arquivo e chamar o `NSApplication.Init()` método.   Há apenas mais uma etapa antes que você possa executar o aplicativo.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Traga a biblioteca de suporte não gerenciada para seu projeto

Antes que seu aplicativo seja executado, você precisa colocar a `Xamarin.Mac` biblioteca de suporte em seu projeto.   Para fazer isso, adicione um novo arquivo ao seu projeto (em opções de projeto, selecione **Adicionar**e, em seguida, **Adicionar arquivo existente**) e navegue até este diretório:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Aqui, selecione o arquivo **libxammac. dylib**.   Você terá a opção de copiar, vincular ou mover.   Eu gosto pessoalmente de vincular, mas copiar também funciona.    Em seguida, você precisa selecionar o arquivo e, no painel de Propriedades (selecione **exibir > Pads > Propriedades** se o painel de propriedades não estiver visível), vá para a seção **Compilar** e defina a configuração **copiar para diretório de saída** como **copiar se mais recente**.

Agora você pode executar seu aplicativo Xamarin. Mac.

O resultado no diretório bin terá esta aparência:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Para executar esse aplicativo, você precisará de todos esses arquivos no mesmo diretório.

## <a name="building-a-standalone-application-for-distribution"></a>Criando um aplicativo autônomo para distribuição

Talvez você queira distribuir um único executável para seus usuários.  Para fazer isso, você pode usar a `mkbundle` ferramenta para transformar os vários arquivos em um executável independente.

Primeiro, certifique-se de que seu aplicativo é compilado e executado.   Quando estiver satisfeito com os resultados, você poderá executar o seguinte comando na linha de comando:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

Na chamada de linha de comando acima, a opção `-o` é usada para especificar a saída gerada, nesse caso, passamos. `/tmp/consoleapp`   Esse agora é um aplicativo autônomo que você pode distribuir e não tem dependências externas em mono ou Xamarin. Mac, é um executável totalmente independente.

A linha de comando especificou manualmente o arquivo **Machine. config** a ser usado e um arquivo de configuração de mapeamento de biblioteca de todo o sistema.   Eles não são necessários para todos os aplicativos, mas é conveniente agrupá-los, pois eles são usados quando você usa mais recursos do .NET

## <a name="project-less-builds"></a>Compilações sem projeto

Você não precisa de um projeto completo para criar um aplicativo Xamarin. Mac independente, você também pode usar Makers UNIX simples para realizar o trabalho.   O exemplo a seguir mostra como você pode configurar um makefile para um aplicativo simples de linha de comando:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)

bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

O acima `Makefile` fornece três destinos:

- `make`criará o programa
- `make run`compilará e executará o programa no diretório atual
- `make bundle`criará um executável independente
