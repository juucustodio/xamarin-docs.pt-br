---
title: Retornos de chamada no Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117132"
---
# <a name="callbacks-on-android"></a>Retornos de chamada no Android

Chamar ao Java do C# é um pouco uma *negócio arriscado*. Isto significa que há um *padrão* para retornos de chamada de C# para Java; no entanto, é mais complicado do que gostaríamos.

Abordaremos as três opções para fazer retornos de chamada que façam mais sentido para Java:

- Classes abstratas
- Interfaces
- Métodos virtuais

## <a name="abstract-classes"></a>Classes abstratas

Essa é a rota mais fácil para retornos de chamada, portanto, recomendo usar `abstract` se você estiver apenas tentando obter um retorno de chamada trabalhar de forma mais simples.

Vamos começar com um C# classe gostaríamos de Java para implementar:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Aqui estão os detalhes para fazer isso funcionar:

- `[Register]` gera um nome de pacote BOM em Java – você receberá um nome de pacote gerado automaticamente sem ele.
- Criando subclasses `Java.Lang.Object` sinais à inserção de .NET para executar a classe por meio do gerador de Java do xamarin. Android.
- Construtor vazio: é o que você deseja usar do código Java.
- `(IntPtr, JniHandleOwnership)` construtor: é o que xamarin. Android usará para criar o C#-equivalentes de objetos Java.
- `[Export]` sinaliza o xamarin. Android para expor o método para Java. Também podemos alterar o nome do método, uma vez que o mundo Java gosta de usar os métodos de letras minúsculas.

Próxima vamos fazer um C# método para testar o cenário:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` pode ser qualquer classe para testar isso, desde que ele seja um `Java.Lang.Object`.

Agora, execute a inserção de .NET em seu assembly .NET para gerar um AAR. Consulte a [guia de Introdução](~/tools/dotnet-embedding/get-started/java/android.md) para obter detalhes.

Depois de importar o arquivo AAR para Android Studio, vamos escrever um teste de unidade:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
Portanto, podemos:

- Implementado o `AbstractClass` em Java com um tipo anônimo
- Verificado se nossa instância retorna `"Java"` do Java
- Verificado se nossa instância retorna `"Java"` doC#
- Adicionado `throws Throwable`, já que C# construtores estão marcados com `throws`

Se, executamos este teste de unidade como-está, ele falhará com um erro, como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

O que está faltando aqui está um `Invoker` tipo. Isso é uma subclasse de `AbstractClass` que encaminha C# chamadas para Java. Se inserir um objeto Java a C# world e o equivalente a C# tipo é abstrato e, em seguida, xamarin. Android procura automaticamente por um C# tipo com o sufixo `Invoker` para uso dentro de C# código.

Xamarin. Android usa esse `Invoker` padrão para projetos de associação do Java entre outras coisas.

Aqui está nossa implementação de `AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Há um pouco acontecendo aqui, podemos:

- Adicionada uma classe com o sufixo `Invoker` que pode efetuar subclasses `AbstractClass`
- Adicionado `class_ref` para manter a referência JNI para a classe Java que pode efetuar subclasses nosso C# classe
- Adicionado `id_gettext` para manter a referência JNI para Java `getText` método
- Incluído um `(IntPtr, JniHandleOwnership)` construtor
- Implementado `ThresholdType` e `ThresholdClass` como um requisito para xamarin. Android para saber detalhes sobre o `Invoker`
- `GetText` necessário para pesquisar o Java `getText` método com a assinatura JNI apropriado e chamá-lo
- `Dispose` é necessário apenas para eliminar a referência para `class_ref`

Depois de adicionar essa classe e gerar um novo AAR, nossa unidade teste passa. Como você pode ver esse padrão para retornos de chamada não é *ideal*, mas poderá ser feito.

Para obter detalhes sobre a interoperabilidade de Java, consulte o incrível [documentação do xamarin. Android](~/android/platform/java-integration/working-with-jni.md) sobre esse assunto.

## <a name="interfaces"></a>Interfaces

Interfaces são muito parecidas como classes abstratas, exceto um detalhe: xamarin. Android não gera Java para eles. Isso ocorre porque antes da inserção de .NET, não há muitos cenários em que o Java implementaria um C# interface.

Vamos supor que temos o seguinte C# interface:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` sinaliza a incorporação do .NET que essa é uma interface de xamarin. Android, mas caso contrário, isso é exatamente o mesmo que um `abstract` classe.

Uma vez que o xamarin. Android no momento, não irá gerar o código Java para essa interface, adicione o Java a seguir para seu C# projeto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Você pode colocar o arquivo em qualquer lugar, mas certifique-se de definir sua ação de compilação como `AndroidJavaSource`. Isso sinalizará a incorporação do .NET para copiá-lo para o diretório apropriado que é compilado para o seu arquivo AAR.

Em seguida, o `Invoker` implementação será exatamente o mesmo:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Depois de gerar um arquivo AAR, no Android Studio, poderíamos escrever a seguinte unidade de transmissão de teste:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Métodos virtuais

Substituindo um `virtual` em Java, é possível, mas não é uma ótima experiência.

Vamos supor que você tenha o seguinte C# classe:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Se você tiver seguido as `abstract` exemplo de classe acima, ele funcionaria, exceto um detalhe: _xamarin. Android não pesquisar a `Invoker`_ .

Para corrigir esse problema, modifique o C# à classe `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Isso não é ideal, mas ele obtém esse cenário funcione. Xamarin. Android selecionará o `VirtualClassInvoker` e pode usar o Java `@Override` no método.

## <a name="callbacks-in-the-future"></a>Retornos de chamada no futuro

Há algumas coisas que pudemos para melhorar a esses cenários:

1. `throws Throwable` no C# construtores é fixa nisso [PR](https://github.com/xamarin/java.interop/pull/170).
1. Fazer com que o gerador de Java no xamarin. Android dão suporte a interfaces.
    - Isso remove a necessidade de adicionar o arquivo de origem de Java com uma ação de build `AndroidJavaSource`.
1. Faça uma maneira para xamarin. Android carregar um `Invoker` para aulas virtuais.
    - Isso remove a necessidade de marcar a classe em nossa `virtual` exemplo `abstract`.
1. Gerar `Invoker` classes automaticamente para a inserção do .NET
    - Isso vai ser complicado, mas poderá ser feito. Xamarin. Android já está fazendo algo semelhante a isto para projetos de associação de Java.

Há muito trabalho a ser feito aqui, mas esses aprimoramentos à inserção de .NET são possíveis.

## <a name="further-reading"></a>Leitura adicional

* [Guia de Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Pesquisa preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
