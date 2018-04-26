---
title: C# 6 novos recursos visão geral
description: A versão mais recente da linguagem c# – versão 6 – continua a evoluir o idioma para ter menos clichê, mais clareza e mais consistência. Sintaxe de inicialização de limpeza, a capacidade de usar aguardar em blocos catch/finally e null-condicional? operador são especialmente úteis.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.technology: xamarin-cross-platform
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: de6fdab62a57dddb6fcf48302b7ff9f5ec2bc9a2
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="c-6-new-features-overview"></a>C# 6 novos recursos visão geral

_A versão mais recente da linguagem c# – versão 6 – continua a evoluir o idioma para ter menos clichê, mais clareza e mais consistência. Sintaxe de inicialização de limpeza, a capacidade de usar aguardar em blocos catch/finally e null-condicional? operador são especialmente úteis._

Este documento apresenta os novos recursos do c# 6. Ele é totalmente suportado pelo compilador mono e desenvolvedores podem começar a usar os novos recursos em todas as plataformas de destino Xamarin.

Este artigo inclui breves trechos do código c# 6 que ilustram o uso básico.
O aplicativo de exemplo é um programa de linha de comando que é executado em todas as plataformas de destino do Xamarin e emprega diversos recursos.


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Quais são as novidades no c# 6, pelo [University Xamarin](https://university.xamarin.com/)**


## <a name="development-environment"></a>Ambiente de desenvolvimento

### <a name="mac"></a>Mac

* **O Visual Studio para Mac** tem suporte para o c# 6: você pode criar e compilar aplicativos Xamarin usando o c# 6 recursos.
  Leia mais sobre [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/).

### <a name="windows"></a>Windows

* **O Visual Studio 2015 e 2017** e superior têm suporte completo para o c# 6. Versões anteriores do Visual Studio não dará suporte c# 6.

* **Xamarin Studio para Windows** não oferece suporte a recursos do c# 6 no editor.



## <a name="compiler"></a>Compilador

O compilador Mono c# 6 está incluído no Mono 4.0 e posterior, que é [disponível gratuitamente para download](http://www.mono-project.com/download/).
O Visual Studio para Mac atualiza automaticamente a instalação Mono em seu sistema.

Os usuários do Windows devem ter [Visual Studio 2015 ou 2017 ^](https://www.visualstudio.com/) instalado para compilar o código c# 6 (mesmo se você escolher o Xamarin Studio para Windows como seu IDE).

^ ou *[Microsoft Build Tools 2015](http://www.microsoft.com/download/details.aspx?id=48159)* para o comando de linha compilação ou servidores de compilação, por exemplo.

## <a name="using-c-6"></a>Usando o c# 6

O compilador c# 6 é usado em todas as versões recentes do Visual Studio para Mac.
As que usam os compiladores de linha de comando devem confirmar que `mcs --version` retorna 4.0 ou superior.
O Visual Studio para usuários do Mac pode verificar se eles tiverem Mono 4 (ou mais recente) instaladas consultando **sobre o Visual Studio para Mac > Visual Studio para Mac > Mostrar detalhes**.

## <a name="less-boilerplate"></a>Menos clichê
### <a name="using-static"></a>usando estático
Enumerações e algumas classes como `System.Math`, são principalmente os detentores de valores estáticos e funções. No c# 6, você pode importar todos os membros estáticos de um tipo com um único `using static` instrução. Compare uma função trigonométrica típica em c# 5 e 6 do c#:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` não publique `const` campos, como `Math.PI` e `Math.E`, diretamente acessíveis:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>usando estático com métodos de extensão

O `using static` recurso funciona um pouco diferente com métodos de extensão. Embora os métodos de extensão são escritos usando `static`, eles não fazem sentido sem uma instância no qual operar. Portanto, quando `using static` é usado com um tipo que define os métodos de extensão, os métodos de extensão se tornar disponíveis no seu tipo de destino (o método `this` tipo). Por exemplo, `using static System.Linq.Enumerable` podem ser usados para estender a API de `IEnumerable<T>` objetos sem colocar em todos os tipos LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

O exemplo anterior demonstra a diferença no comportamento: o método de extensão `Enumerable.Where` está associado com a matriz, enquanto o método estático `String.Join` pode ser chamado sem referência para o `String` tipo.

### <a name="nameof-expressions"></a>nameof expressões
Às vezes, você quer se referir ao nome que você deu uma variável ou um campo. No c# 6, `nameof(someVariableOrFieldOrType)` retornará a cadeia de caracteres `"someVariableOrFieldOrType"`. Por exemplo, ao gerar um `ArgumentException` muito provavelmente deseja nomear o argumento é inválido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

A principal vantagem de `nameof` expressões é que eles são tipo verificado e são compatíveis com a ferramenta da plataforma de refatoração. A verificação de tipo de `nameof` expressions é particularmente bem-vindo em situações em que um `string` é usada para associar dinamicamente os tipos. Por exemplo, no iOS um `string` é usado para especificar o tipo usado para criar um protótipo `UITableViewCell` objetos em um `UITableView`. `nameof` pode garantir que essa associação não falhará devido a um erro de ortografia ou de refatoração ineficiente:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Embora você pode passar um nome qualificado para `nameof`, apenas o elemento final (após a última `.`) será retornado. Por exemplo, você pode adicionar uma associação de dados em xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

As duas chamadas para `SetBinding` estiver passando valores idênticos: `nameof(ReactiveType.StringField)` é `"StringField"`, não `"ReactiveType.StringField"` como esperado inicialmente.

## <a name="null-conditional-operator"></a>Operador condicional NULL
Atualizações anteriores para c# introduziu os conceitos de tipos anuláveis e o operador de união null `??` para reduzir a quantidade de código clichê ao lidar com valores anuláveis. C# 6 continua esse tema com o operador"condicionais nulos" `?.`. Quando usado em um objeto no lado direito de uma expressão, o operador condicional null retorna o valor do membro, se o objeto não é `null` e `null` contrário:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Ambos `length0` e `length1` são inferidos para ser do tipo `int?`)

A última linha mostra o exemplo anterior a `?` operador condicional null em combinação com o `??` operador de união de null. Retorna o novo operador condicional null c# 6 `null` no 2º elemento na matriz, no ponto em que o operador de união de null é acionado e fornece um 0 para o `lengths` de matriz (se é apropriado ou não é, logicamente, problema específico).

O operador condicional null enormemente deve reduzir a quantidade de boilerplate verificação null necessário em muitos, muitos aplicativos.

Há algumas limitações sobre o operador condicional null devido a ambiguidades. Não é possível seguir imediatamente um `?` com uma lista de argumentos entre parênteses, como você pode esperar com um delegado:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

No entanto, `Invoke` pode ser usado para separar o `?` da lista de argumentos e ainda é marcada por um `null`-bloco de texto clichê de verificação:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres
O `String.Format` função tradicionalmente usou índices como espaços reservados na cadeia de formato, por exemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). Obviamente, adicionando um novo valor tem sempre envolvidos uma tarefa pouco irritante de contagem de argumentos, renumeração espaços reservados e inserindo um novo argumento na sequência correta na lista de argumentos.

Novo recurso de interpolação de cadeia de caracteres c# 6 aprimora significativamente `String.Format`. Agora, você pode nomear diretamente variáveis em uma cadeia de caracteres prefixados com um `$`. Por exemplo:

```csharp
$"Expected: {expected} Received: {received}."
```

Variáveis são, obviamente, verificadas e uma variável incorreta ou não disponível causará um erro do compilador.

Os espaços reservados não precisam ser simples, podem ser qualquer expressão. Dentro desses espaços reservados, você pode usar aspas *sem* escape as aspas. Por exemplo, observe o `"s"` no seguinte:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

O alinhamento e a sintaxe de formatação do dá suporte a interpolação de cadeia de caracteres `String.Format`. Assim como você escreveu anteriormente `{index, alignment:format}`, no c# 6 gravar `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
resultados em:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolação de cadeia de caracteres é açúcar sintático para `String.Format`: ele não pode ser usado com `@""` literais de cadeia de caracteres e não é compatível com `const`, mesmo que nenhum espaço reservado é usado:

```csharp
const string s = $"Foo"; //Error : const requires value
```

No use-o caso comum de criação de argumentos de função com uma interpolação de cadeia de caracteres, você ainda precisa ter cuidado em relação a problemas de cultura, codificação e saída. Consultas SQL e URL são, obviamente, essenciais para limpar. Assim como acontece com `String.Format`, usa interpolação de cadeia de caracteres de `CultureInfo.CurrentCulture`. Usando `CultureInfo.InvariantCulture` é um pouco mais prolixo:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inicialização

C# 6 fornece várias maneiras concisas para especificar membros, propriedades e campos.

### <a name="auto-property-initialization"></a>Inicialização automática de propriedade

Agora podem ser inicializadas automaticamente propriedades da mesma maneira concisa como campos. Autopropriedades imutáveis podem ser gravados com apenas um getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

No construtor, você pode definir o valor de uma propriedade de auto somente getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Essa inicialização das propriedades automática é um recurso de economia de espaço geral e um benefício para os desenvolvedores que desejam enfatizar imutabilidade em seus objetos.

### <a name="index-initializers"></a>Inicializadores de índice

C# 6 apresenta inicializadores de índice, que permitem que você defina a chave e o valor em tipos que têm um indexador. Normalmente, isso é para `Dictionary`-estilo de estruturas de dados:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Membros da função bodied de expressão

Funções de lambda tem várias vantagens, um dos quais é simplesmente economizar espaço. Da mesma forma, membros de classe bodied expressão permitem que pequenas funções ser expresso de forma um pouco mais sucinta do que era possível nas versões anteriores do c# 6.

Membros da função bodied expressão usam a sintaxe de seta lambda em vez da sintaxe de bloco tradicional:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Observe que a sintaxe de seta de lambda não utilize explícito `return`. Para funções que retornam `void`, a expressão também deve ser uma instrução:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Membros bodied expressão ainda estão sujeitos a regra que `async` tem suporte para métodos, mas não as propriedades:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceções

Não há nenhum duas maneiras sobre ele: manipulação de exceção é difícil de acertar. Novos recursos no c# 6 tornam tratamento de exceção mais flexível e consistente.

### <a name="exception-filters"></a>Filtros de exceção

Por definição, as exceções ocorrem em circunstâncias incomuns e pode ser muito difícil motivo e código sobre *todas as* as formas pode ocorrer uma exceção de um tipo específico. C# 6 apresenta a capacidade de proteger um manipulador de execução com um filtro avaliada em tempo de execução. Isso é feito adicionando uma `when (bool)` padrão depois que o normal `catch(ExceptionType)` declaração. A seguir, um filtro distingue relacionadas a um erro de análise de `date` parâmetro em vez de outros erros de análise.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>aguardar em catch... finally...

O `async` recursos introduzidos no c# 5 foram decisivo para o idioma. No c# 5, `await` não foi permitida no `catch` e `finally` bloqueia um incômodo, o valor da `async/await` recurso. C# 6 remove essa limitação, permitindo assíncronos resultados a ser aguardado consistentemente por meio do programa, como mostrado no trecho a seguir:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Resumo

A linguagem c# continua a evoluir para tornar os desenvolvedores mais produtivos durante também promove práticas recomendadas e ferramentas de suporte. Este documento forneceu uma visão geral dos novos recursos de linguagem no c# 6 e brevemente demonstrou como eles são usados.

## <a name="related-links"></a>Links relacionados

- [Novos recursos de linguagem no c# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [Usando o c# 6 pasta de trabalho](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)
