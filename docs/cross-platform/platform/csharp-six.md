---
title: C#6 Visão geral dos novos recursos
description: A versão 6 do C# idioma continua a desenvolver a linguagem para ter menos código clichê, maior clareza e mais consistência. Sintaxe de inicialização mais limpa, a capacidade de usar Await em blocos catch/finally e o NULL-Conditional? o operador é especialmente útil.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1db7ee95ec261739463fa2584f4acf493ac71217
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014828"
---
# <a name="c-6-new-features-overview"></a>C#6 Visão geral dos novos recursos

_A versão 6 do C# idioma continua a desenvolver a linguagem para ter menos código clichê, maior clareza e mais consistência. Sintaxe de inicialização mais limpa, a capacidade de usar Await em blocos catch/finally e o NULL-Conditional? o operador é especialmente útil._

> [!NOTE]
> Para obter informações sobre a versão mais recente C# do idioma – versão 7 – consulte o artigo [novidades em C# 7,0](/dotnet/csharp/whats-new/csharp-7)

Este documento apresenta os novos recursos do C# 6. Ele é totalmente suportado pelo compilador mono e os desenvolvedores podem começar a usar os novos recursos em todas as plataformas de destino do Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**O que há de C# novo em 6 vídeos**

## <a name="using-c-6"></a>Usando C# 6

O C# compilador 6 é usado em todas as versões recentes do Visual Studio para Mac.
Aqueles que usam compiladores de linha de comando devem confirmar que `mcs --version` retorna 4,0 ou superior.
Visual Studio para Mac usuários podem verificar se têm o mono 4 (ou mais recente) instalado consultando **sobre Visual Studio para Mac > Visual Studio para Mac > mostrar detalhes**.

## <a name="less-boilerplate"></a>Menos clichê
### <a name="using-static"></a>usando estático
Enumerações e determinadas classes, como `System.Math`, são principalmente os proprietários de valores estáticos e funções. Em C# 6, você pode importar todos os membros estáticos de um tipo com uma única instrução`using static`. Compare uma função trigonométrica típica em C# 5 e C# 6:

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

`using static` não faz `const` campos públicos, como `Math.PI` e `Math.E`, acessíveis diretamente:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>usando estático com métodos de extensão

O `using static` recurso Opera de maneira um pouco diferente com os métodos de extensão. Embora os métodos de extensão sejam escritos usando `static`, eles não fazem sentido sem uma instância na qual operar. Assim, quando `using static` é usado com um tipo que define métodos de extensão, os métodos de extensão ficam disponíveis em seu tipo de destino (o tipo de `this` do método). Por exemplo, `using static System.Linq.Enumerable` pode ser usado para estender a API de objetos `IEnumerable<T>` sem trazer todos os tipos de LINQ:

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

O exemplo anterior demonstra a diferença no comportamento: o método de extensão `Enumerable.Where` está associado à matriz, enquanto o método estático `String.Join` pode ser chamado sem referência ao tipo de `String`.

### <a name="nameof-expressions"></a>Expressões nameof
Às vezes, você deseja fazer referência ao nome em que você atribuiu uma variável ou campo. Em C# 6,`nameof(someVariableOrFieldOrType)`retornará a cadeia de caracteres`"someVariableOrFieldOrType"`. Por exemplo, ao lançar um `ArgumentException` é muito provável que você queira nomear qual argumento é inválido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

A principal vantagem das expressões `nameof` é que elas são marcadas e são compatíveis com a refatoração habilitada para ferramentas. O tipo de verificação de expressões `nameof` é particularmente bem-vindo em situações em que uma `string` é usada para associar tipos dinamicamente. Por exemplo, no iOS, um `string` é usado para especificar o tipo usado para protótipos de `UITableViewCell` objetos em um `UITableView`. `nameof` pode garantir que essa associação não falhe devido a uma refatoração incorreta ou impreciso:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Embora você possa passar um nome qualificado para `nameof`, somente o elemento final (após a última `.`) é retornado. Por exemplo, você pode adicionar uma associação de dados no Xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

As duas chamadas para `SetBinding` estão passando valores idênticos: `nameof(ReactiveType.StringField)` é `"StringField"`, não `"ReactiveType.StringField"` como você pode esperar inicialmente.

## <a name="null-conditional-operator"></a>Operador NULL-condicional
As atualizações anteriores C# introduziam os conceitos de tipos anuláveis e o operador de união nula`??`para reduzir a quantidade de código clichê ao manipular valores anuláveis. C#6 continua este tema com o`?.`"operador NULL-Conditional". Quando usado em um objeto no lado direito de uma expressão, o operador NULL-Conditional retornará o valor do membro se o objeto não for `null` e `null` caso contrário:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(`length0` e `length1` são inferidos para serem do tipo `int?`)

A última linha no exemplo anterior mostra o `?` operador condicional nulo em combinação com o operador de União `??` nulo. O novo C# operador NULL-Conditional nulo retorna`null`no segundo elemento na matriz, em que ponto o operador de União nula é ativado e fornece um 0 para a matriz`lengths`(seja apropriado ou não, é claro, específico do problema).

O operador NULL-Conditional deve reduzir enormemente a quantidade de verificação de nulos clichê necessária em muitos aplicativos.

Há algumas limitações no operador NULL-Conditional devido a ambiguidades. Você não pode seguir imediatamente um `?` com uma lista de argumentos entre parênteses, como você pode esperar fazer com um delegado:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

No entanto, `Invoke` pode ser usado para separar o `?` da lista de argumentos e ainda é uma melhoria marcada em um bloco de texto clichê de `null`de verificação:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres
A função `String.Format` tradicionalmente usava índices como espaços reservados na cadeia de caracteres de formato, por exemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). É claro que adicionar um novo valor sempre envolvia uma pequena tarefa incômodo de contar argumentos, renumerar espaços reservados e inserir o novo argumento na sequência direita na lista de argumentos.

C#o novo recurso de interpolação de cadeia de caracteres de 6 melhora muito na `String.Format`. Agora, você pode nomear variáveis diretamente em uma cadeia de caracteres prefixada com um `$`. Por exemplo:

```csharp
$"Expected: {expected} Received: {received}."
```

As variáveis são, é claro, marcada e uma variável incorreta ou não disponível causará um erro do compilador.

Os espaços reservados não precisam ser variáveis simples, podem ser qualquer expressão. Nesses espaços reservados, você pode usar aspas *sem* escapar dessas cotações. Por exemplo, observe o `"s"` no seguinte:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

A interpolação de cadeia de caracteres dá suporte à sintaxe de alinhamento e formatação de `String.Format`. Assim como você escreveu anteriormente `{index, alignment:format}`, em C# 6, você escreve`{placeholder, alignment:format}`:

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

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

A interpolação de cadeia de caracteres é uma simplificação sintática para `String.Format`: ela não pode ser usada com literais de cadeia de caracteres `@""` e não é compatível com `const`, mesmo que nenhum espaço reservado seja usado:

```csharp
const string s = $"Foo"; //Error : const requires value
```

No caso de uso comum de criar argumentos de função com interpolação de cadeia de caracteres, você ainda precisa ter cuidado com problemas de escape, codificação e cultura. As consultas SQL e URL são, é claro, críticas para a limpeza. Assim como ocorre com `String.Format`, a interpolação de cadeia de caracteres usa o `CultureInfo.CurrentCulture`. Usar `CultureInfo.InvariantCulture` é um pouco mais de palavras:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inicialização

C#6 fornece várias maneiras concisas de especificar propriedades, campos e membros.

### <a name="auto-property-initialization"></a>Inicialização de propriedade automática

As propriedades automáticas agora podem ser inicializadas da mesma maneira concisa que os campos. As propriedades automáticas imutáveis podem ser gravadas com apenas um getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

No construtor, você pode definir o valor de uma propriedade automática somente getter:

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

Essa inicialização de propriedades automáticas é um recurso de economia de espaço geral e um benefício para os desenvolvedores que desejam enfatizar a imutabilidade em seus objetos.

### <a name="index-initializers"></a>Inicializadores de índice

C#6 apresenta inicializadores de índice, que permitem definir a chave e o valor em tipos que têm um indexador. Normalmente, isso é para estruturas de dados de estilo `Dictionary`:

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

### <a name="expression-bodied-function-members"></a>Membros da função Expression-apto para

As funções lambda têm vários benefícios, um dos quais está simplesmente economizando espaço. Da mesma forma, os membros da classe Expression-apto para permitem que pequenas funções sejam expressas um pouco mais sucintos do C# que era possível nas versões anteriores do 6.

Os membros da função Expression-apto para usam a sintaxe de seta lambda em vez da sintaxe de bloco tradicional:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Observe que a sintaxe da seta lambda não usa um `return`explícito. Para funções que retornam `void`, a expressão também deve ser uma instrução:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Os membros do Expression-apto para ainda estão sujeitos à regra que `async` tem suporte para métodos, mas não para propriedades:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceções

Não há duas maneiras de fazer isso: a manipulação de exceções é difícil de ser adequada. Os novos recursos C# do 6 tornam o tratamento de exceções mais flexível e consistente.

### <a name="exception-filters"></a>Filtros de exceção

Por definição, as exceções ocorrem em circunstâncias incomuns e pode ser muito difícil de motivo e código sobre *todas* as maneiras que uma exceção de um tipo específico pode ocorrer. C#6 introduz a capacidade de proteger um manipulador de execução com um filtro avaliado por tempo de execução. Isso é feito adicionando um padrão de `when (bool)` após a declaração de `catch(ExceptionType)` normal. A seguir, um filtro distingue um erro de análise relacionado ao parâmetro `date` em oposição a outros erros de análise.

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

### <a name="await-in-catchfinally"></a>aguardar no catch... Por fim...

Os recursos de `async` introduzidos em C# 5 foram um alterador de jogo para a linguagem. Em C# 5,`await`não foi permitido em blocos de`catch`e de`finally`, um incômodo dado o valor do recurso de`async/await`. C#6 remove essa limitação, permitindo que os resultados assíncronos sejam esperados consistentemente por meio do programa, conforme mostrado no trecho a seguir:

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

O C# idioma continua a evoluir para tornar os desenvolvedores mais produtivos e também promover boas práticas e ferramentas de suporte. Este documento forneceu uma visão geral dos novos recursos de linguagem C# do 6 e demonstrou brevemente como eles são usados.

## <a name="related-links"></a>Links relacionados

- [Novos recursos de linguagem C# no 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
