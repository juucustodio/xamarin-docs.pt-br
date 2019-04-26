---
title: Usando associações do xamarin. Mac para aplicativos de Console
description: Crie um aplicativo de console "sem periféricos" usando o xamarin. Mac para acessar as APIs de macOS nativa.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378270"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Associações do xamarin. MAC em aplicativos de console

Existem alguns cenários em que você deseja usar algumas das APIs nativas da Apple em c# para criar um aplicativo sem periféricos &ndash; que não tem uma interface do usuário &ndash; usando a linguagem c#.

Os modelos de projeto para aplicativos do Mac incluem uma chamada para `NSApplication.Init()` seguido por uma chamada para `NSApplication.Main(args)`, ele geralmente se parece com isso:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

A chamada para `Init` prepara o tempo de execução do xamarin. Mac, a chamada para `Main(args)` inicia o Cocoa aplicativo loop principal, que prepara o aplicativo para receber eventos de teclado e mouse e mostrar a janela principal do seu aplicativo.   A chamada para `Main` também tenta localizar recursos Cocoa, prepare uma janela toplevel e espera que o programa seja parte de um pacote de aplicativo (programas distribuídos em um diretório com o `.app` extensão e um layout muito específico).

Não é necessário um usuário de aplicativos sem periféricos de interface e não precisa executar como parte de um pacote de aplicativo.

## <a name="creating-the-console-app"></a>Criando o aplicativo de console

Portanto, é melhor iniciar com um tipo de projeto de Console .NET regular.

Você precisa fazer algumas coisas:

- Crie um projeto vazio.
- Referência de biblioteca do xamarin.
- Traga a dependência não gerenciada ao seu projeto.

Essas etapas são explicadas em mais detalhes abaixo:

### <a name="create-an-empty-console-project"></a>Criar um projeto de Console vazio

Criar um novo projeto de Console .NET, certifique-se de que ele é o .NET e não o .NET Core, como xamarin não é executado em tempo de execução do .NET Core, só é executado com o tempo de execução Mono.

### <a name="reference-the-xamarinmac-library"></a>Fazer referência à biblioteca do xamarin. Mac

Para compilar seu código, você desejará fazer referência a `Xamarin.Mac.dll` assembly deste diretório: `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Para fazer isso, vá para as referências do projeto, selecione a **Assembly .NET** guia e, em seguida, clique no **procurar** botão para localizar o arquivo no sistema de arquivos.  Navegue até o caminho acima e, em seguida, selecione a **xamarin** desse diretório.

Isso lhe dará acesso às APIs Cocoa em tempo de compilação.   Neste ponto, você pode adicionar `using AppKit` na parte superior do seu arquivo e chame o `NSApplication.Init()` método.   Há somente mais uma etapa antes de executar seu aplicativo.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Colocar a biblioteca de suporte não gerenciado em seu projeto

Antes do aplicativo será executado, você precisará colocar o `Xamarin.Mac` biblioteca de suporte para o seu projeto.   Para fazer isso, adicione um novo arquivo ao seu projeto (nas opções de projeto, selecione **Add**e então **Add Existing File**) e navegue até esse diretório:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Aqui, selecione o arquivo **libxammac.dylib**.   Você terá a opção de copiar, vincular ou mover.   Eu pessoalmente gosto de vinculação, mas copiando funciona bem.    Em seguida, você precisa selecionar o arquivo e no painel de propriedade (selecione **exibição > painéis > propriedades** se o painel de propriedade não estiver visível), vá para o **compilar** seção e defina o **copiar para saída Diretório** definir como **copiar se mais recente**.

Agora você pode executar seu aplicativo xamarin. Mac.

O resultado no diretório bin terá esta aparência:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Para executar esse aplicativo, você precisará de todos os arquivos no mesmo diretório.

## <a name="building-a-standalone-application-for-distribution"></a>Criando um aplicativo autônomo para distribuição

Você talvez queira distribuir um único executável para seus usuários.  Para fazer isso, você pode usar o `mkbundle` ferramenta para transformar os diversos arquivos em um executável autônomo.

Primeiro, certifique-se de que seu aplicativo é compilado e executado.   Quando estiver satisfeito com os resultados, você pode executar o comando a seguir na linha de comando:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

Na invocação de linha de comando acima, a opção `-o` é usado para especificar a saída gerada, nesse caso, passamos `/tmp/consoleapp`.   Agora, é um aplicativo autônomo que você pode distribuir e não tem dependências externas no Mono ou xamarin. Mac, é um executável totalmente autocontido.

A linha de comando especificada manualmente a **Machine. config** arquivo a ser usado e um arquivo de configuração de mapeamento de biblioteca de todo o sistema.   Eles não são necessários para todos os aplicativos, mas é conveniente para agrupá-los, pois eles serão usados quando você usa mais recursos do .NET

## <a name="project-less-builds"></a>Builds de projetos menores

Não é necessário um projeto completo para criar um aplicativo autocontido do xamarin. Mac, você também pode usar makefiles simples do Unix para realizar o trabalho.   O exemplo a seguir mostra como você pode configurar um makefile para um aplicativo de linha de comando simples:

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

As opções acima `Makefile` fornece três destinos:

- `make` criará o programa
- `make run` compilará e executará o programa no diretório atual
- `make bundle` criará um executável autônomo
